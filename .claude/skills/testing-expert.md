---
name: testing-expert
description: Use this skill when writing tests for Python applications with pytest. Invoke when creating unit tests, integration tests, test fixtures, mocking dependencies, generating test data, measuring code coverage, implementing TDD, testing FastAPI endpoints, testing Neo4j interactions, or troubleshooting test failures. Also use for configuring pytest, pytest-cov, and CI/CD testing pipelines.
allowed-tools: [Read, Write, Edit, Grep, Bash]
---

# Python Testing Expert (pytest)

You are an expert in **testing Python applications** using **pytest**, with specialized knowledge of testing FastAPI APIs, Neo4j databases, and neuroscience data processing.

## Core Expertise

### pytest Fundamentals
- **Test discovery**: Automatic test discovery (`test_*.py` or `*_test.py`)
- **Assertions**: Simple `assert` statements with detailed failure messages
- **Fixtures**: Reusable test setup and teardown
- **Parametrization**: Running same test with different inputs
- **Markers**: Categorizing and selecting tests
- **Plugins**: pytest-cov, pytest-mock, pytest-asyncio

### Testing Philosophy
- **Test-Driven Development (TDD)**: Write tests before code
- **AAA Pattern**: Arrange, Act, Assert
- **Test Independence**: Each test should run in isolation
- **Fast feedback**: Tests should run quickly
- **Readable tests**: Tests are documentation

---

## Basic pytest Structure

```python
# tests/test_parsing.py

import pytest
from pathlib import Path
from parsing import parse_nwb_file

# Simple test
def test_parse_nwb_file():
    """Test parsing a basic NWB file"""
    # Arrange
    file_path = Path("tests/data/sample.nwb")

    # Act
    result = parse_nwb_file(file_path)

    # Assert
    assert result is not None
    assert result['file_type'] == 'NWBFile'
    assert 'subject' in result

# Test with multiple assertions
def test_nwb_file_metadata():
    result = parse_nwb_file(Path("tests/data/sample.nwb"))

    assert result['session_id'] == 'test-session-001'
    assert result['experimenter'] == ['John Doe']
    assert result['institution'] == 'Test University'
    assert result['lab'] == 'Test Lab'
```

---

## Test Fixtures

### Basic Fixtures

```python
import pytest
from pathlib import Path

@pytest.fixture
def sample_nwb_file():
    """Fixture: Provides path to sample NWB file"""
    return Path("tests/data/sample.nwb")

@pytest.fixture
def parsed_nwb_data(sample_nwb_file):
    """Fixture: Provides parsed NWB data"""
    from parsing import parse_nwb_file
    return parse_nwb_file(sample_nwb_file)

# Using fixtures
def test_with_fixture(sample_nwb_file):
    """Test uses fixture"""
    assert sample_nwb_file.exists()
    assert sample_nwb_file.suffix == '.nwb'

def test_parsed_data(parsed_nwb_data):
    """Test uses fixture that depends on another fixture"""
    assert parsed_nwb_data is not None
    assert 'subject' in parsed_nwb_data
```

### Fixture Scopes

```python
# Function scope (default) - run once per test
@pytest.fixture
def temp_file():
    """Creates temp file for each test"""
    path = Path("temp.txt")
    path.write_text("test")
    yield path
    path.unlink()  # Cleanup

# Session scope - run once per test session
@pytest.fixture(scope="session")
def neo4j_driver():
    """Neo4j connection for entire test session"""
    from neo4j import GraphDatabase
    driver = GraphDatabase.driver(
        "bolt://localhost:7687",
        auth=("neo4j", "test-password")
    )
    yield driver
    driver.close()

# Module scope - run once per test file
@pytest.fixture(scope="module")
def large_test_data():
    """Expensive data generation, shared across module"""
    data = generate_large_dataset()
    return data

# Class scope - run once per test class
@pytest.fixture(scope="class")
def database_setup():
    """Setup database for test class"""
    setup_test_database()
    yield
    teardown_test_database()
```

### Fixture with Cleanup (Teardown)

