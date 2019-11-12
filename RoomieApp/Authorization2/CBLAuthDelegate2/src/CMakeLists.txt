find_package(CURL ${CURL_PACKAGE_CONFIG})
find_package(Threads ${THREADS_PACKAGE_CONFIG})

add_definitions("-DACSDK_LOG_MODULE=cblAuthDelegate2")
add_library(CBLAuthDelegate2 SHARED
    CBLAuthDelegate.cpp
    CBLAuthDelegateConfiguration.cpp
    SQLiteCBLAuthDelegateStorage.cpp)
target_include_directories(CBLAuthDelegate2 PUBLIC
    "${CBLAuthDelegate_SOURCE_DIR}/include")
target_include_directories(CBLAuthDelegate2 PRIVATE
    "${RAPIDJSON_INCLUDE_DIR}")
target_link_libraries(CBLAuthDelegate2 AVSCommon RegistrationManager SQLiteStorage ${CURL_LIBRARIES} ${CMAKE_THREAD_LIBS_INIT})

# install target
asdk_install()
