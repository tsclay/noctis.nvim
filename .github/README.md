# Noctis.nvim

Forked from [talha-akram/noctis](https://github.com/talha-akram/noctis.nvim). See the README there for the basics.

[This comment on \#2293](https://github.com/nvim-treesitter/nvim-treesitter/issues/2293#issuecomment-1900679583)
 of the nvim-treesitter repo revealed that the capture names that would
affect Markdown highlighting were changed.

This caused Markdown files to get no syntax highlighting with Noctis.

Viewing a markdown file with noctis.nvim as is (Using noctis_uva):

![No highlighting here](./no_hl.png)

After making the change that inspired this fork:

![We got highlighting](./yes_hl.png)

## Why?

Breaking changes, like the one noted above, are annoying. Immediate resolution
means diving into the plugin source on your machine and tweaking the code, like
what I did initially with noctis.nvim. *Don't forget to make the same change on
your other machines.* 

The long-term solution is for the colorscheme maintainer to do
the code tweaking and your plugin manager would prompt you to update.

However, what if you don't like the choices made or want to override existing
highlight config? This fork of noctis.nvim answers those questions.

## Setup

This fork allows you to provide overrides to the Noctis
colorscheme setter before Neovim sets the colorscheme.

**Lazy** 

```lua
{
  'tsclay/noctis.nvim',
  init = function()
    require('noctis_theme').setup({
      NormalBold = { fg = 'blue', bg = 'none', bold = true },
      NormalItalic = { fg = 'blue', bg = 'none', italic = true },
    }, {
        -- To give some missing highlights for *.md
      ['@markup.underline'] = { link = 'Underlined' },
      ['@markup.italic'] = { link = 'NormalItalic' },
      ['@markup.strong'] = { link = 'NormalBold' },
      ['@markup.heading'] = { link = 'Title' },
      ['@markup.raw'] = { link = 'Comment' },
      ['@markup.link'] = { link = 'Identifier' },
      ['@markup.link.url'] = { link = 'helpURL' },
      ['@markup.link.label'] = { link = 'Identifier' },
      ['@markup.list'] = { link = 'Todo' },
    })
  end,
},
```

> I use lazy.nvim for plugin management. Feel free to contribute configs for others!

### What it does

The `setup` function takes two arguments, both of the same type aliased as `UserColorConfig`.

- `hl_groups`
- `override`

`UserColorConfig` is a table where keys are strings and values are tables of `HighlightDefinition`.

```lua
---@class (exact) HighlightDefinition
---@field fg? string --color name or "#RRGGBB", see note.
---@field bg? string --color name or "#RRGGBB", see note.
---@field sp? string --color name or "#RRGGBB"
---@field blend? integer -- between 0-100
---@field bold? boolean
---@field standout? boolean
---@field underline? boolean
---@field undercurl? boolean
---@field underdouble? boolean
---@field underdotted? boolean
---@field underdashed? boolean
---@field strikethrough? boolean
---@field italic? boolean
---@field reverse? boolean
---@field nocombine? boolean
---@field link? string --name of another highlight group to link to, see |:hi-link|.
```

This class definition comes from the docs for `vim.api.nvim_set_hl` (see `h: nvim_set_hl()`).

>![NOTE]
>
> The `default` field is excluded since anything user-defined could override plugin-level options.

Taking the base noctis palette for example (`colors/noctis.lua`):

```lua
local palette = {
  bg0           = '#0E2428',  -- Text
  bg1           = '#1A3C43',  -- CursorLine/Sign
  bg2           = '#0A1C1F',  -- Pmenu
  bg3           = '#1A3C43',  -- StatusLine
  bg4           = '#0A1C1F',  -- Tabline/Winbar
  bg_red        = '#3A2727',
  bg_green      = '#2B4234',
  bg_blue       = '#193B41',
  fg            = '#B7C9CC',
  red           = '#D17B9A',
  orange        = '#D66D41',
  yellow        = '#DDB988',
  green         = '#78E0A6',
  cyan          = '#4CA1B3',
  blue          = '#64AAE4',
  purple        = '#6D61E3',
  grey          = '#455B5F',
  light_grey    = '#64848A',
  light_yellow  = '#DDB988',
  none          = 'NONE',
}
```

Referring to the lazy.nvim setup code above, the first table:

```lua
{
  NormalBold = { fg = 'blue', bg = 'none', bold = true },
  NormalItalic = { fg = 'blue', bg = 'none', italic = true },
}
```

Will become the following and be merged with the plugin-level highlight groups:

```lua
{
  NormalBold = { fg = '#64AAE4', bg = 'NONE', bold = true },
  NormalItalic = { fg = '#64AAE4', bg = 'NONE', italic = true },
}
```

The second table is handled the same way. It is handled after `hl_groups` is evaluated,
so referring to a group defined in `hl_groups` with `link` is safe.

```lua
{
  ['@markup.underline'] = { link = 'Underlined' },
  ['@markup.italic'] = { link = 'NormalItalic' },
  ['@markup.strong'] = { link = 'NormalBold' },
  ['@markup.heading'] = { link = 'Title' },
  ['@markup.raw'] = { link = 'Comment' },
  ['@markup.link'] = { link = 'Identifier' },
  ['@markup.link.url'] = { link = 'helpURL' },
  ['@markup.link.label'] = { link = 'Identifier' },
  ['@markup.list'] = { link = 'Todo' },
}
```

## TODO

- [ ] Determine if same result if only one table of overrides/additions given
- [ ] Review README