```python
@pytest.fixture
def neo4j_test_db():
    """Neo4j test database with cleanup"""
    from neo4j import GraphDatabase

    driver = GraphDatabase.driver(
        "bolt://localhost:7687",
        auth=("neo4j", "password")
    )

    # Setup: Create test data
    with driver.session() as session:
        session.run("CREATE (n:TestNode {name: 'test'})")

    yield driver

    # Teardown: Clean up test data
    with driver.session() as session:
        session.run("MATCH (n:TestNode) DELETE n")

    driver.close()
```

---

## Mocking and Patching

### Using pytest-mock

```python
import pytest
from unittest.mock import Mock, MagicMock, patch

# Mock a function
def test_with_mock(mocker):
    """Test using pytest-mock plugin"""
    # Mock external API call
    mock_llm = mocker.patch('query_processor.call_claude_api')
    mock_llm.return_value = {
        'answer': 'Mocked answer',
        'intent': 'metadata_query'
    }

    from query_processor import process_query
    result = process_query("What devices were used?")

    assert result['answer'] == 'Mocked answer'
    mock_llm.assert_called_once()

# Mock file I/O
def test_file_upload_mock(mocker):
    """Mock file operations"""
    mock_save = mocker.patch('api.save_uploaded_file')
    mock_save.return_value = Path("/uploads/test.nwb")

    from api import upload_handler
    result = upload_handler(Mock(filename="test.nwb"))

    assert result['status'] == 'success'
    mock_save.assert_called()

# Mock database
def test_with_mock_db(mocker):
    """Mock Neo4j database calls"""
    mock_session = MagicMock()
    mock_result = [{'name': 'Test Device', 'manufacturer': 'Test Corp'}]
    mock_session.run.return_value = mock_result

    mocker.patch('graph.get_neo4j_session', return_value=mock_session)

    from graph import get_devices
    devices = get_devices()

    assert len(devices) == 1
    assert devices[0]['name'] == 'Test Device'
```

### Mocking Anthropic API (Claude)

```python
def test_query_with_mock_llm(mocker):
    """Test query processing with mocked LLM"""
    # Mock Anthropic client
    mock_client = mocker.patch('query_processor.anthropic.Anthropic')
    mock_instance = mock_client.return_value
    mock_instance.messages.create.return_value = Mock(
        content=[Mock(text="The devices used were: Device A, Device B")]
    )

    from query_processor import generate_answer
    result = generate_answer("What devices?", neo4j_results=[])

    assert "Device A" in result
    assert "Device B" in result
```

---

## Parametrized Tests

### Testing Multiple Inputs

```python
@pytest.mark.parametrize("input_value,expected", [
    ("valid.nwb", True),
    ("invalid.txt", False),
    ("test.NWB", True),  # Case insensitive
    ("file", False),     # No extension
])
def test_is_valid_nwb_file(input_value, expected):
    """Test file validation with multiple inputs"""
    from validation import is_valid_nwb_file
    assert is_valid_nwb_file(input_value) == expected

@pytest.mark.parametrize("nwb_type,expected_label", [
    ("ElectricalSeries", "ElectricalSeries"),
    ("ImageSeries", "ImageSeries"),
    ("TimeSeries", "TimeSeries"),
    ("Subject", "Subject"),
    ("Device", "Device"),
])
def test_nwb_type_to_label(nwb_type, expected_label):
    """Test NWB type mapping"""
    from graph import nwb_type_to_neo4j_label
    assert nwb_type_to_neo4j_label(nwb_type) == expected_label
```

### Parametrizing Fixtures

```python
@pytest.fixture(params=[
    "sample1.nwb",
    "sample2.nwb",
    "sample3.nwb"
])
def nwb_file_path(request):
    """Fixture that provides multiple NWB files"""
    return Path(f"tests/data/{request.param}")

def test_parsing_multiple_files(nwb_file_path):
    """Test runs 3 times, once for each file"""
    from parsing import parse_nwb_file
    result = parse_nwb_file(nwb_file_path)
    assert result is not None
```

---

## Test Markers

### Organizing Tests

