# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

OpenSanctions is a comprehensive sanctions data aggregation system built with Python. It aggregates open-source sanctions data, politically exposed persons (PEPs), and related entities using the Follow the Money (FtM) framework. The core data processing engine is called `zavod`.

## Key Commands

### Docker Development
- `make build` - Build the Docker containers (equivalent to `docker-compose build --pull`)
- `make shell` - Start an interactive bash shell in the container environment
- `make run` - Run the full OpenSanctions crawling process
- `make stop` - Stop all running containers
- `make clean` - Clean up data directories and build artifacts

### Data Processing
- `zavod crawl <dataset.yml>` - Crawl a specific dataset
- `docker compose run --rm app zavod crawl datasets/de/abgeordnetenwatch/de_abgeordnetenwatch.yml` - Example crawl command
- `docker compose up -d db` - Start the database for caching

### Testing and Quality
- `ruff` - Code linting (configured in pyproject.toml)
- `black` - Code formatting
- `isort` - Import sorting
- `mypy` - Type checking
- `pytest` - Run tests

## Architecture

### Core Components

**zavod Package (`/zavod/zavod/`)**
- Main data processing framework built on Follow the Money
- `cli.py` - Command-line interface for all operations
- `crawl.py` - Dataset crawling logic
- `context.py` - Execution context management
- `entity.py` - Entity data models

**Key Modules:**
- `exporters/` - Data export formats (FTM, CSV, maritime, securities, etc.)
- `helpers/` - Utility functions for addresses, dates, names, sanctions, etc.
- `integration/` - Deduplication, indexing, and entity linking
- `meta/` - Dataset metadata management
- `runtime/` - Resource management, HTTP handling, versioning
- `stateful/` - Persistent data models and position tracking

### Dataset Structure

**datasets/ Directory Organization:**
- `_collections/` - Data collection definitions (sanctions.yml, peps.yml, etc.)
- `_externals/` - External data source mappings
- `_global/` - International/multi-country datasets
- `{country_code}/` - Country-specific datasets (e.g., `us/`, `de/`, `gb/`)

**Dataset Components:**
- Each dataset contains a YAML configuration file and a `crawler.py`
- YAML files define metadata, coverage, publishers, and processing parameters
- Crawlers implement data extraction and transformation logic

### Data Processing Pipeline

1. **Crawling** - Extract data from sources using dataset-specific crawlers
2. **Cleaning** - Standardize and normalize data structures
3. **Entity Creation** - Convert to Follow the Money entity format
4. **Deduplication** - Identify and merge duplicate entities
5. **Export** - Output in various formats (JSON, CSV, etc.)

## Development Patterns

### Dataset Development
- Dataset configurations use YAML format with standardized fields
- Crawlers import from `zavod` package and use `Context` for execution
- Use helper functions from `zavod.helpers` for common data transformations
- Position entities use `zavod.stateful.positions.categorise` for classification

### Entity Handling
- All entities follow Follow the Money schema
- Use `h.make_position()` for creating position entities
- Country codes use ISO 3166-1 alpha-2 format
- Dates should be normalized using zavod date helpers

### Code Style
- Python 3.11+ required
- Uses `black`, `isort`, and `ruff` for code formatting
- Type hints required (`mypy` configured)
- Follow existing patterns in similar datasets

### Testing
- Test fixtures located in `zavod/tests/fixtures/`
- Integration tests for crawlers and exporters
- Use `pytest` with coverage reporting

## Environment Setup

The project uses Docker for development:
1. Start database: `docker compose up -d db`
2. Build containers: `make build`
3. Enter development shell: `make shell`

Data is stored in `./data/` directory and mounted in containers.

## Key Dependencies

- **followthemoney** - Core data schema and entity framework
- **nomenklatura** - Entity resolution and deduplication
- **rigour** - Data validation utilities
- **requests** - HTTP client for data fetching
- **lxml** - XML/HTML parsing
- **duckdb** - In-memory analytics database
- **structlog** - Structured logging

## Important Links

Refer the link below for more data.

https://deepwiki.com/opensanctions/opensanctions
https://deepwiki.com/opensanctions/nomenklatura
https://deepwiki.com/opensanctions/yente