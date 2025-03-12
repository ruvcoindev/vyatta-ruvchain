#!/usr/bin/make -f
#
# vyatta-ruvchain - A ruvchain package for Ubiquiti EdgeOS, VyOS and other
# Vyatta-based systems.
# Copyright (C) 2015 Neil Alexander T.
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

PKGNAME?=vyatta-$(shell cd $(BUILDDIR_RUV) && sh contrib/semver/name.sh --bare)
PKGARCH?=mipsel
PKGVERSION?=$(shell cd $(BUILDDIR_RUV) && sh contrib/semver/version.sh --bare)

BUILDDIR_RUV?=/tmp/ruvchain

# cd $(BUILDDIR_RUV) && sh contrib/semver/version.sh | cut -c 2-
# cd $(BUILDDIR_RUV) && sh contrib/semver/name.sh

all: cross

cross: clean build-cross package cleanpackage
native: clean build-native package cleanpackage

build-cross: ruvchainclone ruvchainbuild-cross ruvchainstage
build-native: ruvchainclone ruvchainbuild-native ruvchainstage

package:
	@echo "Packaging $(PKGNAME) version $(PKGVERSION) for $(PKGARCH)..."
	@sed -i "s/^Architecture: .*/Architecture: $(PKGARCH)/" debian/control || \
		sed -i "" "s/^Architecture: .*/Architecture: $(PKGARCH)/" debian/control
	@sed -i "s/^Version: .*/Version: $(PKGVERSION)/" debian/control || \
		sed -i "" "s/^Version: .*/Version: $(PKGVERSION)/" debian/control
	@sed -i "s/^Package: .*/Package: $(PKGNAME)/" debian/control || \
		sed -i "" "s/^Package: .*/Package: $(PKGNAME)/" debian/control
	@cat debian/control
	tar czvf ../data.tar.gz opt usr
	cd debian && tar czvf ../../control.tar.gz *
	echo 2.0 > ../debian-binary
	ar -r ../$(PKGNAME)-$(PKGVERSION)-$(PKGARCH).deb ../debian-binary ../control.tar.gz ../data.tar.gz

cleanpackage:
	rm ../debian-binary
	rm ../control.tar.gz
	rm ../data.tar.gz

ruvchainclone:
	@[ "$(BUILDDIR_RUV)" = "/tmp/ruvchain" ] && git clone -b $(BRANCH) https://github.com/ruvcoindev/ruvchain $(BUILDDIR_RUV) || true

ruvchainbuild-cross:
	@[ "$(PKGARCH)" = "mipsel" ] && (cd $(BUILDDIR_RUV) && GOOS='linux' GOARCH='mipsle' GOMIPS='softfloat' ./build) || true
	@[ "$(PKGARCH)" = "mips" ] && (cd $(BUILDDIR_RUV) && GOOS='linux' GOARCH='mips64' ./build) || true
	@[ "$(PKGARCH)" = "amd64" ] && (cd $(BUILDDIR_RUV) && GOOS='linux' GOARCH='amd64' ./build) || true
	@[ "$(PKGARCH)" = "i386" ] && (cd $(BUILDDIR_RUV) && GOOS='linux' GOARCH='386' ./build) || true

ruvchainbuild-native:
	cd $(BUILDDIR_RUV) && GOOS='linux' ./build

ruvchainstage:
	mkdir -p usr/local/bin
	cp $(BUILDDIR_RUV)/ruvchain usr/local/bin/ruvchain
	cp $(BUILDDIR_RUV)/ruvchainctl usr/local/bin/ruvchainctl

clean:
	rm -rf sbin/ruvchain
	@[ "$(BUILDDIR_RUV)" = "/tmp/ruvchain" ] && rm -rf $(BUILDDIR_RUV) || true

distclean:	clean
