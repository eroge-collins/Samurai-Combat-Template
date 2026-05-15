Always use the `codex-unreal` MCP server when interacting with the Unreal Editor for this project, if the server is configured and the editor bridge manifest is present.

Prefer the Unreal MCP tools over guessing Blueprint graph state from source files alone.

Project context:
- This project is building a professional Codex-to-Unreal MCP bridge/plugin. The goal is to let Codex drive Unreal Editor workflows from prompts, especially Blueprint asset authoring.
- Gameplay/demo systems should be implemented in Blueprint assets through the MCP bridge whenever possible.
- C++ changes should primarily improve the MCP bridge/plugin authoring capabilities when Blueprint creation/manipulation is blocked or insufficient. Do not implement gameplay features directly in C++ unless explicitly asked.
- Current demo focus is a ThirdPerson combat prototype under `/Game/Demo`, with combo attacks, target lock, locomotion blendspaces, hit detection, camera shake, hitstop, Motion Warping attack assist, damage, hit reaction, poise, sprint, dodge, block, and target lock systems.
- Investigate issues using `codex-unreal` MCP first, preserve user edits in Unreal assets, and avoid reauthoring/resetting Demo assets unless explicitly requested.
- The MCP Python server is stored outside the project at `C:\tools\unreal_codex_mcp`. The Codex config for this MCP is expected at `C:\Users\R\.codex\config.toml` under `[mcp_servers.codex-unreal]`, launching `C:\tools\unreal_codex_mcp\server.py` with `--project-root` pointing at this project.
- The project-local plugin remains under `C:\tools\Codex\Plugins\CodexMCPBridge`; only the Python MCP server is stored outside the project folder.

Unreal Editor workflow:
- Before any Unreal/Blueprint work, call `unreal_project.get_status` or the equivalent `project.get_status` command through the `codex-unreal` bridge and confirm the Editor is online.
- Open this project by launching the project `.uproject`, not the generic Unreal launcher. Use the project root `C:\tools\Codex` and the file `C:\tools\Codex\Codex.uproject`.
- If the bridge is offline because the Editor is closed, open `Codex.uproject` from the project folder, wait for the Editor/bridge to become available, then retry MCP calls.
- Do not ask the user to manually open or close Unreal when the task can be handled from the local environment. Manage the Editor lifecycle directly when needed.

C++/Blueprint workflow:
- When changing C++ for the MCP bridge/plugin, close Unreal Editor before building to avoid locked modules/DLLs and stale loaded code.
- Build/compile from the project root after closing Unreal. Reopen `Codex.uproject` only after the C++ build succeeds and Blueprint/MCP work needs to continue.
- When working on gameplay systems, create and modify Blueprint assets via MCP. Only add C++ when the bridge cannot express the needed Blueprint operation, and then use the new bridge capability to finish the gameplay work in Blueprint.
- After Blueprint edits, compile the touched Blueprints through MCP, save the touched assets, and verify graph/asset state with MCP instead of relying on assumptions.

Asset safety:
- Do not reauthorize, reset, or bulk-recreate `/Game/Demo` assets without explicit permission.
- Assume the Editor may contain user edits. Inspect status/diffs where possible and preserve unrelated changes.
- Avoid duplicate notifies, duplicate input mappings, and duplicate graph logic. Prefer inspecting the existing asset graph and patching the smallest safe path.
