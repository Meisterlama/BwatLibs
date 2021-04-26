file(GLOB_RECURSE LUA_SRC
        src/*.c)

add_library(lua STATIC ${LUA_SRC})
target_include_directories(lua include)