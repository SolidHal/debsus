.DEFAULT_GOAL := default
#import all shared make vars
PRAWNOS_ROOT := $(shell git rev-parse --show-toplevel)
include $(PRAWNOS_ROOT)/scripts/BuildScripts/BuildCommon.mk

ifeq ($(TARGET),$(PRAWNOS_ARMHF))
$(error Cant built armhf kernel package with armhf target)
endif

#package specific vars
VERSION := $(PRAWNOS_KERNEL_IMAGE_CAT_VER)
PACKAGE := prawnos-linux-image-arm64
PACKAGE_ARCH := arm64

#uses the full debian version for the package name
PACKAGE_NAME :=  $(PACKAGE)_$(VERSION)_$(PACKAGE_ARCH)
PACKAGE_NAME_DEB := $(PACKAGE_NAME).deb
#uses only the upstream kernel version for the orig tar
PACKAGE_NAME_ORIG := $(PACKAGE)_$(KVER)-$(PRAWNOS_KERNEL_VER)
PACKAGE_NAME_ORIG_TAR := $(PACKAGE_NAME_ORIG).orig.tar.gz

ORIG := $(PRAWNOS_KERNEL_PACKAGE_IMAGE)/orig

default: $(PACKAGE_NAME_DEB)


$(PACKAGE_NAME_DEB): $(PACKAGE_NAME_ORIG_TAR)
	@echo Building $@
# keep the /debian files up to date with the current kernel version
	$(PRAWNOS_KERNEL_SCRIPTS_UPDATE_DEB_FILES) $(PRAWNOS_KERNEL_PACKAGE_IMAGE)/src $(VERSION)

	$(PRAWNOS_PACKAGE_SCRIPTS_PBUILD) $(PACKAGE_NAME_DEB) \
                                            $(PBUILDER_VARS) \
                                            $(PRAWNOS_LOCAL_APT_VARS) \
                                            $(PACKAGE_LOCAL_BUILD_DEPS)

$(PACKAGE_NAME_ORIG_TAR):
	make -f $(PRAWNOS_KERNEL)/makefile kernel
	cp $(PRAWNOS_KERNEL_BUILT) $(ORIG)/$(PACKAGE_NAME_ORIG)
	tar -czf $(PACKAGE_NAME_ORIG_TAR) -C $(ORIG)/ .


.PHONY: clean_all
clean_all: clean clean_orig

.PHONY: clean
clean:
	rm -rf *.upload
	rm -rf *.deb
	rm -rf *.changes
	rm -rf *.dsc
	rm -rf *.build
	rm -rf *.diff.gz
	rm -rf *.debian.tar.xz
	rm -rf *.buildinfo
	rm -f src/debian/debhelper-build-stamp
	rm -rf src/debian/$(PACKAGE)*
	rm -f src/debian/files
	rm -rf src/debian/.debhelper
# kernel package specific
	rm -f orig/$(PACKAGE)_*-*

#when just changing the debian package version, if we remake the orig as well then reprepro will complain on upload. So we might not always want to remove the orig.
.PHONY: clean_orig
clean_orig:
	rm -f $(PACKAGE)_*-*.orig.tar.gz

.PHONY: install
install:
	$(PRAWNOS_PACKAGE_SCRIPTS_INSTALL) $(PACKAGE_NAME_DEB) $(INSTALL_TARGET)

.PHONY: upload
upload:
	$(PRAWNOS_PACKAGE_SCRIPTS_UPLOAD)