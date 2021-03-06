##---------------------------------------------------------------------------
## Author:      Sean D'Epagnier
## Copyright:   
## License:     GPLv3
# ---------------------------------------------------------------------------
# Frontend Author:      jongough aka. Jon Gough    Frontend Version:  1.0.172.0
# ---------------------------------------------------------------------------
## ----- When changing this file do NOT change the order in which sections occur        ----- ##
## ----- Changes should only be made between the section blocks that identify where     ----- ##
## ----- these changes should be. The whole configuration process relies on this        ----- ##
## ----- sequence to be successful                                                      ----- ##
##
## ----- Modify section below to include all the details for your plugin ----- ##
##

set(CMLOC "CMakeLists: ")

# define minimum cmake version
cmake_minimum_required(VERSION 3.1.1)
if(COMMAND cmake_policy)
    if(POLICY CMP0043)
        cmake_policy(SET CMP0043 NEW)
    endif(POLICY CMP0043)
    cmake_policy(SET CMP0048 NEW)
    if(POLICY CMP0077)
        cmake_policy(SET CMP0077 NEW)
    endif(POLICY CMP0077)
endif(COMMAND cmake_policy)

# define plugin name, owner and versions
set(VERBOSE_NAME "Plots")
set(COMMON_NAME "Plots")
set(TITLE_NAME "PLOTS")
set(PACKAGE_CONTACT  "Sean D'Epagnier ")
set(PACKAGE "plots")
set(SHORT_DESCRIPTION "Plots PlugIn for OpenCPN")
set(LONG_DESCRIPTION "Plots PlugIn for OpenCPN Plot speed and course over ground to make the result of small adjustments evident.")

set(VERSION_MAJOR "2")
set(VERSION_MINOR "2")
set(VERSION_PATCH "6")
set(VERSION_TWEAK "0")
set(VERSION_DATE "30-6-2021") # DD/MM/YYYY format
set(OCPN_MIN_VERSION "ov50")
set(OCPN_API_VERSION_MAJOR "1")
set(OCPN_API_VERSION_MINOR "16")
set(TP_COMMENT "  * Release for O5 using CI")

set(PARENT "opencpn")

# The next line allows setup of a local webserver with git for testing purposes.
# The default is shown below, but it is done in cmake/pluginconfigure.cmake now.
# This default is needed to build locally and to build flatpak.
#set(GIT_REPOSITORY_SERVER "github.com")

#No longer needed, queried.
#set(GIT_USER "rgleason")
#set(GIT_REPOSITORY_NAME "testplugin_pi")

# Specifies cloudsmith repository sufficies
set(PROD "prod") # Standard Repos
set(BETA "beta") # Standard Repos
set(ALPHA "alpha") # Standard Repos

# set this if your cloudsmith repository name does not match your git repository name 
#set(CLOUDSMITH_USER "rick-gleason")
set(CLOUDSMITH_USER "opencpn")

# Set if yourCloudsmith Base Repository name does not match your Git Repository name.
# set(CLOUDSMITH_BASE_REPOSITORY "opencpn-plugins") #Rick's customization
set (CLOUDSMITH_BASE_REPOSITORY "plots")  #without the pi 

set(XML_INFO_URL "https://opencpn.org/OpenCPN/plugins/sweepplot.html")
set(XML_SUMMARY ${SHORT_DESCRIPTION})
set(XML_DESCRIPTION ${LONG_DESCRIPTION})

##
## ----- Modify section above to include all the details for your plugin ----- ##
## ----- Modify section below if there are special requirements for the plugin ----- ##
##

option(PLUGIN_USE_SVG "Use SVG graphics" ON)

set(CMAKE_CXX_STANDARD 11)

# Prefer libGL.so to libOpenGL.so, see CMP0072
set(OpenGL_GL_PREFERENCE "LEGACY")
# Don't use local version of GLU library
set(USE_LOCAL_GLU FALSE)
option(USE_GL "Enable OpenGL support" ON)
message(STATUS "${CMLOC}USE_GL: ${USE_GL}")

if("${CMAKE_BUILD_TYPE}" STREQUAL "")
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose the type of build" FORCE)
endif("${CMAKE_BUILD_TYPE}" STREQUAL "")
set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "" "Debug" "Release" "RelWithDebInfo" "MinSizeRel")
message(STATUS "${CMLOC}Build type: ${CMAKE_BUILD_TYPE}")

##
## ----- The statements below are used to setup standard variables that are required by the CMAKE process - do not remove ----- ##
##

project(${PACKAGE})

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# ==================================
include("PluginSetup")
# ==================================
##
## ----- The above is used to setup standard variables that are required by the CMAKE process - do not remove ----- ##
## ----- Modify section below if there are special requirements for the plugin ----- ##
##

set(CMAKE_VERBOSE_MAKEFILE "Activate verbose mode for make files" ON)

option(Plugin_CXX11 "Use c++11" OFF)

##
## ----- Modify section above if there are special requirements for the plugin ----- ##
## ----- do not change next section - needed to configure build process ----- ##
##
# ==================================
include("PluginConfigure")
# ==================================
##
## ----- do not change section above - needed to configure build process ----- ##
# ----- Change below to match project requirements for source, headers, etc. ----- ##
#

add_definitions(-DUSE_S57)

SET(SRC_PLOTS
            src/plots_pi.cpp
            src/PlotsDialog.cpp
            src/PreferencesDialog.cpp
            src/PlotConfigurationDialog.cpp
            src/AboutDialog.cpp
            src/History.cpp
            src/Plot.cpp
            src/PlotsUI.cpp
            src/icons.cpp
	)
	
