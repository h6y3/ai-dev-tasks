# Claude Code Setup Guide for AI Dev Tasks

This guide provides detailed instructions for setting up the AI Dev Tasks workflow with Claude Code, including installation, configuration, troubleshooting, and usage examples.

## Installation

### Option 1: Global Setup (Recommended)
Set up commands globally for use across all your projects:

```bash
# Create Claude Code commands directory
mkdir -p ~/.claude/commands

# Copy command files from this repository
cp claude-code-commands/*.md ~/.claude/commands/

# Restart Claude Code to load new commands
/exit
claude
```

### Option 2: Project-Specific Setup
Set up commands for a specific project only:

```bash
# In your project directory
mkdir -p .claude/commands

# Copy command files
cp /path/to/ai-dev-tasks/claude-code-commands/*.md .claude/commands/

# Restart Claude Code
/exit
claude
```

## Command Reference

### `/ai-dev-help`
**Purpose**: Display workflow guide and available commands  
**Usage**: `/ai-dev-help`  
**When to use**: When you need a reminder of the workflow or available commands

### `/create-prd` 
**Purpose**: Create structured Product Requirements Document  
**Usage**: `/create-prd "Your feature description"`  
**Process**:
1. Describe your feature idea
2. AI asks clarifying questions with numbered options
3. AI generates comprehensive PRD
4. PRD saved as `prd-[feature-name].md` in `/tasks/`

**Example**:
```
/create-prd "Add user authentication with email verification and password reset"
```

### `/generate-tasks`
**Purpose**: Generate implementation task list from PRD  
**Usage**: `/generate-tasks`  
**Process**:
1. AI scans `/tasks/` for available PRDs
2. AI presents numbered options if multiple PRDs exist
3. **Phase 1**: AI generates high-level parent tasks
4. You respond "Go" to proceed
5. **Phase 2**: AI generates detailed sub-tasks
6. Task list saved as `tasks-prd-[feature-name].md`

### `/process-tasks`
**Purpose**: Implement tasks one-by-one with approval gates  
**Usage**: `/process-tasks "start with task 1.1"`  
**Process**:
1. AI implements one sub-task
2. AI marks task complete `[x]`
3. AI asks for approval
4. You respond "yes" or "y" to continue
5. When parent task complete: AI runs tests → commits → continues

## File Organization

The workflow uses a consistent file structure:

```
your-project/
├── tasks/
│   ├── prd-user-auth.md              # Product Requirements
│   ├── tasks-prd-user-auth.md        # Implementation tasks
│   ├── prd-payment-system.md         # Another feature PRD
│   └── tasks-prd-payment-system.md   # Another feature tasks
├── src/                              # Your code
└── .claude/
    └── commands/                     # Project-specific commands (optional)
```

## Configuration

### Global Configuration
Add to `~/.claude/CLAUDE.md`:

```markdown
# AI Dev Tasks Workflow

When I request structured feature development using PRDs, use these commands:
- `/create-prd` - Start with PRD creation
- `/generate-tasks` - Generate task list from PRD  
- `/process-tasks` - Process tasks one by one
- `/ai-dev-help` - Show workflow guide

## File Organization
- PRDs: `/tasks/prd-[feature-name].md`
- Task Lists: `/tasks/tasks-prd-[feature-name].md`
- Follow two-phase generation and one-task-at-a-time processing
```

### Project Configuration
Add to your project's `./CLAUDE.md`:

```markdown
# AI Dev Tasks
Use structured feature development workflow:
- `/create-prd` for requirements gathering
- `/generate-tasks` for breaking down PRDs  
- `/process-tasks` for methodical implementation

Ensure `/tasks` directory exists for file organization.
```

## Usage Examples

### Complete Feature Development Workflow

```bash
# Step 1: Create PRD
/create-prd "Add shopping cart functionality with persistent storage"

# AI asks clarifying questions like:
# 1. What items can be added to cart?
# 2. Should cart persist across sessions?
# 3. What are the main user actions?
# You answer: 1, yes, view/add/remove/checkout

# Step 2: Generate tasks
/generate-tasks

# AI shows: "I found these PRDs: 1. prd-shopping-cart.md"
# AI generates parent tasks and asks: "Ready for sub-tasks? Say 'Go'"
Go

# Step 3: Process tasks
/process-tasks "start with task 1.1"

# AI implements task 1.1, then asks: "Task 1.1 complete. Continue to 1.2?"
yes

# Continue approving each task...
y
yes
y
```

