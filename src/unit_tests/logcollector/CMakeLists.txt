# Generate logcollector library
file(GLOB logcollector_files
    ${SRC_FOLDER}/logcollector/*.o)
add_library(LOGCOLLECTOR_O STATIC ${logcollector_files})
set_source_files_properties(
    ${logcollector_files}
    PROPERTIES
    EXTERNAL_OBJECT true
    GENERATED true
)
set_target_properties(
    LOGCOLLECTOR_O
    PROPERTIES
    LINKER_LANGUAGE C
)
target_link_libraries(LOGCOLLECTOR_O ${WAZUHLIB} ${WAZUHEXT} -lpthread)

if(${TARGET} STREQUAL "winagent")
    link_directories(${SRC_FOLDER}/syscheckd/build/bin)
endif(${TARGET} STREQUAL "winagent")

#include wrappers
include(${SRC_FOLDER}/unit_tests/wrappers/wazuh/shared/shared.cmake)

# Generate logcollector tests
if(${TARGET} STREQUAL "winagent")
    list(APPEND logcollector_names "test_read_win_event_channel")
    list(APPEND logcollector_flags "-Wl,--wrap,wstr_split -Wl,--wrap=syscom_dispatch -Wl,--wrap=Start_win32_Syscheck \
                                    -Wl,--wrap=is_fim_shutdown -Wl,--wrap,convert_windows_string \
                                    ${DEBUG_OP_WRAPPERS}")
else()
    list(APPEND logcollector_names "test_logcollector")
    list(APPEND logcollector_flags "-Wl,--wrap,OS_SHA1_Stream -Wl,--wrap,merror_exit -Wl,--wrap,popen \
                                    -Wl,--wrap,fopen -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets -Wl,--wrap,fread -Wl,--wrap,fseek \
                                    -Wl,--wrap,fwrite -Wl,--wrap,remove -Wl,--wrap,fgetpos \
                                    -Wl,--wrap,cJSON_CreateObject -Wl,--wrap,cJSON_AddArrayToObject -Wl,--wrap,cJSON_AddStringToObject \
                                    -Wl,--wrap,cJSON_AddStringToObject -Wl,--wrap,cJSON_AddItemToArray -Wl,--wrap,pthread_rwlock_wrlock \
                                    -Wl,--wrap,cJSON_PrintUnformatted -Wl,--wrap,cJSON_Delete -Wl,--wrap,wfopen -Wl,--wrap,clearerr \
                                    -Wl,--wrap,cJSON_GetObjectItem -Wl,--wrap,cJSON_GetArraySize -Wl,--wrap,cJSON_GetArrayItem \
                                    -Wl,--wrap,cJSON_GetStringValue -Wl,--wrap,OS_SHA1_File_Nbytes -Wl,--wrap,fileno -Wl,--wrap,fstat \
                                    -Wl,--wrap,pthread_rwlock_rdlock -Wl,--wrap,pthread_rwlock_unlock \
                                    -Wl,--wrap,stat -Wl,--wrap=fgetc -Wl,--wrap=w_fseek -Wl,--wrap,w_ftell \
                                    -Wl,--wrap,OS_SHA1_File_Nbytes_with_fp_check -Wl,--wrap,cJSON_CreateString \
                                    -Wl,--wrap,cJSON_AddItemToObject -Wl,--wrap,wpclose -Wl,--wrap,kill \
                                    -Wl,--wrap,so_get_function_sym -Wl,--wrap,so_get_module_handle ${DEBUG_OP_WRAPPERS} \
                                    ${HASH_OP_WRAPPERS}")

    list(APPEND logcollector_names "test_read_multiline_regex")
    list(APPEND logcollector_flags "-Wl,--wrap,fopen -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets -Wl,--wrap,popen \
                                    -Wl,--wrap,fread -Wl,--wrap,fwrite -Wl,--wrap,remove -Wl,--wrap,fseek -Wl,--wrap=fgetc\
                                    -Wl,--wrap,time -Wl,--wrap,can_read -Wl,--wrap,w_ftell -Wl,--wrap,w_expression_match \
                                    -Wl,--wrap,w_msg_hash_queues_push -Wl,--wrap,fgetpos -Wl,--wrap=w_update_file_status \
                                    -Wl,--wrap=w_get_hash_context -Wl,--wrap=_fseeki64 -Wl,--wrap=OS_SHA1_Stream \
                                    -Wl,--wrap=w_fseek -Wl,--wrap,_mdebug2")

    list(APPEND logcollector_names "test_localfile-config")
    list(APPEND logcollector_flags "-Wl,--wrap,fopen -Wl,--wrap,popen -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets \
                                    -Wl,--wrap,fread -Wl,--wrap,fwrite -Wl,--wrap,remove -Wl,--wrap,fseek -Wl,--wrap=fgetc\
                                    -Wl,--wrap,w_get_attr_val_by_name -Wl,--wrap,fgetpos ${DEBUG_OP_WRAPPERS}")

    list(APPEND logcollector_names "test_state")
    list(APPEND logcollector_flags "-Wl,--wrap,fopen -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets \
                                    -Wl,--wrap,fread -Wl,--wrap,fwrite -Wl,--wrap,remove -Wl,--wrap,fseek -Wl,--wrap=fgetc \
                                    -Wl,--wrap,fgetpos -Wl,--wrap,time -Wl,--wrap,popen \
                                    -Wl,--wrap,cJSON_CreateObject -Wl,--wrap,cJSON_CreateArray \
                                    -Wl,--wrap,cJSON_AddStringToObject -Wl,--wrap,cJSON_AddNumberToObject \
                                    -Wl,--wrap,cJSON_AddItemToArray -Wl,--wrap,cJSON_AddItemToObject \
                                    -Wl,--wrap,pthread_mutex_unlock -Wl,--wrap,pthread_mutex_lock \
                                    -Wl,--wrap,cJSON_Delete -Wl,--wrap,cJSON_Print -Wl,--wrap,getDefine_Int \
                                    -Wl,--wrap,FOREVER -Wl,--wrap,sleep -Wl,--wrap,getpid -Wl,--wrap,cJSON_Duplicate \
                                    -Wl,--wrap,strftime ${DEBUG_OP_WRAPPERS} ${HASH_OP_WRAPPERS}")

    list(APPEND logcollector_names "test_lccom")
    list(APPEND logcollector_flags "-Wl,--wrap,w_logcollector_state_get -Wl,--wrap,cJSON_CreateObject \
                                    -Wl,--wrap,cJSON_AddNumberToObject \
                                    -Wl,--wrap,cJSON_AddObjectToObject -Wl,--wrap,cJSON_AddStringToObject \
                                    -Wl,--wrap,cJSON_AddItemToObject -Wl,--wrap,cJSON_AddFalseToObject -Wl,--wrap,cJSON_PrintUnformatted \
                                    -Wl,--wrap,cJSON_Delete -Wl,--wrap,_mwarn -Wl,--wrap,stat -Wl,--wrap,_mdebug2 \
                                    -Wl,--wrap,difftime -Wl,--wrap,strftime ${DEBUG_OP_WRAPPERS}")

    list(APPEND logcollector_names "test_macos_log")
    list(APPEND logcollector_flags "-Wl,--wrap,wpopenv -Wl,--wrap,fileno -Wl,--wrap,fcntl -Wl,--wrap,_merror \
                                    -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets -Wl,--wrap,fgetpos \
                                    -Wl,--wrap,fopen -Wl,--wrap,fread -Wl,--wrap,fseek -Wl,--wrap,fwrite \
                                    -Wl,--wrap,remove -Wl,--wrap,fgetc -Wl,--wrap,wpclose -Wl,--wrap,access \
                                    -Wl,--wrap,getpid -Wl,--wrap,_minfo -Wl,--wrap,pthread_rwlock_wrlock \
                                    -Wl,--wrap,pthread_rwlock_unlock -Wl,--wrap,pthread_rwlock_rdlock \
                                    -Wl,--wrap,so_get_function_sym -Wl,--wrap,so_get_module_handle -Wl,--wrap,popen \
                                    -Wl,--wrap,_mdebug1 -Wl,--wrap,w_get_os_codename -Wl,--wrap,w_get_process_childs")

    list(APPEND logcollector_names "test_read_macos")
    list(APPEND logcollector_flags "-Wl,--wrap,w_expression_match -Wl,--wrap,can_read -Wl,--wrap,fgets \
                                    -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgetpos -Wl,--wrap,_mdebug2\
                                    -Wl,--wrap,fopen -Wl,--wrap,fread -Wl,--wrap,fseek -Wl,--wrap,fwrite -Wl,--wrap,popen \
                                    -Wl,--wrap,remove -Wl,--wrap,fgetc -Wl,--wrap,_merror -Wl,--wrap,waitpid \
                                    -Wl,--wrap,w_msg_hash_queues_push -Wl,--wrap,_mdebug1 -Wl,--wrap,_minfo \
                                    -Wl,--wrap,kill -Wl,--wrap,wpopenv -Wl,--wrap,wpclose -Wl,--wrap,strerror \
                                    -Wl,--wrap,time -Wl,--wrap,so_get_function_sym -Wl,--wrap,so_get_module_handle \
                                    -Wl,--wrap,isDebug -Wl,--wrap,w_get_first_child -Wl,--wrap,w_is_macos_sierra \
                                    -Wl,--wrap,w_macos_set_log_settings -Wl,--wrap,w_macos_set_last_log_timestamp \
                                    -Wl,--wrap,w_macos_set_is_valid_data")

    list(APPEND logcollector_names "test_read_multiline")
    list(APPEND logcollector_flags "-Wl,--wrap,fopen -Wl,--wrap,popen -Wl,--wrap,fclose -Wl,--wrap,fflush -Wl,--wrap,fgets \
                                    -Wl,--wrap,fread -Wl,--wrap,fwrite -Wl,--wrap,remove -Wl,--wrap,fseek -Wl,--wrap=fgetc\
                                    -Wl,--wrap,time -Wl,--wrap,can_read -Wl,--wrap,w_ftell -Wl,--wrap,w_expression_match \
                                    -Wl,--wrap,fgetpos -Wl,--wrap=w_update_file_status -Wl,--wrap,_merror \
                                    -Wl,--wrap=w_get_hash_context -Wl,--wrap=_fseeki64 -Wl,--wrap=OS_SHA1_Stream \
                                    -Wl,--wrap=w_fseek")
endif()

list(LENGTH logcollector_names count)
math(EXPR count "${count} - 1")
foreach(counter RANGE ${count})
    list(GET logcollector_names ${counter} logcollector_test_name)
    list(GET logcollector_flags ${counter} logcollector_test_flags)
    add_executable(${logcollector_test_name} ${logcollector_test_name}.c)
    target_link_libraries(
        ${logcollector_test_name}
        ${WAZUHLIB}
        ${WAZUHEXT}
        LOGCOLLECTOR_O
        ${TEST_DEPS}
    )

    if(${TARGET} STREQUAL "winagent")
        target_link_libraries(${logcollector_test_name} fimdb)
    endif(${TARGET} STREQUAL "winagent")

    if(NOT logcollector_test_flags STREQUAL " ")
        target_link_libraries(
            ${logcollector_test_name}
            ${logcollector_test_flags}
        )
    endif()
    add_test(NAME ${logcollector_test_name} COMMAND ${logcollector_test_name})
endforeach()