```python
import pytest

# Mark slow tests
@pytest.mark.slow
def test_large_file_parsing():
    """Parsing 1GB file - slow test"""
    # Run with: pytest -m slow
    pass

# Mark integration tests
@pytest.mark.integration
def test_end_to_end_flow():
    """Full upload → parse → query flow"""
    # Run with: pytest -m integration
    pass

# Mark unit tests
@pytest.mark.unit
def test_parse_subject_metadata():
    """Unit test for subject parsing"""
    # Run with: pytest -m unit
    pass

# Skip tests conditionally
@pytest.mark.skipif(
    not Path("tests/data/large.nwb").exists(),
    reason="Large test file not available"
)
def test_with_large_file():
    pass

# Expected to fail
@pytest.mark.xfail(reason="Feature not implemented yet")
def test_future_feature():
    assert False  # Will not fail the test suite
```

### Custom Markers (pytest.ini)

```ini
# pytest.ini
[pytest]
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks tests as integration tests
    unit: marks tests as unit tests
    requires_neo4j: marks tests that require Neo4j running
    requires_nwb_files: marks tests that need test NWB files
```

---

## Testing FastAPI Applications

### Using TestClient

```python
from fastapi.testclient import TestClient
from api import app
import pytest

client = TestClient(app)

def test_health_endpoint():
    """Test health check endpoint"""
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "healthy"}

def test_upload_endpoint():
    """Test file upload (Story 5.1)"""
    files = {
        "file": ("test.nwb", b"mock nwb content", "application/octet-stream")
    }
    response = client.post("/api/v1/upload", files=files)

    assert response.status_code == 200
    data = response.json()
    assert "file_id" in data
    assert data["status"] == "ready"

def test_upload_invalid_file():
    """Test upload with invalid file type"""
    files = {"file": ("test.txt", b"text content", "text/plain")}
    response = client.post("/api/v1/upload", files=files)

    assert response.status_code == 400
    assert "Invalid file type" in response.json()["detail"]

def test_query_endpoint():
    """Test query processing (Story 5.2)"""
    query_data = {
        "file_id": "12345678-1234-1234-1234-123456789012",
        "query_text": "What devices were used?"
    }
    response = client.post("/api/v1/query", json=query_data)

    assert response.status_code == 200
    data = response.json()
    assert "answer" in data
    assert "provenance" in data
    assert "processing_time_ms" in data

def test_status_endpoint():
    """Test status endpoint (Story 5.3)"""
    file_id = "12345678-1234-1234-1234-123456789012"
    response = client.get(f"/api/v1/status/{file_id}")

    assert response.status_code == 200
    data = response.json()
    assert data["file_id"] == file_id
    assert "status" in data
```

### Testing with Dependency Overrides

```python
from fastapi import Depends

# Original dependency
def get_db():
    return real_database_connection()

# Test with mock dependency
@pytest.fixture
def mock_db():
    return MockDatabase()

def test_with_mock_dependency(mock_db):
    """Override app dependency for testing"""
    from api import app, get_db

    # Override dependency
    app.dependency_overrides[get_db] = lambda: mock_db

    # Test with mocked dependency
    response = client.get("/api/v1/structure/test-id")
    assert response.status_code == 200

    # Clean up
    app.dependency_overrides.clear()
```

---

## Testing Neo4j Interactions

### Test Neo4j Queries

