project('PDcurses', 'c',
  version: '3.9',
  meson_version: '>= 0.47.0')

os = target_machine.system()

incdir = [include_directories('.')]
# PDCURSES_HEADERS	= files('curspriv.h', 'curses.h')
# PANEL_HEADER = files('panel.h')

subdir('pdcurses')

deps = []
if os == 'windows'
  subdir('wincon')
elif os == 'linux'
  # requires libxt-dev libxpm-dev libxaw7-dev
  subdir('x11')
  incdir += include_directories('/usr/include/X11')
  deps = [dependency('xaw7'), dependency('xpm'), dependency('xmu')]
else
  error('We have not yet added ' + os + ' to Meson for PDcurses.')
endif

curses_lib = library('pdcurses',
  sources: [lib_src, pdc_src],
  include_directories: incdir,
  dependencies: deps)

subdir('demos')