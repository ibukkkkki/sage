# AGENTS.md — SAGE

## Architecture

- **`client/`** — Python >=3.13. Orchestrates scene generation via MCP. Connects to Isaac Sim and model servers.
- **`server/`** — Python ==3.10.12. Core backend: layout solvers, asset generation, physics, augmentation. MCP server.
- `IsaacLab/`, `M2T2/`, `matfuse-sd/`, `robomimic/` — Vendored/modified subprojects. Each retains its original license (not Apache 2.0).
- `server/` and `client/` are **independent projects** managed with `uv` (separate `pyproject.toml` and `uv.lock` each).

## Commands

- **Install dependencies (server):** `uv sync --directory server`
- **Install dependencies (client):** `uv sync --directory client`
- **Start Isaac Sim:** `./client/isaac_sim_conda.sh --no-window omni.isaac.sim --enable isaac.sim.mcp_extension`
- **Run generation (room desc):** `python client/client_generation_room_desc.py --room_desc "..." --server_paths ../server/layout_wo_robot.py`
- **Run robot task generation:** `python client/client_generation_robot_task.py ...`
- **Run scene augmentation:** `python client/client_generation_scene_aug.py ...`
- **Export scenes:** `python server/pack_scene_to_zip.py --layout_id <ID> --upload_name <ID>`
- There is **no test suite** for the core SAGE code. Subproject tests exist but require their respective frameworks (Isaac Sim, robomimic).
- There is **no linter/formatter/typecheck** configured. Use `ruff` or `mypy` ad-hoc if needed.

## Environment & Setup

- **Client** uses `conda` env `sage` (from `client/environment.yml`) or `uv` with Python >=3.13.
- **Server** requires Python **exactly 3.10.12** (Isaac Sim compatibility). Uses `uv`.
- Both `client/key.json` and `server/key.json` must be populated with API keys/URLs. `server/key.json` is **gitignored**.
- Required external services: TRELLIS (3D gen), Qwen3-VL (VLM), GPT-OSS (LLM), MatFuse (materials). All must be running before client invocation.
- `server/pyproject.toml` contains **hardcoded local paths** in `[tool.uv.sources]` (e.g., `/home/hongchix/codes/...`). These must be updated to match the local checkout.
- Isaac Sim 4.2.0 must be installed and the MCP extension symlinked: `ln -s <path-to>/server/isaacsim/isaac.sim.mcp_extension ~/isaacsim/exts/isaac.sim.mcp_extension`
- `HF_TOKEN` env var needed for model downloads (TRELLIS, Flux).
- `PHYSICS_CRITIC_ENABLED` / `SEMANTIC_CRITIC_ENABLED` env vars toggle validation features (default: `"true"`).

## Code Conventions

- All source files have SPDX Apache 2.0 license headers.
- Server entry: `server/main.py` is a stub. Real logic starts from layout scripts (`layout.py`, `layout_wo_robot.py`, etc.) invoked as MCP servers.
- Client entry: `client/client_generation*.py` scripts. They connect to MCP servers specified via `--server_paths`.
- `assets/` — Static media (images for README). Not build artifacts.

## Git

- Requires DCO sign-off on commits: `git commit -s -m "..."`
