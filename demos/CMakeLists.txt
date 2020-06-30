foreach(f testcurs ozdemo xmas firework ptest rain worm)
  add_executable(${f} ${f}.c)
  target_link_libraries(${f} pdcurses)
endforeach()

add_executable(tuidemo tui.c tuidemo.c)
target_link_libraries(tuidemo pdcurses)
