cmake_minimum_required(VERSION 3.16)

project(IronReporter VERSION 1.0.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Qt6 REQUIRED COMPONENTS Quick LinguistTools)

configure_file(version.rc.in version.rc)

qt_standard_project_setup(REQUIRES 6.8)

qt_add_executable(IronReporter
    main.cpp
    version.rc
)

qt_add_qml_module(IronReporter
    URI IronReporter
    QML_FILES
        qml/App.qml
    RESOURCES resources.qrc
)

target_compile_definitions(IronReporter PRIVATE
    APP_VERSION="${PROJECT_VERSION}"
)

set_target_properties(IronReporter PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER com.kikimosha.appIronReporter
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

target_link_libraries(IronReporter
    PRIVATE Qt6::Quick
)

include(GNUInstallDirs)
install(TARGETS IronReporter
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

if(WIN32)
    get_target_property(_qmake_executable Qt6::qmake IMPORTED_LOCATION)
    get_filename_component(_qt_bin_dir "${_qmake_executable}" DIRECTORY)
    find_program(WINDEPLOYQT_EXECUTABLE windeployqt HINTS "${_qt_bin_dir}")

    install(CODE "
        execute_process(COMMAND \"${WINDEPLOYQT_EXECUTABLE}\"
            --qmldir \"${CMAKE_CURRENT_SOURCE_DIR}\"
            --no-translations
            --compiler-runtime
            \"\$ENV{DESTDIR}\${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR}/IronReporter.exe\"
        )
        ")
endif()
