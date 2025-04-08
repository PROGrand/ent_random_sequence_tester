cmake_minimum_required(VERSION 3.10)
project(ent VERSION 1.4)

# Compiler and flags
set(CMAKE_C_COMPILER gcc)
set(CMAKE_C_FLAGS "-O3 -Wall")

# Source files
set(SOURCES
    ent.c
    iso8859.c
    randtest.c
    chisq.c
)

# Header files
set(HEADERS
    iso8859.h
    randtest.h
)

# Executable target
add_executable(ent ${SOURCES})

# Link libraries
target_link_libraries(ent m)

# Custom targets

# Distribution target
add_custom_target(dist
    COMMAND rm -rf ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}
    COMMAND mkdir ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}
    COMMAND tar cfvz ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}.tar.gz ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}
    COMMAND rm -rf ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}
    COMMENT "Creating distribution tarball"
)

# Zip target
add_custom_target(zip
    COMMAND rm -f random.zip
    COMMAND zip -pr random.zip ${CMAKE_CURRENT_SOURCE_DIR}
    COMMENT "Creating zip file"
)

# Test target
add_custom_target(check
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png > entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -c ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -fc ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -b ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -bc ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -t ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -ct ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -ft ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -bt ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/ent -bct ${CMAKE_CURRENT_SOURCE_DIR}/figures/entitle.png >> entest.bak
    COMMAND diff ${CMAKE_CURRENT_SOURCE_DIR}/entest.mas entest.bak || echo '** ent:  Regression test failed. **'
    COMMAND rm entest.bak
    COMMENT "Tests OK"
)

# Clean target
add_custom_target(clean_all
    COMMAND rm -f *.o ent ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION} ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION}.tar.gz random.zip
    COMMENT "Cleaning build files"
)

# Lint target
add_custom_target(lint
    COMMAND lint *.c
    COMMENT "Running lint checks"
)