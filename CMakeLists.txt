
include_directories(src/ src/rml/include/ build/vs2013/)

include_directories(include SYSTEM)


set(result ${tbb_GMA_USER_DEFINED_ARGS})

add_library(TBB STATIC

src/tbb/arena.cpp                    src/tbb/concurrent_queue.cpp    src/tbb/dynamic_link.cpp  src/tbb/mutex.cpp           src/tbb/queuing_mutex.cpp       src/tbb/scheduler.cpp      src/tbb/task.cpp                src/tbb/tbb_misc_ex.cpp
src/tbb/cache_aligned_allocator.cpp  src/tbb/concurrent_vector.cpp   src/tbb/governor.cpp      src/tbb/observer_proxy.cpp  src/tbb/queuing_rw_mutex.cpp    src/tbb/semaphore.cpp      src/tbb/task_group_context.cpp  src/tbb/tbb_statistics.cpp
src/tbb/concurrent_hash_map.cpp      src/tbb/condition_variable.cpp  src/tbb/itt_notify.cpp    src/tbb/pipeline.cpp        src/tbb/reader_writer_lock.cpp  src/tbb/spin_mutex.cpp     src/tbb/tbb_main.cpp            src/tbb/tbb_thread.cpp
src/tbb/concurrent_monitor.cpp       src/tbb/critical_section.cpp    src/tbb/market.cpp        src/tbb/private_server.cpp  src/tbb/recursive_mutex.cpp     src/tbb/spin_rw_mutex.cpp  src/tbb/tbb_misc.cpp            src/tbb/x86_rtm_rw_mutex.cpp

)

# TODO: add other supported compilers.
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
	target_compile_definitions(TBB PRIVATE -DUSE_PTHREAD)
	set(compiler gcc)
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "Intel")
	set(compiler icc)
	if (CMAKE_SYSTEM_NAME MATCHES "Windows")
		set(compiler icl)
	endif()
elseif (MSVC)
	set(compiler cl)
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
	set(compiler clang)
endif()

set(result "compiler=${compiler}" ${result})

if (CMAKE_SYSTEM_NAME MATCHES "Android")
	set(result target=android ${result})
endif()


target_include_directories(TBB INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_include_directories(TBB PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src/)
target_compile_definitions(TBB PRIVATE -D__TBB_BUILD)

if (WIN32)
	target_compile_definitions(TBB PRIVATE -DUSE_WINTHREAD)
else()
	target_compile_definitions(TBB PRIVATE -DUSE_PTHREAD)
endif()

if(WIN32)
set_target_properties(TBB PROPERTIES
	DEBUG_OUTPUT_NAME "tbb_debug"
	RELEASE_OUTPUT_NAME "tbb")
endif()