SET(HDR_PLOTS
            src/PlotsUI.h
            src/History.h
            src/PlotsDialog.h
			src/PlotConfigurationDialog.h
			src/PreferencesDialog.h
            src/AboutDialog.h
			src/wx28compat.h
    )
			
set(OCPNSRC ocpnsrc/TexFont.cpp)

set(OCPNHDRS
    ocpninclude/ocpn_plugin.h
    ocpninclude/pluginmanager.h
    ocpninclude/TexFont.h
    libs/ocpn-api/ocpn_plugin.h
)

add_definitions(-DPLUGIN_USE_SVG)

#set(EXTINCLUDE_DIR ${EXTINCLUDE_DIR} extinclude libs/ocpn-api/)
#if(APPLE)
#    option(PI_JSON_SCHEMA_VALIDATOR "Use JSON Schema validator" OFF)
#else(APPLE)
#    option(PI_JSON_SCHEMA_VALIDATOR "Use JSON Schema validator" ON)
#endif(APPLE)
#
#if(PI_JSON_SCHEMA_VALIDATOR)
#    message(STATUS "${CMLOC}Using JSON Schema validation - warning this changes the 'flavour' of the executable and it may not be compatible with OpenCPN")
#    add_definitions(-DPI_JSON_SCHEMA_VALIDATOR)
#    message(STATUS "${CMLOC}Adding JSON Schema validation")
#
#    set(EXTSRC ${EXTSRC} extsrc/json-schema-validator/json-schema-draft7.json.cpp extsrc/json-schema-validator/json-uri.cpp extsrc/json-schema-validator/json-validator.cpp)
#    set(EXTINCLUDE_DIR ${EXTINCLUDE_DIR} extinclude/nlohmann extinclude)
#    #include_directories(BEFORE ${PROJECT_SOURCE_DIR}/extinclude/json-schema-validator)
#else(PI_JSON_SCHEMA_VALIDATOR)
#    message(STATUS "${CMLOC}Not using JSON Schema validation - warning invalid JSON 'may' cause unexpected crashes")
#endif(PI_JSON_SCHEMA_VALIDATOR)

include_directories(BEFORE ${PROJECT_SOURCE_DIR}/include)
include_directories(BEFORE ${PROJECT_SOURCE_DIR}/ocpninclude)
include_directories(BEFORE ${PROJECT_SOURCE_DIR}/ocpnsrc/glu/include)
#include_directories(BEFORE ${PROJECT_SOURCE_DIR}/${EXTINCLUDE_DIR})
include_directories(BEFORE ${PROJECT_SOURCE_DIR}/libs/)
include_directories(BEFORE ${PROJECT_SOURCE_DIR}/libs/ocpn-api)
include_directories(BEFORE ${PROJECT_SOURCE_DIR}/libs/wx/include)


# ==================================
include("PluginNMEA")
include("PluginJSON")
# ==================================


# ==================================
target_sources(${PACKAGE_NAME} PUBLIC ${SRC_PLOTS} ${HDR_PLOTS} ${SRC_NMEA0183} ${SRC_JSON})
# ==================================
#add_library(${PACKAGE_NAME} SHARED ${SRC_PLOTS} ${HDR_PLOTS} ${SRC_NMEA0183} ${SRC_JSON})


if(NOT OCPN_FLATPAK_CONFIG)
    # Build environment not available when flatpak is being configured so following statements will not work
    message(STATUS "${CMLOC}Adding target link libraries to ${PACKAGE_NAME}")
    add_subdirectory(libs/tinyxml)
    target_link_libraries(${PACKAGE_NAME} ocpn::tinyxml)
endif(NOT OCPN_FLATPAK_CONFIG)




add_definitions(-DTIXML_USE_STL)

if(UNIX AND NOT APPLE AND NOT QT_ANDROID)
    if(PI_JSON_SCHEMA_VALIDATOR)
        # Change OS/ABI to be UNIX - System V to make it run with OCPN
        set(lib_name lib${PACKAGE_NAME}.so)
        message(STATUS "${CMLOC}lib-name: ${lib_name}")
        find_file(elfedit elfedit)
        if(NOT (${elfedit} STREQUAL "elfedit-NOTFOUND"))
            message(STATUS "${CMLOC}Will ensure OS/ABI is set to 0 to match OCPN")
            add_custom_command(
                TARGET ${PACKAGE_NAME}
                POST_BUILD
                WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                DEPENDS ${PACKAGE_NAME}
                COMMENT " Running post build action on ${lib_name}."
                COMMAND sh -c 'elfedit --input-osabi=Linux --output-osabi=none ${lib_name} || (echo 0) ')
        else(NOT (${elfedit} STREQUAL "elfedit-NOTFOUND"))
            message(STATUS "${CMLOC}Cannot correct OS/ABI to match OCPN if it is generated incorrectly. Package binutils needed")
        endif(NOT (${elfedit} STREQUAL "elfedit-NOTFOUND"))
    endif(PI_JSON_SCHEMA_VALIDATOR)
endif(UNIX AND NOT APPLE AND NOT QT_ANDROID)

##
## ----- Change above to match project requirements ----- ##
## ----- do not change next section - needed to configure build process ----- ##

# Needed for android builds
if(QT_ANDROID)
    include_directories(BEFORE ${qt_android_include})
endif(QT_ANDROID)

# ==================================
# Needed for all builds
include("PluginInstall")
include("PluginLocalization")
include("PluginPackage")
# ==================================
##
## ----- do not change section above - needed to configure build process ----- ##


#message(STATUS "${CMLOC}include directories: ")
#get_property(dirs DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY INCLUDE_DIRECTORIES)
#foreach(dir ${dirs})
#    message(STATUS "${CMLOC}dir='${dir}'")
#endforeach()
