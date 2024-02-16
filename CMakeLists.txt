cmake_minimum_required(VERSION 3.12)
project(MeshSTEPLoader LANGUAGES CXX VERSION 0.5)

set(MESHSTEPLOADERSRC_DIR src/MeshSTEPLoader)

set(HEADER_FILES
    ${MESHSTEPLOADERSRC_DIR}/config.h.in
    ${MESHSTEPLOADERSRC_DIR}/init.h
    ${MESHSTEPLOADERSRC_DIR}/MeshSTEPLoader.h
    ${MESHSTEPLOADERSRC_DIR}/ParametricTriangleTopologyContainer.h
    ${MESHSTEPLOADERSRC_DIR}/STEPShapeMapping.h
    ${MESHSTEPLOADERSRC_DIR}/SingleComponent.h
    ${MESHSTEPLOADERSRC_DIR}/SingleComponent.inl
)

set(SOURCE_FILES
    ${MESHSTEPLOADERSRC_DIR}/init.cpp
    ${MESHSTEPLOADERSRC_DIR}/MeshSTEPLoader.cpp
    ${MESHSTEPLOADERSRC_DIR}/ParametricTriangleTopologyContainer.cpp
    ${MESHSTEPLOADERSRC_DIR}/STEPShapeMapping.cpp
    ${MESHSTEPLOADERSRC_DIR}/SingleComponent.cpp
)

find_package(Sofa.Component.Topology.Container.Constant REQUIRED)
find_package(Sofa.Component.Topology.Container.Dynamic REQUIRED)

list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
find_package(OpenCascade REQUIRED)

add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES})
target_link_libraries(${PROJECT_NAME} PUBLIC Sofa.Component.Topology.Container.Constant Sofa.Component.Topology.Container.Dynamic)
target_link_libraries(${PROJECT_NAME} PUBLIC ${OPENCASCADE_LIBRARIES})
target_include_directories(${PROJECT_NAME} PUBLIC ${OPENCASCADE_INCLUDE_DIR})

if(WIN32)
    # required for OpenCASCADE
    target_compile_options(${PROJECT_NAME} PRIVATE "-DWNT")
endif()

sofa_create_package_with_targets(
    PACKAGE_NAME ${PROJECT_NAME}
    PACKAGE_VERSION ${PROJECT_VERSION}
    TARGETS ${PROJECT_NAME} AUTO_SET_TARGET_PROPERTIES
    INCLUDE_SOURCE_DIR "src"
    INCLUDE_INSTALL_DIR ${PROJECT_NAME}
    EXAMPLE_INSTALL_DIR "example"
    RELOCATABLE "plugins"
    )

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/cmake/FindOpenCascade.cmake DESTINATION lib/cmake)
if(WIN32)
    # Copy OpenCascade DLLs
    file(COPY ${OPENCASCADE_DLLS} DESTINATION bin)
    install(FILES ${OPENCASCADE_DLLS} DESTINATION bin)
endif()
