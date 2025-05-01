# MCP Server

## Initial Execute Instruction Set

> Warning: This will run claude in YOLO mode with no interactions.

```bash
# Define the Name of the MCP Server we are creating.
MCP_SERVER_NAME="scrum-team-mcp-server"

# Define the Spec for the initial tool we are making.
DETAILS=$(cat <<EOF
<mcpServerName>$MCP_SERVER_NAME</mcpServerName>
<toolSpec>
    <toolsToExpose>
        <tool>
            <name>reverse_tool</name>
            <description>String Reverse</description>
            <details>The received string should always be exactly reversed.</details>
        </tool>
    </toolsToExpose>
</toolSpec>
EOF
)

# Define the tools that are allowed.
ALLOWED_TOOLS=(
    "Bash"
    "Edit"
    "View"
    "GlobTool"
    "GrepTool"
    "LSTool"
    "BatchTool"
    "AgentTool"
    "WebFetchTool"
    "Write"
)

# Establish the prompt
AI_PROMPT="
- Run git ls-files and eza --git-ignore --tree to understand the context of the project.
- Implement the Spec with:
$DETAILS
"

# Execute the Agent
claude -p $AI_PROMPT --allowedTools $ALLOWED_TOOLS --json
```