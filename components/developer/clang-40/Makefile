#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL"), version 1.0.
# You may only use this file in accordance with the terms of version
# 1.0 of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source.  A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2016 Aurelien Larcher.  All rights reserved.
#
PREFERRED_BITS=64

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME= clang-40
LLVM_NAME=llvm
CLANG_NAME=cfe

COMPONENT_VERSION= 4.0.1
COMPONENT_MAJOR_VERSION= \
  $(shell echo $(COMPONENT_VERSION) | $(GSED) -e 's/\([0-9]\+\.[0-9]\+\).*/\1/')
COMPONENT_REVISION= 1
COMPONENT_PROJECT_URL = http://llvm.org/
COMPONENT_FMRI= developer/clang-40
COMPONENT_CLASSIFICATION= Development/C
COMPONENT_SUMMARY= LLVM tools and Clang compiler
COMPONENT_SRC= $(LLVM_NAME)-$(COMPONENT_VERSION).src
COMPONENT_ARCHIVE= $(COMPONENT_SRC).tar.xz
COMPONENT_ARCHIVE_HASH= \
  sha256:da783db1f82d516791179fe103c71706046561f7972b18f0049242dee6712b51
COMPONENT_ARCHIVE_URL= \
  http://llvm.org/releases/$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE)
COMPONENT_LICENSE= BSD

COMPONENT_ARCHIVE_1=    $(CLANG_NAME)-$(COMPONENT_VERSION).src.tar.xz
COMPONENT_ARCHIVE_HASH_1= \
  sha256:61738a735852c23c3bdbe52d035488cdb2083013f384d67c1ba36fabebd8769b
COMPONENT_ARCHIVE_URL_1= http://llvm.org/releases/$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE_1)

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/cmake.mk
include $(WS_MAKE_RULES)/ips.mk

CMAKE_PREFIX= /usr/clang/$(COMPONENT_MAJOR_VERSION)

PATH=$(PATH.illumos)

PKG_MACROS+= GCC_VERSION=$(GCC_VERSION)

COMPONENT_POST_UNPACK_ACTION += \
  $(UNPACK) $(USERLAND_ARCHIVES)$(COMPONENT_ARCHIVE_1) && \
	mv $(CLANG_NAME)-$(COMPONENT_VERSION).src $(COMPONENT_SRC)/tools/clang

COMPONENT_PREP_ACTION = ( \
	$(GSED) -i -e 's|OPENINDIANA_GCC_VERSION|\"$(GCC_FULL_VERSION)\"|' \
		$(SOURCE_DIR)/tools/clang/lib/Driver/ToolChains.cpp ; \
	$(GSED) -i -e 's|OPENINDIANA_GCC_LIBPATH|\"$(GCC_LIBGCCDIR)\"|' \
		 $(SOURCE_DIR)/tools/clang/lib/Driver/ToolChains.cpp ; \
	$(GSED) -i -e 's|OPENINDIANA_GCC_INCPATH|\"$(GCC_INCGXXDIR)\"|' \
		 $(SOURCE_DIR)/tools/clang/lib/Frontend/InitHeaderSearch.cpp ; )

COMPONENT_PRE_CONFIGURE_ACTION = (cp -a $(SOURCE_DIR)/* $(@D))

COMPONENT_POST_INSTALL_ACTION =  \
for file in `echo $(PROTO_DIR)/$(CMAKE_PREFIX)/bin/*`; do \
	elfedit -e 'dyn:runpath $(GCC_LIBDIR):$$ORIGIN/../lib' $$file ; \
done && \
for file in \
	`ggrep -rlh '^\#!*/usr/bin/env python' $(PROTO_DIR)/$(CMAKE_PREFIX)`; do \
  sed -i -e 's,^\#!*/usr/bin/env python,\#!$(PYTHON),' $$file; \
done

COMPONENT_TEST_TARGETS = check-all
COMPONENT_TEST_ENV += PATH=/usr/gnu/bin:/usr/bin

CMAKE_OPTIONS += -DCMAKE_C_COMPILER=$(CC)
CMAKE_OPTIONS += -DCMAKE_CXX_COMPILER=$(CXX)
CMAKE_OPTIONS += -DGCC_INSTALL_PREFIX=$(GCC_ROOT)
CMAKE_OPTIONS += -DCMAKE_CXX_LINK_FLAGS="-L$(GCC_LIBDIR) -Wl,-rpath,$(GCC_LIBDIR)"
CMAKE_OPTIONS += -DLLVM_ENABLE_ASSERTIONS=ON
CMAKE_OPTIONS += -DCMAKE_BUILD_TYPE="Release"


build: $(BUILD_64)

install: $(INSTALL_64)

test: $(TEST_64)

# Auto-generated dependencies
REQUIRED_PACKAGES += library/libxml2
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += runtime/python-27
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GCC_RUNTIME_PKG)
REQUIRED_PACKAGES += system/library/math