```python
import pytest
from neo4j import GraphDatabase

@pytest.fixture(scope="module")
def neo4j_driver():
    """Neo4j test database connection"""
    driver = GraphDatabase.driver(
        "bolt://localhost:7687",
        auth=("neo4j", "test-password")
    )
    yield driver
    driver.close()

@pytest.fixture
def clean_neo4j_db(neo4j_driver):
    """Clean Neo4j database before each test"""
    with neo4j_driver.session() as session:
        session.run("MATCH (n) DETACH DELETE n")
    yield
    with neo4j_driver.session() as session:
        session.run("MATCH (n) DETACH DELETE n")

def test_create_node(neo4j_driver, clean_neo4j_db):
    """Test node creation"""
    from graph import create_experimenter_node

    create_experimenter_node(
        neo4j_driver,
        name="John Doe",
        file_path="/path/to/file.nwb"
    )

    # Verify node was created
    with neo4j_driver.session() as session:
        result = session.run(
            "MATCH (e:Experimenter {name: $name}) RETURN e",
            name="John Doe"
        )
        record = result.single()
        assert record is not None
        assert record["e"]["name"] == "John Doe"

def test_create_relationship(neo4j_driver, clean_neo4j_db):
    """Test relationship creation"""
    from graph import create_nwb_file_node, create_experimenter_node, link_experimenter

    # Create nodes
    nwb_id = create_nwb_file_node(neo4j_driver, {"session_id": "test-001"})
    exp_id = create_experimenter_node(neo4j_driver, {"name": "Jane Smith"})

    # Create relationship
    link_experimenter(neo4j_driver, nwb_id, exp_id)

    # Verify relationship
    with neo4j_driver.session() as session:
        result = session.run("""
            MATCH (n:NWBFile)-[r:HAS_EXPERIMENTER]->(e:Experimenter)
            WHERE n.session_id = $session_id
            RETURN count(r) as count
        """, session_id="test-001")
        assert result.single()["count"] == 1
```

---

## Test Data Generation

### Generating Test NWB Files

```python
import pytest
from pynwb import NWBFile, NWBHDF5IO
from pynwb.file import Subject
from datetime import datetime
from pathlib import Path

@pytest.fixture
def test_nwb_file(tmp_path):
    """Generate minimal NWB file for testing"""
    nwbfile = NWBFile(
        session_description='Test session',
        identifier='test-001',
        session_start_time=datetime.now(),
        experimenter=['Test User'],
        lab='Test Lab',
        institution='Test University'
    )

    # Add subject
    nwbfile.subject = Subject(
        subject_id='test-subject',
        species='Mus musculus',
        sex='M',
        age='P90D'
    )

    # Save to file
    file_path = tmp_path / "test.nwb"
    with NWBHDF5IO(str(file_path), 'w') as io:
        io.write(nwbfile)

    return file_path

def test_with_generated_nwb(test_nwb_file):
    """Test using generated NWB file"""
    from parsing import parse_nwb_file
    result = parse_nwb_file(test_nwb_file)

    assert result['identifier'] == 'test-001'
    assert result['experimenter'] == ['Test User']
```

### Mock Data Factories

```python
def create_mock_parsed_data():
    """Factory: Create mock parsed NWB data"""
    return {
        'file_type': 'NWBFile',
        'identifier': 'mock-001',
        'session_id': 'session-001',
        'experimenter': ['Alice', 'Bob'],
        'institution': 'Mock University',
        'lab': 'Mock Lab',
        'subject': {
            'subject_id': 'mouse-001',
            'species': 'Mus musculus',
            'sex': 'F'
        },
        'devices': [
            {'name': 'Device1', 'manufacturer': 'Corp A'},
            {'name': 'Device2', 'manufacturer': 'Corp B'}
        ],
        'electrodes': [
            {'id': 0, 'x': 100.0, 'y': 200.0, 'z': 300.0, 'location': 'V1'},
            {'id': 1, 'x': 110.0, 'y': 210.0, 'z': 310.0, 'location': 'V1'}
        ]
    }

def test_graph_building():
    """Test graph building with mock data"""
    from graph import build_graph
    data = create_mock_parsed_data()

    result = build_graph(data)

    assert result['nodes_created'] >= 5
    assert result['relationships_created'] >= 4
```

---

## Coverage Testing

### Using pytest-cov

```bash
# Run tests with coverage
pytest --cov=src --cov-report=html --cov-report=term

# Coverage for specific module
pytest tests/test_parsing.py --cov=parsing

# Fail if coverage below threshold
pytest --cov=src --cov-fail-under=70

# Show missing lines
pytest --cov=src --cov-report=term-missing
```

### Coverage Configuration

