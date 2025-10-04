# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

iPLM_Orchestrator is a multi-agent AI system built with [crewAI](https://crewai.com) framework. The project is named KTB3 and uses AI agents to collaborate on complex tasks through a sequential process.

**Framework:** crewAI >=0.201.1
**Python:** >=3.10, <3.14
**Package Manager:** UV (Astral's uv)

## Essential Commands

### Setup & Installation
```bash
pip install uv                # Install UV package manager
crewai install               # Lock and install dependencies
```

### Running the Crew
```bash
crewai run                   # Run the crew (from ktb3/ directory)
# OR
python -m ktb3.main         # Direct Python execution
```

### Testing & Training
```bash
crewai test <n_iterations> <eval_llm>    # Test crew execution
crewai train <n_iterations> <filename>   # Train the crew
crewai replay <task_id>                  # Replay specific task execution
```

### Environment Setup
- Add `OPENAI_API_KEY` to `ktb3/.env` file before running

## Architecture

### Core Structure

The project follows crewAI's decorator-based architecture pattern:

- **[ktb3/src/ktb3/crew.py](ktb3/src/ktb3/crew.py)**: Core crew class using `@CrewBase` decorator
  - Agents defined with `@agent` decorator
  - Tasks defined with `@task` decorator
  - Crew configuration with `@crew` decorator
  - Uses `Process.sequential` for task execution

- **[ktb3/src/ktb3/main.py](ktb3/src/ktb3/main.py)**: Entry point with CLI commands
  - `run()`: Execute crew with inputs
  - `train()`: Train crew iterations
  - `replay()`: Replay task execution
  - `test()`: Test crew performance

### Configuration Files

- **[ktb3/src/ktb3/config/agents.yaml](ktb3/src/ktb3/config/agents.yaml)**: Agent definitions
  - Supports templated variables (e.g., `{topic}`)
  - Defines role, goal, and backstory for each agent

- **[ktb3/src/ktb3/config/tasks.yaml](ktb3/src/ktb3/config/tasks.yaml)**: Task definitions
  - Links tasks to agents
  - Supports variable interpolation
  - Defines expected outputs

### Agent System

Current agents:
- **researcher**: Researches the specified topic
- **reporting_analyst**: Creates detailed reports from research

Tasks flow sequentially:
1. research_task → researcher agent
2. reporting_task → reporting_analyst agent (outputs to `report.md`)

### Custom Tools

Custom tools extend `crewai.tools.BaseTool`:
- Define input schema with Pydantic `BaseModel`
- Implement `_run()` method
- See [ktb3/src/ktb3/tools/custom_tool.py](ktb3/src/ktb3/tools/custom_tool.py) for template

### Knowledge Sources

The `ktb3/knowledge/` directory contains knowledge sources that can be integrated into crews for context-aware agent behavior.

## Key Patterns

1. **Input Variables**: Pass inputs dict to `crew().kickoff(inputs=inputs)` - automatically interpolates into YAML configs
2. **Agent Config**: Agents load config from YAML via `config=self.agents_config['agent_name']`
3. **Task Config**: Tasks load config from YAML via `config=self.tasks_config['task_name']`
4. **Output Files**: Specify `output_file='filename.md'` in Task definition for file output
5. **Process Types**: Sequential (current) or Hierarchical available via `Process.sequential` or `Process.hierarchical`

## Project Scripts

Defined in [ktb3/pyproject.toml](ktb3/pyproject.toml):
- `ktb3` → runs main
- `run_crew` → runs main
- `train` → training mode
- `replay` → replay mode
- `test` → test mode
