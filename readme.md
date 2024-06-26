```
require "core"

local custom_init_path = vim.api.nvim_get_runtime_file("lua/custom/init.lua", false)[1]

if custom_init_path then
  dofile(custom_init_path)
end

require("core.utils").load_mappings()

local lazypath = vim.fn.stdpath "data" .. "/lazy/lazy.nvim"


-- bootstrap lazy.nvim!
if not vim.loop.fs_stat(lazypath) then
  require("core.bootstrap").gen_chadrc_template()
  require("core.bootstrap").lazy(lazypath)
end

dofile(vim.g.base46_cache .. "defaults")
vim.opt.rtp:prepend(lazypath)
require "plugins"


require'lspconfig'.pyright.setup{}
require'lsp_lines'.setup{}
require'lspconfig'.tsserver.setup{}
require'lspconfig'.omnisharp.setup{
  filetypes = {"cs", "vb", "cshtml", "razor"},
  cmd = {"omnisharp", "--languageserver", "--hostPID", tostring(vim.fn.getpid())}
}

if vim.loop.os_uname().sysname == "Windows_NT" then
   require('nvim-treesitter.install').compilers = { "clang" }
end

vim.api.nvim_exec([[
  function! FormatJS()
    execute '%!prettier --stdin-filepath %'
    write
  endfunction

  function! FormatPython()
    execute '%!black -q -'
    write
  endfunction

  augroup Format
    autocmd!
    autocmd BufWritePre *.js call FormatJS()
    autocmd BufWritePre *.py call FormatPython()
  augroup END
]], false)


require'lspconfig'.rust_analyzer.setup{}
```
