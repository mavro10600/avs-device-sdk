set(RoomieApp_SOURCES)
list(APPEND RoomieApp_SOURCES
    ConnectionObserver.cpp
    ConsolePrinter.cpp
    ConsoleReader.cpp
    GuiRenderer.cpp
    InteractionManager.cpp
    KeywordObserver.cpp
    LocaleAssetsManager.cpp
    UIManager.cpp
    UserInputManager.cpp
    SampleApplication.cpp
    SampleEqualizerModeController.cpp
    main.cpp)

if (PORTAUDIO)
    list(APPEND RoomieApp_SOURCES PortAudioMicrophoneWrapper.cpp)
endif()

IF (A4B)
    list(APPEND RoomieApp_SOURCES RevokeAuthorizationObserver.cpp)
ENDIF()

if (PCC)
    list(APPEND RoomieApp_SOURCES PhoneCaller.cpp)
endif()

if (MCC)
    list(APPEND RoomieApp_SOURCES MeetingClient.cpp)
    list(APPEND RoomieApp_SOURCES CalendarClient.cpp)
endif()

IF (HAS_EXTERNAL_MEDIA_PLAYER_ADAPTERS)
    file(GLOB_RECURSE SRC_FILE ${CMAKE_CURRENT_SOURCE_DIR}/ExternalMediaAdapterRegistration/*.cpp)
    foreach(myfile ${SRC_FILE})
       list(APPEND RoomieApp_SOURCES ${myfile})
    endforeach(myfile)
ENDIF()

add_executable(RoomieApp ${RoomieApp_SOURCES})

target_include_directories(RoomieApp PUBLIC
    "${RoomieApp_SOURCE_DIR}/include"
    "${AudioResources_SOURCE_DIR}/include"
    "${RegistrationManager_SOURCE_DIR}/include"
    "${ESP_SOURCE_DIR}/include"
    "${PORTAUDIO_INCLUDE_DIR}")

target_link_libraries(RoomieApp
    DefaultClient
    CapabilitiesDelegate
    CBLAuthDelegate
    SQLiteStorage
    EqualizerImplementations
    "${PORTAUDIO_LIB_PATH}")

if (PCC)
    target_link_libraries(RoomieApp AVSCommon)
endif()

if (MCC)
    target_link_libraries(RoomieApp AVSCommon)
endif()

if (ANDROID)
    target_include_directories(RoomieApp PUBLIC "${AndroidUtilities}/include")
    target_link_libraries(RoomieApp AndroidUtilities)
    if (ANDROID_MEDIA_PLAYER)
        target_include_directories(RoomieApp PUBLIC "${AndroidSLESMediaPlayer}/include")
        target_link_libraries(RoomieApp AndroidSLESMediaPlayer)
    endif()
endif()

if (GSTREAMER_MEDIA_PLAYER)
    target_include_directories(RoomieApp PUBLIC "${MediaPlayer_SOURCE_DIR}/include")
    target_link_libraries(RoomieApp MediaPlayer)
endif()

if(KWD)
    target_link_libraries(RoomieApp KeywordDetectorProvider)
endif()

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    target_link_libraries(RoomieApp
        "-framework CoreAudio" 
        "-framework AudioToolbox" 
        "-framework AudioUnit" 
        "-framework CoreServices" 
        "-framework Carbon")
elseif(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    target_link_libraries(RoomieApp
      rt m pthread asound atomic)
endif()

