# Specification for a Template MCP Server

> A minimal MCP server implementation to serve as a learning example and sanity check.

## Implementation Details

- Read ai_docs/** for Examples demonstrating Best Practices

### Development Environment
- Python ≥ 3.12 with uv as the package manager
- Document all functions and classes with clear docstrings
- Focus on simplicity and clarity for demonstration purposes
- Document README following style ai_docs/sample_readme.md

### MCP Server Framework
- Use the standard `mcp` package (≥1.6.0) for MCP protocol compatibility
- Implement server using the high-level FastMCP approach via `mcp.server.fastmcp`
- The server will handle stdin/stdout communication with the client
- FastMCP automatically handles conversion of tool results to MCP protocol format
- Tool responses are returned as Python objects that FastMCP converts to proper `content` array format

### Tool Response Flow
1. Tool function returns a native Python type (like a string or dictionary)
2. FastMCP automatically converts this to MCP format using TextContent objects
3. Error handling is simplified by using Python's native exception mechanism
   - Raised exceptions are automatically converted to appropriate error responses
   - FastMCP provides helper functions for standardized error responses

### Server Implementation Details

With FastMCP, the server implementation is simplified using decorators:

*Example*
```python
from mcp.server.fastmcp import FastMCP

# Create a FastMCP server instance
mcp = FastMCP("Template MCP Server")

# Register tools using decorators
@mcp.tool()
def sample_tool(name: str = "John") -> str:
    """Hello world sample tool."""
    return f"Hello, {name}!"

# Main entry point
if __name__ == "__main__":
    mcp.run()
```

The server supports:
1. Tool registration via decorators
2. Automatic handling of tool calls with proper argument passing
3. Automatic conversion of tool results to MCP protocol format
4. Built-in error handling with standardized error responses

### Testing Requirements
- Run tests with `uv run pytest`
- Tests should verify both success and error paths
- Keep tests simple but comprehensive

## Codebase Structure

- pyproject.toml
- README.md
- src/
    - template_mcp_server/
        - __init__.py
        - main.py
        - server.py
            - FastMCP instance creation
            - Tool registration
        - tools/
            - __init__.py
            - some_tool.py
            - ...
        - shared/
            - __init__.py
            - utils.py
            - data_types.py   (Pydantic)
        - tests/
            - __init__.py
            - tools/
                - __init__.py
                - some_tool.py
                - ...
            - shared/
                - __init__.py
                - test_utils.py

## Project Configuration

- CREATE pyproject.toml
- Requires Python >=3.12
- Ensure Sections 
    - [project], [build-system], [tool.setuptools], [tool.pytest] ...

### Data Types Implementation

The `data_types.py` file should contain pydantic Data types and models if necessary.


### Error Codes

| Code | Meaning |
|------|---------|
| INVALID_INPUT_FORMAT | Empty or invalid name parameter |
| INTERNAL_SERVER_ERROR| Unhandled exception inside the server |


## Tools to Expose

- CREATE def sample_tool(name: str = "John") -> str:
- Tool Description: Hello World Sample


### Required .mcp.json Configuration

*Example*
```json
{
  "mcpServers": {
    "<project_name>>": {
      "type": "stdio",
      "command": "uv",
      "args": [
        "run",
        "<project>"
      ],
      "env": {}
    }
  }
}
```

## Validation (close the loop)

- Run `uv run pytest <path_to_test>` to validate the tests are passing - do this iteratively as you build out the tests.
- After code is written, run `uv run pytest` to validate all tests are passing.
- At the end Use `uv run <project> --help` to validate the mcp server works.