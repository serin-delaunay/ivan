file(GLOB FELIB_SOURCES Include/*.h Source/*.cpp)

if(${CMAKE_CXX_COMPILER_ID} STREQUAL "GNU")
  add_definitions(-DGCC) # TODO: Use __GNUC__ in the code instead.
endif()

add_library(FeLib ${FELIB_SOURCES})

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_LIST_DIR})
find_package(SDL2)
if(SDL2_FOUND)
  message(STATUS "Using SDL2")
else()
  message(WARNING "SDL2 not found")
  find_package(SDL 1.2.0 REQUIRED)
  message(STATUS "Using SDL1")
endif()

if(MSVC)
  # Not very pretty solution. This finds SDL2.dll from SDL2.lib path, so that it can be installed where ivan.exe will end up.
  message("SDL2_LIBRARY ${SDL2_LIBRARY}")
  list(GET SDL2_LIBRARY 0 sdl_lib_dir)
  message("sdl_lib_dir ${sdl_lib_dir}")
  get_filename_component(sdl_lib_dir "${sdl_lib_dir}" DIRECTORY)
  message("sdl_lib_dir ${sdl_lib_dir}")
  install(FILES "${sdl_lib_dir}/SDL2.dll" DESTINATION "ivan")
endif()

if(MINGW)
  # Not very pretty solution. This finds SDL2.dll from SDL2.lib path, so that it can be installed where ivan.exe will end up.
  message("SDL2_LIBRARY ${SDL2_LIBRARY}")
  list(GET SDL2_LIBRARY 2 sdl_lib_dir)
  message("sdl_lib_dir ${sdl_lib_dir}")
  get_filename_component(sdl_lib_dir "${sdl_lib_dir}" DIRECTORY)
  message("sdl_lib_dir ${sdl_lib_dir}")
  install(FILES "${sdl_lib_dir}/../bin/SDL2.dll" DESTINATION "ivan")

  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static-libgcc -static-libstdc++")
endif()

target_include_directories(FeLib PUBLIC Include)
target_include_directories(FeLib SYSTEM PUBLIC ${SDL2_INCLUDE_DIR})
target_link_libraries(FeLib ${SDL2_LIBRARY})
