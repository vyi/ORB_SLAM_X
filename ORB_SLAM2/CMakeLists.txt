cmake_minimum_required(VERSION 3.0)
project(ORB_SLAM2)


MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall   -O3 -march=native")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(OpenCV_DIR "C:/Users/vvyog/Documents/opencv/build/x64/vc15/lib")
set(EIGEN3_INCLUDE_DIR "C:/Users/vvyog/Documents/Eigen3/include/eigen3")
set(Eigen3_DIR "C:/Users/vvyog/Documents/Eigen3/share/eigen3/cmake")

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake_modules)

#find_package(OpenCV 4 QUIET)
#if(NOT OpenCV_FOUND)
#   find_package(OpenCV 2.4.3 QUIET)
#   if(NOT OpenCV_FOUND)
#      message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
#   endif()
#endif()

find_package(OpenCV 3 )

# Add Eigen3 configuration
if(WIN32)
    find_package(Eigen3 3.1.0 REQUIRED PATHS "C:\\Users\\vvyog\\Documents\\Eigen3\\share\\eigen3\\cmake")
    if(EIGEN3_FOUND)
        message(STATUS "Found Eigen3 for Pangolin: ${EIGEN3_INCLUDE_DIR}")
    else()
        message(FATAL_ERROR "Eigen3 not found for Pangolin")
    endif()
    
    include_directories(${EIGEN3_INCLUDE_DIR})
endif()
set(Boost_NO_SYSTEM_PATHS true)
set (Boost_USE_STATIC_LIBS ON CACHE BOOL "use static libraries from Boost")
set (Boost_USE_MULTITHREADED ON)

set(BOOST_ROOT C:/Users/vvyog/Documents/boost_1_74_0)

find_package(Boost 1.70 REQUIRED 
  COMPONENTS
  system program_options serialization iostreams filesystem container thread 
  )



find_package(Pangolin REQUIRED PATHS "C:/Users/vvyog/Documents/orb3_cmake/Thirdparty/Pangolin/build")

include_directories(
${PROJECT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}/include
${EIGEN3_INCLUDE_DIR}
${Pangolin_INCLUDE_DIRS}
${DBOW2_DIR}/include
${G2O_DIR}/include
)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)

add_library(${PROJECT_NAME} SHARED
src/System.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FrameDrawer.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapDrawer.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
src/Viewer.cc
)

add_subdirectory(Thirdparty/g2o)
add_subdirectory(Thirdparty/DBoW2)

target_link_libraries(${PROJECT_NAME}
${OpenCV_LIBS}
${EIGEN3_LIBS}
${Pangolin_LIBRARIES}
${PROJECT_SOURCE_DIR}/Thirdparty/DBoW2/lib/libDBoW2.dll
${PROJECT_SOURCE_DIR}/Thirdparty/g2o/lib/libg2o.dll
)

# Build examples

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/RGB-D)

add_executable(rgbd_tum
Examples/RGB-D/rgbd_tum.cc)
target_link_libraries(rgbd_tum ${PROJECT_NAME})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Stereo)

add_executable(stereo_kitti
Examples/Stereo/stereo_kitti.cc)
target_link_libraries(stereo_kitti ${PROJECT_NAME})

add_executable(stereo_euroc
Examples/Stereo/stereo_euroc.cc)
target_link_libraries(stereo_euroc ${PROJECT_NAME})


set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/Examples/Monocular)

add_executable(mono_tum
Examples/Monocular/mono_tum.cc)
target_link_libraries(mono_tum ${PROJECT_NAME})

add_executable(mono_kitti
Examples/Monocular/mono_kitti.cc)
target_link_libraries(mono_kitti ${PROJECT_NAME})

add_executable(mono_euroc
Examples/Monocular/mono_euroc.cc)
target_link_libraries(mono_euroc ${PROJECT_NAME})

