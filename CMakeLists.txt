cmake_minimum_required(VERSION 3.31.4)

project(Lua VERSION 5.4.1 LANGUAGES C)

option(BUILD_SHADED_LIBS "Build shared library" OFF)
option(LUA_COMPAT_5_3 "Enable Lua 5.3 compatibility" ON)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O2")
if (LUA_COMPAT_5_3)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -LUA_COMPAT_5_3")
endif()

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
  add_definitions(-DLUA_USE_LINUX)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  add_definitions(-DLUA_USE_MACOSX -DLUA_USE_READLINE)
elseif (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DWIN32")
endif()

file(GLOB LUA_CORE_SRC
    src/lapi.c
    src/lcode.c
    src/lctype.c
    src/ldebug.c
    src/ldo.c
    src/ldump.c
    src/lfunc.c
    src/lgc.c
    src/llex.c
    src/lmem.c
    src/lobject.c
    src/lopcodes.c
    src/lparser.c
    src/lstate.c
    src/lstring.c
    src/ltable.c
    src/ltm.c
    src/lundump.c
    src/lvm.c
    src/lzio.c
)

file(GLOB LUA_LIB_SRC
  src/lauxlib.c
  src/lbaselib.c
  src/lcorolib.c
  src/ldblib.c
  src/liolib.c
  src/lmathlib.c
  src/loadlib.c
  src/loslib.c
  src/lstrlib.c
  src/ltablib.c
  src/lutf8lib.c
  src/linit.c
)

add_library(lualib STATIC ${LUA_CORE_SRC} ${LUA_LIB_SRC})
add_executable(lua_interp src/lua.c)
target_link_libraries(lua_interp lualib)
add_executable(luac src/luac.c)
target_link_libraries(luac lualib)

set_target_properties(lua_interp PROPERTIES COMPILE_FLAGS "-Os")
set_target_properties(lualib PROPERTIES COMPILE_FLAGS "-Os")
set_target_properties(luac PROPERTIES COMPILE_FLAGS "-Os")
