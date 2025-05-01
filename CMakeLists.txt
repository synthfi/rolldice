cmake_minimum_required(VERSION 3.26)

project(rolldice
  VERSION 1.16
  LANGUAGES C
  DESCRIPTION "rolls virtual dice")

add_executable(rolldice rolldice.c main.c)
target_include_directories(rolldice PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(rolldice PUBLIC readline)

# man page with sed
add_custom_command(
  OUTPUT rolldice.6.gz
  COMMAND sed s/__VERSION__/${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}/ ${CMAKE_CURRENT_SOURCE_DIR}/rolldice.6.src > rolldice.6
  COMMAND gzip -n -9 -c ${CMAKE_CURRENT_BINARY_DIR}/rolldice.6 > rolldice.6.gz
  COMMENT "Running sed to generate man page"
  VERBATIM
)
add_custom_target(generate_man ALL
  DEPENDS rolldice.6.gz)

include(GNUInstallDirs)
set(CMAKE_INSTALL_GAMEDIR "${CMAKE_INSTALL_PREFIX}/games"
  CACHE PATH "Installation directory for games" FORCE)
install(TARGETS rolldice
  RUNTIME DESTINATION ${CMAKE_INSTALL_GAMEDIR})
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/rolldice.6.gz
  DESTINATION ${CMAKE_INSTALL_MANDIR}/man6)

# CPack options
set(CPACK_PACKAGE_VENDOR "Stevie Strickland")
set(CPACK_PACKAGE_CONTACT "sstrickl@gmail.com")
set(CPACK_PACKAGE_HOMEPAGE_URL "https://github.com/sstrickl/rolldice")
set(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION")

set(CPACK_RPM_PACKAGE_LICENSE "GPL-2.0-only")
set(CPACK_RPM_PACKAGE_REQUIRES "readline")

set(CPACK_DEBIAN_PACKAGE_DEPENDS "libreadline8 | libreadline7")
include(CPack)
