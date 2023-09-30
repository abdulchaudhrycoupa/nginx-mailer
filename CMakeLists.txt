cmake_minimum_required(VERSION 3.15)
project(nginx)

# Set C standard
set(CMAKE_C_STANDARD 99)

include(ExternalProject)

# Set variables
set(NGINX_VERSION "1.25.2")
set(NGINX_URL "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz")
set(NGINX_ARCHIVE "${CMAKE_BINARY_DIR}/nginx-${NGINX_VERSION}.tar.gz")
set(NGINX_SOURCE_DIR "${CMAKE_BINARY_DIR}/nginx-${NGINX_VERSION}")

# Download NGINX source code
if(NOT EXISTS "${NGINX_ARCHIVE}")
    message(STATUS "Downloading NGINX ${NGINX_VERSION}")
    file(DOWNLOAD "${NGINX_URL}" "${NGINX_ARCHIVE}" 
         SHOW_PROGRESS TIMEOUT 60 STATUS status)
    list(GET status 0 status_code)
    if(NOT status_code EQUAL 0)
        message(FATAL_ERROR "Failed to download NGINX: ${status}")
    endif()
endif()

# Extract NGINX source code
if(NOT EXISTS "${NGINX_SOURCE_DIR}")
    message(STATUS "Extracting NGINX ${NGINX_VERSION}")
    execute_process(COMMAND ${CMAKE_COMMAND} -E tar xzf "${NGINX_ARCHIVE}"
                    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}"
                    RESULT_VARIABLE result)
    if(NOT result EQUAL 0)
        message(FATAL_ERROR "Failed to extract NGINX: ${result}")
    endif()
endif()

# Define external project
ExternalProject_Add(nginx
    PREFIX ${NGINX_PREFIX}
    URL ${NGINX_URL}
    SOURCE_DIR ${NGINX_SOURCE_DIR}
    BINARY_DIR ${NGINX_BINARY_DIR}
    CONFIGURE_COMMAND ${CMAKE_COMMAND} -E env "SHELL=/bin/bash" ${NGINX_SOURCE_DIR}/configure --prefix=${NGINX_BINARY_DIR}/install
    BUILD_COMMAND $(MAKE)
    INSTALL_COMMAND ""  # Empty string to skip install step
    BUILD_IN_SOURCE 1
)

