#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
# Copyright (c) 2013, Colin Ellis. All rights reserved.
# Copyright (c) 2011, 2012, Oracle and/or its affiliates. All rights reserved.
#
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		gnupg
COMPONENT_VERSION=	2.0.30
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_PROJECT_URL=	http://www.gnupg.org/
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.bz2
COMPONENT_ARCHIVE_HASH=	\
    sha256:e329785a4f366ba5d72c2c678a7e388b0892ac8440c2f4e6810042123c235d71
COMPONENT_ARCHIVE_URL=	ftp://ftp.gnupg.org/gcrypt/gnupg/$(COMPONENT_ARCHIVE)
COMPONENT_SIG_URL=	$(COMPONENT_ARCHIVE_URL).sig	
COMPONENT_BUGDB=	utility/gnupg

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/configure.mk
include $(WS_MAKE_RULES)/ips.mk

# Enable C99 mode + -Xc for its additional warnings.
#studio_C99MODE = -Xc $(studio_C99_ENABLE)

# -xinline=%auto -- we like inlining where appropriate
#CFLAGS += -xinline=%auto

# -xbuiltin=%none -- builtins have been known to be buggy
#CFLAGS += -xbuiltin=%none

CFLAGS += $(CPP_LARGEFILES)
#CFLAGS += $(XPG6MODE)
#CFLAGS += $(CPP_POSIX)
#CFLAGS += $(CPP_C99_EXTENDED_MATH)
#CFLAGS += $(studio_PIC)

# If we pass -lc, -mt won't link with -lthread
# (which is what it does by default, and we don't
# want that, because we use POSIX threads).
LDFLAGS += -lc -z defs
LD_OPTIONS += -z defs

CONFIGURE_ENV +=	CFLAGS="$(CFLAGS)"
CONFIGURE_ENV +=	LDFLAGS="$(LDFLAGS)"
CONFIGURE_ENV +=	INSTALL="$(INSTALL)"

CONFIGURE_OPTIONS  +=		--localstatedir=/var
CONFIGURE_OPTIONS  +=		--libexecdir=$(USRBINDIR)
CONFIGURE_OPTIONS  +=		--datadir=$(CONFIGURE_PREFIX)/share
CONFIGURE_OPTIONS  +=		--sysconfdir=/etc
CONFIGURE_OPTIONS  +=		--infodir=$(CONFIGURE_INFODIR)
CONFIGURE_OPTIONS  +=		--enable-nls
CONFIGURE_OPTIONS  +=		--enable-largefile
CONFIGURE_OPTIONS  +=		--disable-selinux-support
CONFIGURE_OPTIONS  +=		--with-pinentry-pgm=$(CONFIGURE_PREFIX)/lib/pinentry
CONFIGURE_OPTIONS  +=		--with-gpg-error-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-libassuan-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-ksba-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-pth-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-libcurl=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-libiconv-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-libintl-prefix=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-zlib=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-bzip2=$(CONFIGURE_PREFIX)
CONFIGURE_OPTIONS  +=		--with-readline=$(CONFIGURE_PREFIX)

LIBKSBA_MANIFEST = 	manifest-$(MACH)-libksba.depend
LIBASSUAN_MANIFEST =	manifest-$(MACH)-libassuan.depend
GPGME_MANIFEST =	manifest-$(MACH)-gpgme.depend
PINENTRY_MANIFEST =	manifest-$(MACH)-pinentry.depend

# Work around pkgdepend bug which doesn't resolve dependencies from
# the local workspace repo. Fortunately we only have to do this once.
DEPENDED +=	$(WS_TOP)/components/library/libksba/build/$(LIBKSBA_MANIFEST)
DEPENDED +=	$(WS_TOP)/components/library/libassuan/build/$(LIBASSUAN_MANIFEST)
DEPENDED +=	$(WS_TOP)/components/sysutils/gpgme/build/$(GPGME_MANIFEST)
DEPENDED +=	$(WS_TOP)/components/sysutils/pinentry/build/$(PINENTRY_MANIFEST)

PKG_PROTO_DIRS += $(WS_TOP)/components/library/libksba
PKG_PROTO_DIRS += $(WS_TOP)/components/library/libassuan
PKG_PROTO_DIRS += $(WS_TOP)/components/sysutils/gpgme
PKG_PROTO_DIRS += $(WS_TOP)/components/sysutils/pinentry

MAN8LIST = addgnupghome applygnupgdefaults

build: $(BUILD_32)

install: $(INSTALL_32)
	( cd $(PROTOUSRSHARELOCALEDIR) ; \
	    $(CP) -R 'en@boldquot' en )
	( cd $(PROTOUSRSHAREMANDIR) ; \
	    if test -d man8 ; then \
	    $(MV) man8 man1m ; fi )
	( cd $(PROTOUSRSHAREMANDIR)/man1m ; \
	    list1='$(MAN8LIST)' ; for f in $$list1; do \
	    if test -f $$f.8 ; then \
	    $(MV) $$f.8 $$f.1m ; fi ; \
	    done )

test: $(TEST_32)

# Missing dependencies for pinentry
REQUIRED_PACKAGES += library/desktop/gtk2
REQUIRED_PACKAGES += library/glib2
REQUIRED_PACKAGES += library/libsecret

# Auto-generated dependencies
REQUIRED_PACKAGES += SUNWcs
REQUIRED_PACKAGES += compress/bzip2
REQUIRED_PACKAGES += library/pth
REQUIRED_PACKAGES += library/readline
REQUIRED_PACKAGES += library/security/libassuan
REQUIRED_PACKAGES += library/security/libgpg-error
REQUIRED_PACKAGES += library/security/libksba
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += security/pinentry
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/security/libgcrypt
REQUIRED_PACKAGES += system/library/usb/libusb
REQUIRED_PACKAGES += web/curl
