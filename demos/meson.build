
foreach n: ['testcurs', 'ozdemo', 'xmas', 'firework', 'ptest', 'rain', 'worm']

  executable(n, n+'.c',
    include_directories: [incdir],
    link_with: curses_lib)

endforeach

executable('tuidemo',
  sources: ['tui.c', 'tuidemo.c'],
  include_directories: [incdir],
  link_with: curses_lib)