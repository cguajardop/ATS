# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

############################################################
# Dockerfile to build Edge-Tier Cache container images for
# Apache Traffic Control
# Based on CentOS 8
############################################################

ifeq ($(RHEL_VERSION),)
	export RHEL_VERSION := 8
else
	RHEL_VERSION := $(shell echo $(RHEL_VERSION) | cut -f1 -d.)
endif

CIAB_DIR_RELATIVE := $(dir $(MAKEFILE_LIST))
CIAB_DIR_ABSOLUTE := $(shell cd $(CIAB_DIR_RELATIVE) && pwd)
TC_DIR := $(CIAB_DIR_RELATIVE)../..

BUILD_SUFFIX := _build
BUILD_NUMBER := $(shell git rev-list HEAD 2>/dev/null | wc -l | tr -d '[[:space:]]').$(shell git rev-parse --short=8 HEAD)
ATS_VERSION := $(shell $(CIAB_DIR_RELATIVE)bin/ats-version.sh)
ifeq ($(RHEL_VERSION),7)
# The commit hash in `git describe` is only 7 characters in CentOS 7's git 1.8
ATS_VERSION := $(shell echo "$(ATS_VERSION)" | sed -E 's/^(.*-[0-9]+\.[0-9a-f]{7})[0-9a-f]*/\1/')
endif

SPECIAL_SYRUP := $(ATS_VERSION).$(ATS_VERSION).el$(RHEL_VERSION).x86_64.rpm

ATS_SOURCE := $(wildcard $(TC_DIR)/cache-config/testing/docker/trafficserver/**)

ATS_RPM_RELATIVE := $(CIAB_DIR_RELATIVE)$(ATS_RPM)

ATS_RPM_ABSOLUTE := $(CIAB_DIR_ABSOLUTE)/$(ATS_RPM)

ATS_DIST_RPM := $(TC_DIR)/RPM/trafficserver-$(SPECIAL_SYRUP)

.PHONY: all build-builders clean debug native nearly-all pull-builders very-clean

# Default target; builds all pre-requisite rpms from source trees
all: $(ATS_RPM_RELATIVE)

# Relative path output RPM recipies
$(ATS_RPM_RELATIVE): $(ATS_DIST_RPM)
	cp -f "$?" "$@"

# Absolute path output RPM recipies
$(ATS_RPM_ABSOLUTE): $(ATS_DIST_RPM)
	cp -f "$?" "$@"

# Dist rpms
$(ATS_DIST_RPM): $(ATS_SOURCE)
	docker-compose -f $(TC_DIR)/cache-config/testing/docker/docker-compose-ats-build.yml build trafficserver_build && docker-compose -f $(TC_DIR)/cache-config/testing/docker/docker-compose-ats-build.yml run --rm trafficserver_build


clean:
	cd "$(CIAB_DIR_RELATIVE)"
	$(RM) $(TM_RPM_RELATIVE) $(TO_RPM_RELATIVE) $(TP_RPM_RELATIVE) $(TR_RPM_RELATIVE) $(TOMCAT_RPM_RELATIVE) $(ORT_RPM_RELATIVE) $(TS_RPM_RELATIVE)
	