```ini
# pytest.ini
[pytest]
addopts =
    --verbose
    --cov=src
    --cov-report=html
    --cov-report=term-missing
    --cov-fail-under=60

# pyproject.toml
[tool.coverage.run]
source = ["src"]
omit = [
    "tests/*",
    "**/__pycache__/*",
    "**/conftest.py"
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
    "if TYPE_CHECKING:",
]
```

---

## Integration Testing

### End-to-End Test

```python
@pytest.mark.integration
def test_complete_workflow(test_nwb_file, neo4j_driver, clean_neo4j_db):
    """
    Test complete workflow: Upload → Parse → Build Graph → Query

    Story 7.3: Integration Test for Complete Flow
    """
    from api import app
    from fastapi.testclient import TestClient

    client = TestClient(app)

    # Step 1: Upload file
    with open(test_nwb_file, 'rb') as f:
        files = {"file": ("test.nwb", f, "application/octet-stream")}
        upload_response = client.post("/api/v1/upload", files=files)

    assert upload_response.status_code == 200
    file_id = upload_response.json()["file_id"]

    # Step 2: Check status
    status_response = client.get(f"/api/v1/status/{file_id}")
    assert status_response.status_code == 200
    assert status_response.json()["status"] == "ready"

    # Step 3: Query data
    query_data = {
        "file_id": file_id,
        "query_text": "Who are the experimenters?"
    }
    query_response = client.post("/api/v1/query", json=query_data)
    assert query_response.status_code == 200
    assert "Test User" in query_response.json()["answer"]

    # Step 4: Verify graph structure
    with neo4j_driver.session() as session:
        result = session.run("MATCH (n) RETURN count(n) as count")
        node_count = result.single()["count"]
        assert node_count > 0  # Nodes were created
```

---

## Test-Driven Development (TDD)

### TDD Cycle: Red → Green → Refactor

```python
# Step 1: Write failing test (RED)
def test_extract_experimenter():
    """Test experimenter extraction from NWB file"""
    from parsing import extract_experimenter

    nwb_data = {'general': {'experimenter': ['Alice', 'Bob']}}
    result = extract_experimenter(nwb_data)

    assert result == ['Alice', 'Bob']
    # Test fails - function doesn't exist yet

# Step 2: Write minimal code to pass (GREEN)
# In parsing.py:
def extract_experimenter(nwb_data):
    return nwb_data.get('general', {}).get('experimenter', [])

# Test now passes

# Step 3: Refactor if needed
def extract_experimenter(nwb_data):
    """Extract experimenter list from NWB data"""
    general = nwb_data.get('general', {})
    experimenters = general.get('experimenter', [])

    # Ensure it's a list
    if isinstance(experimenters, str):
        experimenters = [experimenters]

    return experimenters

# Test still passes, code is cleaner
```

---

## Asynchronous Testing

### pytest-asyncio

```python
import pytest
import asyncio

@pytest.mark.asyncio
async def test_async_function():
    """Test async function"""
    from query_processor import async_call_llm

    result = await async_call_llm("test query")
    assert result is not None

@pytest.mark.asyncio
async def test_concurrent_queries():
    """Test multiple queries concurrently"""
    from query_processor import process_query_async

    queries = [
        "What devices were used?",
        "Who are the experimenters?",
        "How many trials?"
    ]

    results = await asyncio.gather(*[
        process_query_async(q) for q in queries
    ])

    assert len(results) == 3
    assert all(r['answer'] for r in results)
```

---

## Conftest.py Organization

### Shared Fixtures

```python
# tests/conftest.py
import pytest
from pathlib import Path
from neo4j import GraphDatabase

# Shared fixtures available to all tests

@pytest.fixture(scope="session")
def test_data_dir():
    """Test data directory"""
    return Path(__file__).parent / "data"

@pytest.fixture(scope="session")
def neo4j_driver():
    """Neo4j connection for tests"""
    driver = GraphDatabase.driver(
        "bolt://localhost:7687",
        auth=("neo4j", "test-password")
    )
    yield driver
    driver.close()

@pytest.fixture
def clean_db(neo4j_driver):
    """Clean database before each test"""
    with neo4j_driver.session() as session:
        session.run("MATCH (n) DETACH DELETE n")
    yield
    with neo4j_driver.session() as session:
        session.run("MATCH (n) DETACH DELETE n")

@pytest.fixture
def sample_nwb_file(test_data_dir):
    """Sample NWB file path"""
    return test_data_dir / "sample.nwb"

# Pytest hooks
def pytest_configure(config):
    """Configure pytest"""
    config.addinivalue_line(
        "markers", "slow: marks tests as slow"
    )
    config.addinivalue_line(
        "markers", "integration: integration tests"
    )
```

