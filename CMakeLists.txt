cmake_minimum_required(VERSION 3.12.0 FATAL_ERROR)


##############################################
#Options


option(BUILD_TESTS "Build tests." OFF)
set(SOUL_ROOT "${CMAKE_CURRENT_SOURCE_DIR}/../Soul Engine" CACHE STRING "The directory location of Soul Engine")
set(SOUL_BUILD "${CMAKE_CURRENT_SOURCE_DIR}/../Soul Engine/Build" CACHE STRING "The build location of Soul Engine")


##############################################
#Project 

include(${CMAKE_CURRENT_SOURCE_DIR}/CMake/Hunter/HunterGate.cmake)

HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.23.27.tar.gz"
    SHA1 "70d75a25f895df3da00d8382417e8c2a08c93173"
    FILEPATH ${CMAKE_CURRENT_SOURCE_DIR}/CMake/Hunter/config.cmake
)

project(SoulEditor
	VERSION 0.0.1
	DESCRIPTION "Soul Engine editor and project interface."
	LANGUAGES CXX
)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/CMake)

if(MSVC)
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /SUBSYSTEM:WINDOWS /ENTRY:mainCRTStartup")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS} /SUBSYSTEM:WINDOWS /ENTRY:mainCRTStartup")
endif()

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${PROJECT_NAME})


##############################################
#Sources


file(GLOB_RECURSE PROJECT_HEADERS Source/*.h Include/*.h)
file(GLOB_RECURSE PROJECT_SOURCES Source/*.cpp)
set (PROJECT_FILES 
    ${PROJECT_HEADERS} 
    ${PROJECT_SOURCES} 
)


include(source_groups)
source_groups(${PROJECT_FILES})


##############################################
#Dependencies


#find_package(SoulEngine REQUIRED)
add_subdirectory(${SOUL_ROOT} ${SOUL_BUILD})

#GLM target
hunter_add_package(glm)
find_package(glm REQUIRED)

##############################################
#Targets


add_executable(${PROJECT_NAME} ${PROJECT_FILES})

set_target_properties(${PROJECT_NAME} 
    PROPERTIES 
        LINKER_LANGUAGE CXX
        CXX_EXTENSIONS OFF  
        CXX_STANDARD 17
)

target_include_directories(${PROJECT_NAME}
    PUBLIC 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/Include>
        $<INSTALL_INTERFACE:Include>
    PRIVATE
        Source
)

target_link_libraries(${PROJECT_NAME}
    PUBLIC

    PRIVATE		
        glm
        Soul::SoulEngine
)


##############################################
#Tests


if(BUILD_TESTS)
 

endif()