### Working with Multiple PRDs

```bash
# If you have multiple PRDs
/generate-tasks

# AI responds:
# "I found these PRDs without task lists:
# 1. prd-user-auth.md
# 2. prd-shopping-cart.md  
# 3. prd-payment-system.md
# Which PRD should I use?"

# You respond:
2

# AI continues with shopping cart PRD...
```

## Troubleshooting

### Command Not Found
**Problem**: `/create-prd` returns "command not found"  
**Solution**: 
1. Verify files are in `~/.claude/commands/` or `.claude/commands/`
2. Restart Claude Code: `/exit` then `claude`
3. Check file permissions: `ls -la ~/.claude/commands/`

### Tasks Directory Missing
**Problem**: AI says "cannot find /tasks directory"  
**Solution**: Create directory in your project:
```bash
mkdir tasks
```

### PRD Not Found
**Problem**: `/generate-tasks` says "no PRDs found"  
**Solution**: 
1. Check `/tasks` directory exists
2. Verify PRD file starts with `prd-`
3. Create PRD first using `/create-prd`

### Git Commit Failures
**Problem**: AI fails to commit after completing parent task  
**Solution**:
1. Ensure git repository is initialized: `git init`
2. Check git user config: `git config user.name` and `git config user.email`
3. Verify tests pass before commit

### Existing Task List
**Problem**: `/generate-tasks` skips PRD because task list exists  
**Solution**: 
1. Delete existing task list: `rm tasks/tasks-prd-[name].md`
2. Or rename it: `mv tasks/tasks-prd-[name].md tasks/tasks-prd-[name]-backup.md`

## Advanced Usage

### Custom Task Modifications
You can manually edit task lists before processing:
1. Generate tasks with `/generate-tasks`
2. Edit `tasks/tasks-prd-[name].md` to add/remove/modify tasks
3. Process with `/process-tasks`

### Partial Implementation
Resume work on partially completed tasks:
1. Find the next uncompleted task in your task list
2. Use `/process-tasks "start with task X.Y"`

### Multiple Feature Development
Work on multiple features simultaneously:
1. Each feature gets its own PRD and task list
2. Switch between features by specifying different task lists
3. Use clear naming: `prd-feature1.md`, `prd-feature2.md`

## Integration Notes

### Claude Code Features
The workflow integrates with:
- **File Reading/Writing**: Automatic PRD and task list management
- **Git Operations**: Conventional commits with proper formatting
- **Test Running**: Automatic test suite execution
- **TodoWrite**: Internal progress tracking
- **Error Handling**: Graceful recovery from common issues

### Testing Integration
Commands automatically detect and run appropriate test frameworks:
- `npm test` for Node.js projects
- `pytest` for Python projects  
- `bin/rails test` for Rails projects
- Custom test commands from package.json

### Git Integration
Automatic commit format follows conventional commit standards:
```
feat: add shopping cart persistence

- Implements localStorage for cart data
- Adds cart state management
- Creates cart persistence utilities
- Includes comprehensive unit tests

Related to task 2.0 in shopping-cart PRD
```

## Best Practices

1. **Start Small**: Begin with simple features to get familiar with the workflow
2. **Be Specific**: Provide detailed answers to clarifying questions
3. **Review Each Step**: Use the approval gates to catch issues early
4. **Maintain Tests**: Ensure tests pass before moving to next parent task
5. **Backup Work**: Git commits provide automatic backup at each parent task completion
6. **Use Descriptive Names**: Choose clear, descriptive names for PRDs and features

## Support

For issues or questions:
1. Check this troubleshooting guide
2. Review the main [README.md](README.md) for workflow overview
3. Use `/ai-dev-help` for quick command reference
4. Open an issue in the [repository](https://github.com/h6y3/ai-dev-tasks)