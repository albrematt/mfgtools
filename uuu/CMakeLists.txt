set(UUU_SOURCES
		uuu.cpp
		buildincmd.cpp
		autocomplete.cpp
		)
set(UUU_LSTS
		uuu.lst
		emmc_burn_loader.lst
		emmc_burn_all.lst
		fat_write.lst
		qspi_burn_loader.lst
		sd_burn_loader.lst
		spl_boot.lst
		sd_burn_all.lst
		nand_burn_loader.lst
		)
set(UUU_CLSTS
		uuu.clst
		emmc_burn_loader.clst
		emmc_burn_all.clst
		fat_write.clst
		qspi_burn_loader.clst
		sd_burn_loader.clst
		spl_boot.clst
		sd_burn_all.clst
		nand_burn_loader.clst
		)

add_executable(uuu ${UUU_SOURCES} ${UUU_CLSTS})
target_compile_definitions(uuu
		PRIVATE
		$<$<NOT:$<CXX_COMPILER_ID:MSVC>>:TARGET_PATH="${CMAKE_INSTALL_PREFIX}/bin/uuu">
		)
target_include_directories(uuu
		PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
		)
target_link_libraries(uuu
		PUBLIC
		${CMAKE_DL_LIBS}
		libuuu
		)

if(SKIP_UUU_INSTALL)
	install(TARGETS uuu DESTINATION "${CMAKE_INSTALL_BINDIR}")
endif()
