# Screenshot Organizer - Data Model

## Core Data Structures

### Screenshot Analysis Result
```python
@dataclass
class AnalysisResult:
    """Result of screenshot analysis"""
    file_path: str
    category: str  # code, errors, documentation, design, communication, memes, other
    suggested_filename: str
    description: str
    processing_method: str  # "ocr" or "vision"
    processing_time_ms: float
    confidence_score: float
    extracted_text: Optional[str] = None
    timestamp: datetime = field(default_factory=datetime.now)
    error: Optional[str] = None
```

### OCR Processing Result
```python
@dataclass
class OCRResult:
    """Result from OCR processing"""
    text: str
    word_count: int
    processing_time_ms: float
    sufficient_text: bool  # True if word_count >= threshold
    language: str = "en"
    confidence: float = 0.0
```

### Vision Model Result
```python
@dataclass
class VisionResult:
    """Result from Azure GPT-4o Vision processing"""
    category: str
    description: str
    suggested_filename: str
    processing_time_ms: float
    raw_response: str  # Original JSON from model
    confidence: float = 0.0
```

### Batch Processing Report
```python
@dataclass
class BatchReport:
    """Summary of batch processing"""
    total_files: int
    successful: int
    failed: int
    ocr_processed: int
    vision_processed: int
    total_time_ms: float
    average_time_per_file_ms: float
    categories_count: Dict[str, int]
    errors: List[ProcessingError]
    timestamp: datetime
```

### File Organization Record
```python
@dataclass
class FileOrganization:
    """Record of file organization action"""
    original_path: str
    new_path: str
    category: str
    original_filename: str
    new_filename: str
    moved_at: datetime
    archived: bool = False
    archive_path: Optional[str] = None
```

### Processing Error
```python
@dataclass
class ProcessingError:
    """Error encountered during processing"""
    file_path: str
    error_type: str  # "ocr_failed", "vision_failed", "file_not_found", etc.
    error_message: str
    timestamp: datetime
    recoverable: bool
```

## MCP Protocol Messages

### Request Messages

```python
# Analyze Screenshot Request
{
    "tool": "analyze_screenshot",
    "parameters": {
        "path": str,
        "force_vision": bool
    }
}

# Batch Process Request
{
    "tool": "batch_process",
    "parameters": {
        "folder": str,
        "recursive": bool,
        "max_files": Optional[int]
    }
}

# Organize File Request
{
    "tool": "organize_file",
    "parameters": {
        "source": str,
        "category": str,
        "new_name": str,
        "archive_original": bool
    }
}
```

### Response Messages

```python
# Success Response
{
    "status": "success",
    "tool": str,
    "result": {
        # Tool-specific result data
    },
    "metadata": {
        "processing_time_ms": float,
        "timestamp": str
    }
}

# Error Response
{
    "status": "error",
    "tool": str,
    "error": {
        "type": str,
        "message": str,
        "recoverable": bool
    },
    "metadata": {
        "timestamp": str
    }
}
```

## Configuration Schema

```yaml
# Configuration data model
processing:
  ocr:
    min_words_threshold: int  # Minimum words for classification
    languages: List[str]      # Supported OCR languages
    timeout_ms: int           # OCR timeout
    
  vision:
    model_path: str           # Path to Azure GPT-4o Vision model
    max_tokens: int          # Maximum tokens in response
    temperature: float       # Model temperature
    timeout_ms: int         # Vision processing timeout
    
  classification:
    confidence_threshold: float  # Minimum confidence for category
    custom_patterns: Dict[str, List[str]]  # Additional patterns
    
organization:
  base_path: str            # Base directory for organized files
  categories: List[str]     # Available categories
  filename_pattern: str     # Pattern for new filenames
  handle_duplicates: str    # "increment", "skip", "overwrite"
  archive:
    enabled: bool          # Whether to archive originals
    path: str             # Archive directory
    
api:
  azure:
    endpoint: str              # Azure AI Foundry project endpoint
    api_key: Optional[str]     # API key (or use Azure CLI via 'az login')
    model_deployment: str      # Model deployment name (gpt-4, gpt-4o, gpt-4-turbo)
    max_tokens: int            # Maximum response tokens
    max_context_length: int    # Maximum conversation context (default: 8000)
    temperature: float         # Response temperature (optional)
    
  mcp:
    host: str            # MCP server host
    port: int            # MCP server port
    timeout_ms: int      # Request timeout
    
logging:
  level: str             # DEBUG, INFO, WARNING, ERROR
  file: str             # Log file path
  format: str           # Log format string
  rotate_size: int      # Max log file size before rotation
  rotate_count: int     # Number of log files to keep
```

