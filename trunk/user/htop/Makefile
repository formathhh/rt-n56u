SRC_NAME = htop-3.0.2
SRC_URL = https://github.com/htop-dev/htop/archive/refs/tags/3.0.2.tar.gz
THISDIR = $(shell pwd)

all: download_test extract_test config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(SRC_NAME).tar.gz ]; then \
		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME).tar.gz $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar zxf $(SRC_NAME).tar.gz; \
		# 修复 GitHub 源码包的双层目录结构 \
		if [ -d "$(SRC_NAME)/$(SRC_NAME)" ]; then \
			mv "$(SRC_NAME)/$(SRC_NAME)" tmp_htop_dir; \
			rm -rf $(SRC_NAME); \
			mv tmp_htop_dir $(SRC_NAME); \
		fi; \
		# 确保有 configure 脚本 \
		cd $(SRC_NAME) && [ ! -f configure ] && autoreconf -i; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
	# 确保 configure 脚本存在 \
	if [ ! -f configure ]; then \
		autoreconf -i; \
	fi; \
	./configure \
		--prefix=/usr \
		--enable-unicode \
		HTOP_NCURSESW_CONFIG_SCRIPT="$(STAGEDIR)/bin/ncursesw6-config" \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done

romfs:
	$(ROMFSINST) -p +x $(THISDIR)/$(SRC_NAME)/htop /usr/bin/htop
