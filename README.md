<div align="center">

# parrot.nvim 🦜

This is [parrot.nvim](https://github.com/frankroeder/parrot.nvim), the ultimate [stochastic parrot](https://en.wikipedia.org/wiki/Stochastic_parrot) to support your text editing inside Neovim.

[Features](#features) • [Demo](#demo) • [Getting Started](#getting-started) • [Commands](#commands) • [Configuration](#configuration) • [Roadmap](#roadmap) • [FAQ](#faq)

<img src="https://github.com/frankroeder/parrot.nvim/assets/19746932/b19c5260-1713-400a-bd55-3faa87f4b509" alt="parrot.nvim logo" width="50%">

</div>

> [!NOTE] ⚠️
> This repository is still a work in progress, as large parts of the code are still being simplified and restructured.
> It is based on the brilliant work [gp.nvim](https://github.com/Robitx/gp.nvim) by https://github.com/Robitx.

I started this repository because a perplexity subscription provides $5 of API credits every month for free.
Instead of letting them go to waste, I modified my favorite GPT plugin, [gp.nvim](https://github.com/Robitx/gp.nvim), to meet my needs - a new Neovim plugin was born! 🔥

Unlike [gp.nvim](https://github.com/Robitx/gp.nvim), [parrot.nvim](https://github.com/frankroeder/parrot.nvim) prioritizes a seamless out-of-the-box experience by simplifying functionality and focusing solely on text generation, excluding the integration of DALL-E and Whisper.

## Features

- Persistent conversations as markdown files stored within the Neovim standard path or a user-defined location
- Custom hooks for inline text editing and to start chats with predefined prompts
- Support for multiple providers:
    + [Anthropic API](https://www.anthropic.com/api)
    + [perplexity.ai API](https://blog.perplexity.ai/blog/introducing-pplx-api)
    + [OpenAI API](https://platform.openai.com/)
    + [Mistral API](https://docs.mistral.ai/api/)
    + [Gemini API](https://ai.google.dev/gemini-api/docs)
    + Local and offline serving via [ollama](https://github.com/ollama/ollama)
    + [Groq API](https://console.groq.com)
- Flexible support for providing API credentials from various sources, such as environment variables, bash commands, and your favorite password manager CLI (lazy evaluation)
- Provide repository-specific instructions with a `.parrot.md` file with the command `PrtContext`
- **No** autocompletion and **no** hidden requests in the background to analyze your files

## Demo

Seamlessly switch between providers and models.
TODO: replace
<div align="left">
    <img src="https://github.com/frankroeder/parrot.nvim/assets/19746932/da44ebb0-e705-4ea6-b7c0-1a93c6ba034f" width="100%">
</div>

---

Trigger code completions based on comments.
<div align="left">
    <img src="https://github.com/frankroeder/parrot.nvim/assets/19746932/dc5a0790-b9a2-45ff-90c8-e67eb02f26f3" width="100%">
</div>

---

Let the parrot fix your bugs.
<div align="left">
    <img src="https://github.com/frankroeder/parrot.nvim/assets/19746932/a77fa8b2-9714-42da-bafe-645b540931ab" width="100%">
</div>

---

<details>
<summary>Rewrite a visual selection with `PrtRewrite`.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/624f265a-1f5a-41b2-a015-2631700d0d23" width="100%">
</div>
</details>

---

<details>
<summary>Append code with the visual selection as context with `PrtAppend`.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/9798759d-cb47-48a5-bcf1-3969377dbaa2" width="100%">
</div>
</details>

---

<details>
<summary>Add comments to a function with `PrtPrepend`.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/7a4f20a0-baff-413a-9ff1-62df9f53ae28" width="100%">
</div>
</details>

## Getting Started

### Dependencies
- [`neovim`](https://github.com/neovim/neovim/releases)
- [`fzf`](https://github.com/junegunn/fzf)
- [`plenary`](https://github.com/nvim-lua/plenary.nvim)
- [`ripgrep`](https://github.com/BurntSushi/ripgrep)

### lazy.nvim
```lua
{
  "frankroeder/parrot.nvim",
  tag = "v0.3.10",
  dependencies = { 'ibhagwan/fzf-lua', 'nvim-lua/plenary.nvim' },
  -- optionally include "rcarriga/nvim-notify" for beautiful notifications
  config = function()
    require("parrot").setup {
      -- Providers must be explicitly added to make them available.
      providers = {
        pplx = {
          api_key = os.getenv "PERPLEXITY_API_KEY",
          -- OPTIONAL
          -- gpg command
          -- api_key = { "gpg", "--decrypt", vim.fn.expand("$HOME") .. "/pplx_api_key.txt.gpg"  },
          -- macOS security tool
          -- api_key = { "/usr/bin/security", "find-generic-password", "-s pplx-api-key", "-w" },
        },
        openai = {
          api_key = os.getenv "OPENAI_API_KEY",
        },
        anthropic = {
          api_key = os.getenv "ANTHROPIC_API_KEY",
        },
        mistral = {
          api_key = os.getenv "MISTRAL_API_KEY",
        },
        gemini = {
          api_key = os.getenv "GEMINI_API_KEY",
        },
        groq = {
          api_key = os.getenv "GROQ_API_KEY",
        },
        ollama = {} -- provide an empty list to make provider available
      },
    }
  end,
}
```

## Commands

Below are the available commands that can be configured as keybindings.
These commands are included in the default setup.
Additional useful commands are implemented through hooks (see my example configuration).

### General
| Command                   | Description                                   |
| ------------------------- | ----------------------------------------------|
| `PrtChatNew <target>`     | open a new chat                               |
| `PrtChatToggle <target>`  | toggle chat (open last chat or new one)       |
| `PrtChatPaste <target>`   | paste visual selection into the latest chat   |
| `PrtInfo`                 | print plugin config                           |
| `PrtContext <target>`     | edits the local context file                  |
| `PrtChatFinder`           | fuzzy search chat files using fzf             |
| `PrtChatDelete`           | delete the current chat file                  |
| `PrtChatRespond`          | trigger chat respond (in chat file)           |
| `PrtStop`                 | interrupt ongoing respond                     |
| `PrtProvider <provider>`  | switch the provider (empty arg triggers fzf)  |
| `PrtModel <model>`        | switch the model (empty arg triggers fzf)     |
|  __Interactive__          | |
| `PrtRewrite`              | Rewrites the visual selection based on a provided prompt |
| `PrtAppend`               | Append text to the visual selection based on a provided prompt    |
| `PrtPrepend`              | Prepend text to the visual selection based on a provided prompt   |
| `PrtNew`                  | Prompt the model to respond in a new window   |
| `PrtEnew`                 | Prompt the model to respond in a new buffer   |
| `PrtVnew`                 | Prompt the model to respond in a vsplit       |
| `PrtTabnew`               | Prompt the model to respond in a new tab      |
|  __Example Hooks__        | |
| `PrtImplement`            | implements/translates the visual selection comment into code |
| `PrtAsk`                  | Ask the model a question                      |

With `<target>`, we indicate the command to open the chat within one of the following target locations (defaults to `toggle_target`):

- `popup`: open a popup window which can be configured via the options provided below
- `split`: open the chat in a horizontal split
- `vsplit`: open the chat in a vertical split
- `tabnew`: open the chat in a new tab

All chat commands (`PrtChatNew, PrtChatToggle`) and custom hooks support the
visual selection to appear in the chat when triggered.
Interactive commands require the user to make use of the [template placeholders](#template-placeholders)
to consider a visual selection within an API request.

## Configuration

### Options

```lua
{
    -- The provider definitions with endpoints, api keys and models used for chat summarization
    providers = ...

    -- the prefix used for all commands
    cmd_prefix = "Prt",

    -- optional parameters for curl
    curl_params = {},

    -- The directory to store persisted state information like the
    -- current provider and the selected models
    state_dir = vim.fn.stdpath("data"):gsub("/$", "") .. "/parrot/persisted",

    -- The directory to store the chats (searched with PrtChatFinder)
    chat_dir = vim.fn.stdpath("data"):gsub("/$", "") .. "/parrot/chats",

    -- Chat user prompt prefix
    chat_user_prefix = "🗨:",

    -- llm prompt prefix
    llm_prefix = "🦜:",

    -- Explicitly confirm deletion of a chat file
    chat_confirm_delete = true,

    -- Local chat buffer shortcuts
    chat_shortcut_respond = { modes = { "n", "i", "v", "x" }, shortcut = "<C-g><C-g>" },
    chat_shortcut_delete = { modes = { "n", "i", "v", "x" }, shortcut = "<C-g>d" },
    chat_shortcut_stop = { modes = { "n", "i", "v", "x" }, shortcut = "<C-g>s" },
    chat_shortcut_new = { modes = { "n", "i", "v", "x" }, shortcut = "<C-g>c" },

    -- Option to move the chat to the end of the file after finished respond
    chat_free_cursor = false,

     -- use prompt buftype for chats (:h prompt-buffer)
    chat_prompt_buf_type = false,

    -- Default target for  PrtChatToggle, PrtChatNew, PrtContext and the chats opened from the ChatFinder
    -- values: popup / split / vsplit / tabnew
    toggle_target = "vsplit",

    -- The interactive user input appearing when can be "native" for
    -- vim.ui.input or "buffer" to query the input within a native nvim buffer
    -- (see video demonstrations below)
    user_input_ui = "native",

    -- Popup window layout
    -- border: "single", "double", "rounded", "solid", "shadow", "none"
    style_popup_border = "single",

    -- margins are number of characters or lines
    style_popup_margin_bottom = 8,
    style_popup_margin_left = 1,
    style_popup_margin_right = 2,
    style_popup_margin_top = 2,
    style_popup_max_width = 160

    -- Prompt used for interactive LLM calls like PrtRewrite where {{llm}} is
    -- a placeholder for the llm name
    command_prompt_prefix_template = "🤖 {{llm}} ~ ",

    -- auto select command response (easier chaining of commands)
    -- if false it also frees up the buffer cursor for further editing elsewhere
    command_auto_select_response = true,

    -- fzf_lua options for PrtModel and PrtChatFinder when plugin is installed
    fzf_lua_opts = {
        ["--ansi"] = true,
        ["--sort"] = "",
        ["--info"] = "inline",
        ["--layout"] = "reverse",
        ["--preview-window"] = "nohidden:right:75%",
    },

    -- Enables the query spinner animation 
    enable_spinner = true,
    -- Type of spinner animation to display while loading
    -- Available options: "dots", "line", "star", "bouncing_bar", "bouncing_ball"
    spinner_type = "star",
}
```

#### Demonstrations

<details>
<summary>With `user_input_ui = "native"`, use `vim.ui.input` as slim input interface.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/014ad6ad-6367-41d1-ac57-229563540061" width="100%">
</div>
</details>

<details>
<summary>With `user_input_ui = "buffer"`, your input is simply a buffer. All of the content is passed to the API when closed.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/3390a4c1-cb60-4f2a-8bd9-0f47f6ec6e55" width="100%">
</div>
</details>

<details>
<summary>The spinner is a useful indicator for providers that take longer to respond.</summary>
<div align="left">
    <img src="https://github.com/user-attachments/assets/39828992-ad2c-4010-be66-e3a03038a980" width="100%">
</div>
</details>


### Key Bindings

This plugin provides the following default key mappings:

| Keymap       | Description                                                 |
|--------------|-------------------------------------------------------------|
| `<C-g>c`     | Opens a new chat via `PrtChatNew`                           |
| `<C-g><C-g>` | Trigger the API to generate a response via `PrtChatRespond` |
| `<C-g>s`     | Stop the current text generation via `PrtStop`              |
| `<C-g>d`     | Delete the current chat file via `PrtChatDelete`            |

Refer to my personal lazy.nvim setup for further hooks and key bindings:
https://github.com/frankroeder/dotfiles/blob/master/nvim/lua/plugins/parrot.lua

```
### Adding a new command

#### Ask a single-turn question and receive the answer in a popup window

```lua
require("parrot").setup {
    -- ...
    hooks = {
      Ask = function(parrot, params)
        local template = [[
          In light of your existing knowledge base, please generate a response that
          is succinct and directly addresses the question posed. Prioritize accuracy
          and relevance in your answer, drawing upon the most recent information
          available to you. Aim to deliver your response in a concise manner,
          focusing on the essence of the inquiry.
          Question: {{command}}
        ]]
        local model_obj = parrot.get_model("command")
        parrot.logger.info("Asking model: " .. model_obj.name)
        parrot.Prompt(params, parrot.ui.Target.popup, model_obj, "🤖 Ask ~ ", template)
      end,
    }
    -- ...
}
```

#### Start a chat with a predefined chat prompt to check your spelling.

```lua
require("parrot").setup {
    -- ...
    hooks = {
      SpellCheck = function(prt, params)
        local chat_prompt = [[
          Your task is to take the text provided and rewrite it into a clear,
          grammatically correct version while preserving the original meaning
          as closely as possible. Correct any spelling mistakes, punctuation
          errors, verb tense issues, word choice problems, and other
          grammatical mistakes.
        ]]
        prt.ChatNew(params, chat_prompt)
      end,
    }
    -- ...
}
```

### Template Placeholders

Users can utilize the following placeholders in their templates to inject
specific content into the user messages:

| Placeholder             | Content                              |
|-------------------------|--------------------------------------|
| `{{selection}}`         | Current visual selection             |
| `{{filetype}}`          | Filetype of the current buffer       |
| `{{filepath}}`          | Full path of the current file        |
| `{{filecontent}}`       | Full content of the current buffer   |
| `{{multifilecontent}}`  | Full content of all open buffers     |

Below is an example of how to use these placeholders in a completion hook, which
receives the full file context and the selected code snippet as input.


```lua
require("parrot").setup {
    -- ...
    hooks = {
    	CompleteFullContext = function(prt, params)
    	  local template = [[
          I have the following code from {{filename}}:

          ```{{filetype}}
          {{filecontent}}
          ```

          Please look at the following section specifically:
          ```{{filetype}}
          {{selection}}
          ```

          Please finish the code above carefully and logically.
          Respond just with the snippet of code that should be inserted.
          ]]
    	  local model_obj = prt.get_model()
    	  prt.Prompt(params, prt.ui.Target.append, model_obj, nil, template)
    	end,
    }
    -- ...
}
```

The placeholders `{{filetype}}` and  `{{filecontent}}` can also be used in the `chat_prompt` when
creating custom hooks calling `prt.ChatNew(params, chat_prompt)` to directly inject the whole file content.

```lua
require("parrot").setup {
    -- ...
      CodeConsultant = function(prt, params)
        local chat_prompt = [[
          Your task is to analyze the provided {{filetype}} code and suggest
          improvements to optimize its performance. Identify areas where the
          code can be made more efficient, faster, or less resource-intensive.
          Provide specific suggestions for optimization, along with explanations
          of how these changes can enhance the code's performance. The optimized
          code should maintain the same functionality as the original code while
          demonstrating improved efficiency.

          Here is the code
          ```{{filetype}}
          {{filecontent}}
          ```
				]]
        prt.ChatNew(params, chat_prompt)
      end,
    }
    -- ...
}
```

## Roadmap

- Add status line integration/ notifications for summary of tokens used or money spent
- Improve the documentation
- Create a tutorial video
- Reduce overall code complexity and improve robustness

## FAQ

- I am encountering errors related to the state.
    > If the state is corrupted, simply delete the file `~/.local/share/nvim/parrot/persisted/state.json`.
- The completion feature is not functioning, and I am receiving errors.
    > Ensure that you have an adequate amount of API credits and examine the log file `~/.local/state/nvim/parrot.nvim.log` for any errors.
- I have discovered a bug, have a feature suggestion, or possess a general idea to enhance this project.
    > Everyone is invited to contribute to this project! If you have any suggestions, ideas, or bug reports, please feel free to submit an issue.

## Related Projects

- [robitx/gp.nvim](https://github.com/Robitx/gp.nvim)
- [huynle/ogpt.nvim](https://github.com/huynle/ogpt.nvim)

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=frankroeder/parrot.nvim&type=Date)](https://star-history.com/#frankroeder/parrot.nvim&Date)
