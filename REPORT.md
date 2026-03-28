# Lab 8 Report

## Task 1A - Bare agent
Installed nanobot-ai via uv. Configured config.json for Qwen Code API.

## Task 1B - MCP tools
Integrated mcp-lms (9 tools). Created skills/lms/SKILL.md.

## Task 2A - Dockerize nanobot
Created nanobot/Dockerfile + entrypoint.py. Deployed as Docker service.

## Task 2B - WebSocket + Flutter
Added nanobot-websocket-channel. Enabled Flutter client. Uncommented Caddy routes.

## Task 3A - Structured logging
Happy path: request_started -> db_query -> request_completed(200)
Error path: request_started -> db_query ERROR connection refused -> request_completed

## Task 3B - Traces
Healthy trace: Caddy -> Backend -> db_query -> 200
Error trace: Caddy -> Backend -> db_query ERROR -> error response

## Task 3C - Observability MCP tools
Created mcp/mcp-obs with logs_search, logs_error_count, traces_list, traces_get.
Created skills/observability/SKILL.md.
Normal: 0 errors. Failure: agent detected unhealthy backend.

## Task 4A - Multi-step investigation
Agent identified DB connection refused errors in logs and traces.
Found planted bug: broad except returning 404 instead of real error.

## Task 4B - Proactive health check
Created cron job for 2-min health checks. Agent posted proactive reports.

## Task 4C - Bug fix and recovery
Root cause: routers/items.py except Exception returned 404 for all errors.
Fix: Changed to HTTP 500 with real error message.
Post-fix: Real DB errors now surfaced as 500.
Recovery: Health check confirms system healthy after postgres restart.
