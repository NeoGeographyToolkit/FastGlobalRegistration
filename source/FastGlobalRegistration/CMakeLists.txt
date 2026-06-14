add_library(FastGlobalRegistrationLib ${FastGlobalRegistration_LINK_MODE} app.cpp app.h)
add_executable(FastGlobalRegistration main.cpp)
add_executable(Evaluation evaluation.cpp)
TARGET_LINK_LIBRARIES(FastGlobalRegistration FastGlobalRegistrationLib)
TARGET_LINK_LIBRARIES(Evaluation FastGlobalRegistrationLib)

# Install the library and its public header. These were previously copied by
# hand from the build tree; install them properly so a plain "make install"
# places them in the prefix.
install(TARGETS FastGlobalRegistrationLib
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
install(FILES app.h DESTINATION include/FastGlobalRegistration)

# On macOS, CMake's install step edits the library with install_name_tool,
# which invalidates its code signature; recent macOS then refuses to load it.
# Re-sign ad-hoc after install. No-op on Linux.
if (APPLE)
  install(CODE "
    file(GLOB signlibs \"\${CMAKE_INSTALL_PREFIX}/lib/libFastGlobalRegistrationLib*.dylib\")
    foreach(f IN LISTS signlibs)
      execute_process(COMMAND codesign --force --sign - \${f} ERROR_QUIET)
    endforeach()
  ")
endif()