---

## Best Practices for NWB-KG Project

### 1. **Test Structure (Story 7.1, 7.2)**

```
tests/
├── conftest.py                 # Shared fixtures
├── data/                       # Test NWB files
│   ├── sample.nwb
│   └── minimal.nwb
├── test_parsing.py            # Story 7.1: Unit tests for parsing
├── test_graph.py              # Story 7.2: Unit tests for graph building
├── test_api.py                # FastAPI endpoint tests
├── test_query.py              # Query processing tests
└── test_integration.py        # Story 7.3: Integration tests
```

### 2. **Test Naming Conventions**

```python
# Good: Descriptive names
def test_parse_nwb_file_extracts_subject_metadata():
    pass

def test_upload_endpoint_rejects_non_nwb_files():
    pass

def test_cypher_generation_handles_device_queries():
    pass

# Avoid: Vague names
def test_parsing():
    pass

def test_api():
    pass
```

### 3. **One Assert Per Concept**

```python
# Good: Multiple related assertions OK
def test_experimenter_node_creation():
    node = create_experimenter_node("Alice")
    assert node['name'] == "Alice"
    assert node['type'] == "Experimenter"
    assert 'created_at' in node

# Avoid: Testing multiple unrelated things
def test_everything():
    assert parse_file(file1)
    assert build_graph(data)
    assert query_works("test")
```

### 4. **Use Fixtures for Setup**

```python
# Good: Fixture for common setup
@pytest.fixture
def parsed_nwb():
    return parse_nwb_file("test.nwb")

def test_has_subject(parsed_nwb):
    assert 'subject' in parsed_nwb

def test_has_devices(parsed_nwb):
    assert len(parsed_nwb['devices']) > 0

# Avoid: Repeated setup in each test
def test_has_subject():
    data = parse_nwb_file("test.nwb")  # Repeated
    assert 'subject' in data

def test_has_devices():
    data = parse_nwb_file("test.nwb")  # Repeated
    assert len(data['devices']) > 0
```

---

## Running Tests

### Command Examples

```bash
# Run all tests
pytest

# Run specific test file
pytest tests/test_parsing.py

# Run specific test
pytest tests/test_parsing.py::test_extract_subject

# Run tests matching pattern
pytest -k "test_device"

# Run marked tests
pytest -m unit
pytest -m "not slow"

# Verbose output
pytest -v

# Show print statements
pytest -s

# Stop on first failure
pytest -x

# Run last failed tests
pytest --lf

# Parallel execution (with pytest-xdist)
pytest -n 4  # 4 workers
```

---

## When to Use This Skill

Claude will automatically invoke this skill when:
- Writing unit tests for Python code
- Creating integration tests
- Setting up test fixtures
- Mocking dependencies (APIs, databases, file I/O)
- Generating test data
- Measuring code coverage
- Testing FastAPI endpoints
- Testing Neo4j interactions
- Implementing TDD
- Troubleshooting test failures
- Working on Epic 7 stories (Testing)

---

## Resources

- **pytest Documentation**: https://docs.pytest.org/
- **pytest-cov**: https://pytest-cov.readthedocs.io/
- **pytest-mock**: https://pytest-mock.readthedocs.io/
- **FastAPI Testing**: https://fastapi.tiangolo.com/tutorial/testing/

---

When this skill is invoked, provide expert guidance on writing comprehensive, maintainable tests for Python applications, specifically tailored to the NWB Knowledge Graph project's testing requirements (Epic 7).