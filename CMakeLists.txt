###################################################################################################
#                                                                                                 #
# This file is part of HPIPM.                                                                     #
#                                                                                                 #
# HPIPM -- High Performance Interior Point Method.                                                #
# Copyright (C) 2017 by Gianluca Frison.                                                          #
# Developed at IMTEK (University of Freiburg) under the supervision of Moritz Diehl.              #
# All rights reserved.                                                                            #
#                                                                                                 #
# HPMPC is free software; you can redistribute it and/or                                          #
# modify it under the terms of the GNU Lesser General Public                                      #
# License as published by the Free Software Foundation; either                                    #
# version 2.1 of the License, or (at your option) any later version.                              #
#                                                                                                 #
# HPMPC is distributed in the hope that it will be useful,                                        #
# but WITHOUT ANY WARRANTY; without even the implied warranty of                                  #
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.                                            #
# See the GNU Lesser General Public License for more details.                                     #
#                                                                                                 #
# You should have received a copy of the GNU Lesser General Public                                #
# License along with HPMPC; if not, write to the Free Software                                    #
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301  USA                  #
#                                                                                                 #
# Author: Gianluca Frison, gianluca.frison (at) imtek.uni-freiburg.de                             #
#                                                                                                 #
###################################################################################################

cmake_minimum_required(VERSION 2.8.11)
project(hpipm)

set(CMAKE_VERBOSE_MAKEFILE ON)

# Target architecture
set(TARGET GENERIC)

# BLAS and LAPACK version (for LA=BLAS in BLASFEO)
set(REF_BLAS 0)
#set(REF_BLAS OPENBLAS)
#set(REF_BLAS NETLIB)
#set(REF_BLAS MKL)
#set(REF_BLAS BLIS)
#set(REF_BLAS ATLAS)

# BLASFEO
if(NOT TARGET blasfeo)
	# BLASFEO installation directory
	set(BLASFEO_PATH /opt/blasfeo)
	add_library(blasfeo STATIC IMPORTED)
	set_target_properties(blasfeo PROPERTIES IMPORTED_LOCATION ${BLASFEO_PATH}/lib/libblasfeo.a)
	set(BLASFEO_INCLUDE_DIR ${BLASFEO_PATH}/include)
else()
	# TODO: horrible hardcode; needs to go
	set(BLASFEO_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../blasfeo/include)
endif()

# headers installation directory
set(HPIPM_HEADERS_INSTALLATION_DIRECTORY "include" CACHE STRING "Headers local installation directory")

# enable runtine checks
set(RUNTIME_CHECKS 0)
#set(RUNTIME_CHECKS 0)

#
if(${REF_BLAS} MATCHES 0)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ")
endif(${REF_BLAS} MATCHES 0)
if(${REF_BLAS} MATCHES OPENBLAS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_OPENBLAS -I/opt/openblas/include")
endif(${REF_BLAS} MATCHES OPENBLAS)
if(${REF_BLAS} MATCHES BLIS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_BLIS -std=c99")
endif(${REF_BLAS} MATCHES BLIS)
if(${REF_BLAS} MATCHES NETLIB)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_NETLIB")
endif(${REF_BLAS} MATCHES NETLIB)
if(${REF_BLAS} MATCHES MKL)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_MKL -m64 -I/opt/intel/mkl/include")
endif(${REF_BLAS} MATCHES MKL)
if(${REF_BLAS} MATCHES ATLAS)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DREF_BLAS_ATLAS")
endif(${REF_BLAS} MATCHES ATLAS)

#
if(${RUNTIME_CHECKS} MATCHES 1)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DRUNTIME_CHECKS")
endif(${RUNTIME_CHECKS} MATCHES 1)

# architecture-specific flags
if(${TARGET} MATCHES GENERIC)
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DTARGET_GENERIC")
endif(${TARGET} MATCHES GENERIC)

# source files
# cond
file(GLOB COND_SRC
	${PROJECT_SOURCE_DIR}/cond/d_cond.c
	${PROJECT_SOURCE_DIR}/cond/d_cond_aux.c
	${PROJECT_SOURCE_DIR}/cond/d_part_cond.c
	${PROJECT_SOURCE_DIR}/cond/s_cond.c
	${PROJECT_SOURCE_DIR}/cond/s_cond_aux.c
	${PROJECT_SOURCE_DIR}/cond/s_part_cond.c
	)
# dense qp
file(GLOB DENSE_QP_SRC
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_dim.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_sol.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_kkt.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_ipm.c
	${PROJECT_SOURCE_DIR}/dense_qp/d_dense_qp_res.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_dim.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_sol.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_kkt.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_ipm.c
	${PROJECT_SOURCE_DIR}/dense_qp/s_dense_qp_res.c
	)
# core ipm
file(GLOB IPM_CORE_SRC
	${PROJECT_SOURCE_DIR}/ipm_core/d_core_qp_ipm_aux.c
	${PROJECT_SOURCE_DIR}/ipm_core/d_core_qp_ipm.c
	${PROJECT_SOURCE_DIR}/ipm_core/s_core_qp_ipm_aux.c
	${PROJECT_SOURCE_DIR}/ipm_core/s_core_qp_ipm.c
	)
# ocp qp
file(GLOB OCP_QP_SRC
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_dim.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_sol.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_kkt.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_ipm.c
	${PROJECT_SOURCE_DIR}/ocp_qp/d_ocp_qp_res.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_dim.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_sol.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_kkt.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_ipm.c
	${PROJECT_SOURCE_DIR}/ocp_qp/s_ocp_qp_res.c
#	${PROJECT_SOURCE_DIR}/ocp_qp/m_ocp_qp.c
#	${PROJECT_SOURCE_DIR}/ocp_qp/m_ocp_qp_kkt.c
#	${PROJECT_SOURCE_DIR}/ocp_qp/m_ocp_qp_ipm_hard.c
	)
# tree qp
file(GLOB TREE_OCP_QP_SRC
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/scenario_tree.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/d_tree_ocp_qp.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/d_tree_ocp_qp_sol.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/d_tree_ocp_qp_kkt.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/d_tree_ocp_qp_ipm.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/s_tree_ocp_qp.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/s_tree_ocp_qp_sol.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/s_tree_ocp_qp_kkt.c
	${PROJECT_SOURCE_DIR}/tree_ocp_qp/s_tree_ocp_qp_ipm.c
	)

set(HPIPM_SRC ${COND_SRC} ${DENSE_QP_SRC} ${IPM_CORE_SRC} ${OCP_QP_SRC} ${TREE_OCP_QP_SRC})

add_library(hpipm ${HPIPM_SRC})
target_include_directories(hpipm PRIVATE "include" "${BLASFEO_INCLUDE_DIR}")
target_link_libraries(hpipm blasfeo)

install(TARGETS hpipm EXPORT hpipmConfig
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
	RUNTIME DESTINATION bin)

install(EXPORT hpipmConfig DESTINATION cmake)

file(GLOB_RECURSE HPIPM_HEADERS "include/*.h")
install(FILES ${HPIPM_HEADERS} DESTINATION ${HPIPM_HEADERS_INSTALLATION_DIRECTORY})

# test problems
#add_subdirectory(test_problems)
