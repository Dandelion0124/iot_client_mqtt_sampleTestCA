#Copyright (c) Microsoft. All rights reserved.
#Licensed under the MIT license. See LICENSE file in the project root for full license information.

#this is CMakeLists for iothub_service_client

if(NOT ${use_http})
    message("iothub service client needs HTTP support - use_http option was ${use_http}")
    return()
endif()

compileAsC99()

set(iothub_service_client_c_files
../parson/parson.c
./src/iothub_registrymanager.c
./src/iothub_messaging.c
./src/iothub_messaging_ll.c
./src/iothub_devicetwin.c
./src/iothub_devicemethod.c
./src/iothub_service_client_auth.c
./src/iothub_sc_version.c
../iothub_client/src/iothub_message.c
)

set(iothub_service_client_h_files
../parson/parson.h
./inc/iothub_registrymanager.h
./inc/iothub_messaging.h
./inc/iothub_messaging_ll.h
./inc/iothub_devicetwin.h
./inc/iothub_devicemethod.h
./inc/iothub_service_client_auth.h
./inc/iothub_sc_version.h
../iothub_client/inc/iothub_message.h
)

if(MSVC)
set_source_files_properties(../parson/parson.c PROPERTIES COMPILE_FLAGS "/wd4244 /wd4232")
endif()

include_directories(${SHARED_UTIL_INC_FOLDER})

include_directories(${UAMQP_INC_FOLDER})
include_directories(${UAMQP_INCLUDES})

set(IOTHUB_SERVICE_CLIENT_INC_FOLDER ${CMAKE_CURRENT_LIST_DIR}/inc CACHE INTERNAL "This is the include folder for iothub_service_client" FORCE)

include_directories(${CMAKE_CURRENT_LIST_DIR}/../parson ${IOTHUB_SERVICE_CLIENT_INC_FOLDER} ${CMAKE_CURRENT_LIST_DIR}/../iothub_client/inc)

IF(WIN32)
	#windows needs this define
	add_definitions(-D_CRT_SECURE_NO_WARNINGS)
	add_definitions(-DGB_MEASURE_MEMORY_FOR_THIS -DGB_DEBUG_ALLOC)
ENDIF(WIN32)

if (${build_as_dynamic})
    add_library(iothub_service_client SHARED ${iothub_service_client_c_files} ${iothub_service_client_h_files} ./src/iothub_service_client.def)
    linkSharedUtil(iothub_service_client)

else()
    add_library(iothub_service_client ${iothub_service_client_c_files} ${iothub_service_client_h_files})
endif()

if(NOT ${nuget_e2e_tests})
    target_link_libraries(iothub_service_client uamqp)
else()
    target_link_libraries(iothub_service_client)
endif()

if (NOT ${ARCHITECTURE} STREQUAL "ARM")
    if (NOT ${skip_samples})
	add_subdirectory(samples)
	endif()
endif()

if(NOT IN_OPENWRT)
    # Disable tests for OpenWRT
    if(${run_unittests})
        add_subdirectory(tests)
    endif()
endif()

if(${use_installed_dependencies})

    if(NOT DEFINED CMAKE_INSTALL_LIBDIR)
        set(CMAKE_INSTALL_LIBDIR "lib")
    endif()
    
    if(NOT DEFINED CMAKE_INSTALL_INCLUDEDIR)
        set(CMAKE_INSTALL_INCLUDEDIR "include")
    endif()

    install(TARGETS iothub_service_client EXPORT azure_iot_sdksTargets
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_LIBDIR}/../bin
        INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/azureiot
    )
    install(FILES ${iothub_service_client_h_files} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/azureiot)
else()
	set(install_staticlibs
		iothub_service_client
	)
	install(FILES ${iothub_service_client_h_files}
		DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/iothub_service_client) 
	install(TARGETS ${install_staticlibs} 
		ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
		LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()
