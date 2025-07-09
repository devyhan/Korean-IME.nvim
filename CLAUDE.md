# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Korean-IME.nvim is a Neovim plugin that provides Korean input method editor (IME) functionality. It converts English keystrokes to Korean characters (Hangul) and supports Hanja (Chinese character) input, useful when Korean keyboards are unavailable or there are terminal input method bugs.

## Development Commands

### Code Formatting
- `stylua .` - Format Lua code using StyLua (configuration in `.stylua.toml`)
- Uses 2-space indentation, spaces instead of tabs

### Testing
- No automated testing framework - relies on manual testing
- Test Korean input functionality in Neovim manually
- Verify Hanja conversion works with external `hanja.txt` file

## Architecture Overview

### Core Components

1. **`lua/korean_ime/init.lua`** - Main entry point and public API
   - Exports setup(), change_mode(), get_mode(), convert_hanja()
   - Handles plugin initialization and lazy loading

2. **`lua/korean_ime/hangul.lua`** - Core Korean input logic
   - Implements KS X 5002 two-layered scheme (Dubeolsik standard)
   - Manages syllable composition state machine with complex lookup tables
   - Handles keyboard mappings and autocmds for Korean input

3. **`lua/korean_ime/hanja.lua`** - Chinese character (Hanja) support
   - Lazy-loads Hanja database from `hanja.txt`
   - Provides completion interface for Hanja selection
   - Converts Hangul to corresponding Hanja characters

4. **`lua/korean_ime/config.lua`** - Configuration management
   - Defines user configuration options
   - Manages default settings for Hanja database paths and limits

5. **`lua/korean_ime/notify.lua`** - Notification system
   - Unified notification interface
   - Supports VSCode-Neovim and nvim-notify plugin integration

### Key Design Patterns

- **Lazy Loading**: Plugin loads only when Korean input is needed
- **State Machine**: Complex syllable composition using state transitions
- **Modular Design**: Clear separation between Hangul input and Hanja conversion
- **Optional Dependencies**: Graceful degradation when optional plugins unavailable
- **Multi-mode Support**: Works in both insert mode and terminal mode

## Development Guidelines

### Korean Character Composition
- Uses extensive lookup tables for Korean character assembly
- Jamo (basic Korean letters) are combined into syllables following Korean grammar rules
- State machine tracks current composition state (initial, medial, final consonants)

### Hanja Database
- Requires external `hanja.txt` file from libhangul project
- Database is lazy-loaded only when Hanja functionality is used
- Format: Hangul followed by Hanja characters with descriptions

### Integration Points
- **lualine.nvim**: Mode display integration
- **nvim-notify**: Enhanced notifications
- **vscode-neovim**: VSCode environment support

## Important Implementation Details

### Character Encoding
- All Korean text handling uses UTF-8 encoding
- Proper Unicode handling for Korean character composition
- Supports full Korean character set including complex syllables

### Input Method Switching
- Mode switching between English and Korean input
- Persistent mode state across editor sessions
- Visual feedback for current input mode
- Works consistently in insert mode and terminal mode

### Autocmd Management
- Careful setup/teardown of autocmds for Korean input
- Mode-specific behavior in different Neovim contexts (insert, terminal)
- Proper cleanup when switching between modes
- Terminal-specific events (TermOpen, TermClose) for proper state management