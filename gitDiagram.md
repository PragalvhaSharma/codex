flowchart TD
    User["User"]:::user

    subgraph "Frontend: Node.js CLI"
        CLI_UI["codex-cli (Node.js CLI)"]:::frontend
        CLI_BIN["CLI Entry Point\nbin/codex.js"]:::frontend
        COMP["TS React TUI\nsrc/components/"]:::frontend
        UTIL["Hooks & Utilities\nsrc/utils/"]:::frontend
        SP["Single-pass Mode\nsrc/cli-singlepass.tsx"]:::frontend
        INT["Interactive Mode\nsrc/cli.tsx"]:::frontend
        APPROVAL["Approval Overlay\nsrc/components/approval-mode-overlay.tsx"]:::frontend
        CHAT["Chat History\nsrc/components/chat/"]:::frontend
        SB_IMPL["Sandbox Impl\nsrc/utils/agent/sandbox/"]:::frontend
    end

    subgraph "Rust Core Workspace"
        CORE["Core Business Logic\ncore crate"]:::core
        CLI_RS["Rust CLI Parser & Config\ncli crate"]:::core
        EXEC["Command Execution\nexec crate"]:::core
        EXECPOL["Sandbox Policy Enforcement\nexecpolicy crate"]:::core
        TUI_RS["Terminal UI Renderer\ntui crate"]:::core
        APPLY["Patch Application\napply-patch crate"]:::core
        ANSI["ANSI Escape Utilities\nansi-escape crate"]:::core
        FILES["File Search\nfile-search crate"]:::core
        LOGIN_RS["Login Flows\nlogin crate"]:::core
        MCP_CLIENT["MCP Client\nmcp-client crate"]:::core
        MCP_SERVER["MCP Server\nmcp-server crate"]:::core
        MCP_TYPES["MCP Types & Schemas\nmcp-types crate"]:::core
        COMMON["Common Shared Types\ncommon crate"]:::core
        LINUX_SB["Linux Sandbox\nlinux-sandbox crate"]:::core
    end

    subgraph "External Services"
        OPENAI["OpenAI Chat API"]:::external
        MCP_S["MCP Servers"]:::external
    end

    subgraph "System Resources"
        FS["Local File System & Config"]:::system
        GIT["Git CLI"]:::system
        SANDBOX_OS["OS Sandbox Layer\n(Seatbelt/Landlock)"]:::system
    end

    User -->|"run codex"| CLI_BIN
    CLI_BIN -->|"loads config"| UTIL
    CLI_BIN -->|"renders UI"| COMP
    CLI_BIN -->|"spawns Rust CLI"| CLI_RS
    COMP -->|"use chat history"| CHAT
    COMP -->|"show overlay"| APPROVAL
    CLI_BIN -->|"single-pass mode"| SP
    CLI_BIN -->|"interactive mode"| INT
    CLI_BIN -->|"sandbox utils"| SB_IMPL

    CLI_RS -->|"calls core logic"| CORE
    CORE -->|"invoke exec"| EXEC
    EXEC -->|"enforce policy"| EXECPOL
    EXEC -->|"run in sandbox"| SANDBOX_OS
    CORE -->|"HTTP call"| OPENAI
    CORE -->|"HTTP call"| MCP_S
    CORE -->|"use common types"| COMMON
    CORE -->|"invoke login"| LOGIN_RS
    CORE -->|"invoke MCP client"| MCP_CLIENT
    MCP_CLIENT -->|"uses schemas"| MCP_TYPES
    CORE -->|"file search"| FILES
    APPLY -->|"apply patches"| GIT
    APPLY -->|"write files"| FS
    EXEC -->|"ansi processing"| ANSI
    CLI_RS -->|"render TUI"| TUI_RS
    CLI_RS -->|"use Linux sandbox"| LINUX_SB

    OPENAI -->|"response"| CORE
    MCP_S -->|"response"| CORE
    FS -->|"read/write"| CLI_RS
    FS -->|"read/write"| CORE

    click CLI_BIN "https://github.com/openai/codex/blob/main/codex-cli/bin/codex.js"
    click COMP "https://github.com/openai/codex/tree/main/codex-cli/src/components/"
    click UTIL "https://github.com/openai/codex/tree/main/codex-cli/src/utils/"
    click SP "https://github.com/openai/codex/blob/main/codex-cli/src/cli-singlepass.tsx"
    click INT "https://github.com/openai/codex/blob/main/codex-cli/src/cli.tsx"
    click APPROVAL "https://github.com/openai/codex/blob/main/codex-cli/src/components/approval-mode-overlay.tsx"
    click CHAT "https://github.com/openai/codex/tree/main/codex-cli/src/components/chat/"
    click SB_IMPL "https://github.com/openai/codex/tree/main/codex-cli/src/utils/agent/sandbox/"

    click CORE "https://github.com/openai/codex/tree/main/codex-rs/core/"
    click CLI_RS "https://github.com/openai/codex/tree/main/codex-rs/cli/"
    click EXEC "https://github.com/openai/codex/tree/main/codex-rs/exec/"
    click EXECPOL "https://github.com/openai/codex/tree/main/codex-rs/execpolicy/"
    click TUI_RS "https://github.com/openai/codex/tree/main/codex-rs/tui/"
    click APPLY "https://github.com/openai/codex/tree/main/codex-rs/apply-patch/"
    click ANSI "https://github.com/openai/codex/tree/main/codex-rs/ansi-escape/"
    click FILES "https://github.com/openai/codex/tree/main/codex-rs/file-search/"
    click LOGIN_RS "https://github.com/openai/codex/tree/main/codex-rs/login/"
    click MCP_CLIENT "https://github.com/openai/codex/tree/main/codex-rs/mcp-client/"
    click MCP_SERVER "https://github.com/openai/codex/tree/main/codex-rs/mcp-server/"
    click MCP_TYPES "https://github.com/openai/codex/tree/main/codex-rs/mcp-types/"
    click COMMON "https://github.com/openai/codex/tree/main/codex-rs/common/"
    click LINUX_SB "https://github.com/openai/codex/tree/main/codex-rs/linux-sandbox/"

    classDef frontend fill:#D0E8FF,stroke:#0366D6,color:#0366D6
    classDef core fill:#E6FFED,stroke:#22863A,color:#22863A
    classDef external fill:#FFE8B5,stroke:#D29B00,color:#D29B00
    classDef system fill:#F0F0F0,stroke:#6A737D,color:#6A737D
    classDef user fill:#FFF5B1,stroke:#BF8700,color:#BF8700