## Database Schema (SQLite for Metadata)

```sql
-- Processing history table
CREATE TABLE processing_history (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    file_path TEXT NOT NULL,
    original_filename TEXT NOT NULL,
    new_filename TEXT,
    category TEXT NOT NULL,
    processing_method TEXT NOT NULL,
    processing_time_ms REAL NOT NULL,
    extracted_text TEXT,
    description TEXT,
    confidence_score REAL,
    processed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    moved_to TEXT,
    error TEXT
);

-- Batch processing sessions
CREATE TABLE batch_sessions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    session_id TEXT UNIQUE NOT NULL,
    total_files INTEGER NOT NULL,
    successful INTEGER NOT NULL,
    failed INTEGER NOT NULL,
    ocr_processed INTEGER NOT NULL,
    vision_processed INTEGER NOT NULL,
    total_time_ms REAL NOT NULL,
    started_at TIMESTAMP NOT NULL,
    completed_at TIMESTAMP
);

-- File organization records
CREATE TABLE file_organizations (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    original_path TEXT NOT NULL,
    new_path TEXT NOT NULL,
    category TEXT NOT NULL,
    original_filename TEXT NOT NULL,
    new_filename TEXT NOT NULL,
    archived BOOLEAN DEFAULT FALSE,
    archive_path TEXT,
    moved_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Custom classification patterns
CREATE TABLE custom_patterns (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    category TEXT NOT NULL,
    pattern TEXT NOT NULL,
    added_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    active BOOLEAN DEFAULT TRUE
);

-- Performance metrics
CREATE TABLE performance_metrics (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    metric_type TEXT NOT NULL,  -- "ocr", "vision", "classification"
    value REAL NOT NULL,
    unit TEXT NOT NULL,          -- "ms", "mb", "count"
    recorded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create indexes for common queries
CREATE INDEX idx_processing_history_category ON processing_history(category);
CREATE INDEX idx_processing_history_processed_at ON processing_history(processed_at);
CREATE INDEX idx_file_organizations_category ON file_organizations(category);
CREATE INDEX idx_file_organizations_moved_at ON file_organizations(moved_at);
```

## State Management

### Session State
```python
@dataclass
class ChatSession:
    """Chat session state"""
    session_id: str
    user_id: Optional[str]
    messages: List[Message]
    context: Dict[str, Any]
    created_at: datetime
    last_activity: datetime
    mcp_connection: Optional[MCPConnection]
    
@dataclass
class Message:
    """Chat message"""
    role: str  # "user", "assistant", "system"
    content: str
    timestamp: datetime
    tool_calls: Optional[List[ToolCall]] = None
    
@dataclass
class ToolCall:
    """MCP tool call record"""
    tool_name: str
    parameters: Dict[str, Any]
    result: Optional[Dict[str, Any]]
    error: Optional[str]
    duration_ms: float
```

### Model State
```python
@dataclass
class ModelState:
    """State of ML models"""
    ocr_available: bool
    vision_model_loaded: bool
    vision_model_path: Optional[str]
    vision_model_size_mb: float
    vision_load_time_ms: float
    last_used: Optional[datetime]
```

## Validation Rules

1. **File Paths**: Must exist and be readable image files
2. **Categories**: Must be one of predefined categories
3. **Filenames**: Must be filesystem-safe (no special chars)
4. **Processing Time**: Track and alert if exceeds thresholds
5. **Confidence Scores**: Between 0.0 and 1.0
6. **Batch Size**: Maximum 1000 files per batch
7. **Text Length**: OCR text limited to 10000 characters
8. **Model Responses**: Must be valid JSON for vision model

## Data Flow Examples

### Single File Processing
```
Input: {"path": "/screenshots/code_example.png"}
    ↓
OCR Processing: {"text": "def hello()...", "word_count": 45}
    ↓
Classification: {"category": "code", "confidence": 0.95}
    ↓
Output: {
    "category": "code",
    "suggested_filename": "python_hello_function.png",
    "processing_method": "ocr",
    "processing_time_ms": 35.2
}
```

### Batch Processing with Mixed Methods
```
Input: {"folder": "/screenshots", "max_files": 10}
    ↓
File Discovery: Found 10 PNG files
    ↓
Processing:
  - 7 files → OCR (sufficient text)
  - 3 files → Vision (insufficient text)
    ↓
Output: {
    "total_files": 10,
    "successful": 10,
    "ocr_processed": 7,
    "vision_processed": 3,
    "categories_count": {
        "code": 4,
        "documentation": 2,
        "design": 3,
        "other": 1
    }
}
```