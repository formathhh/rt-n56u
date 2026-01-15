SRC_NAME = htop-3.0.2
SRC_URL = https://github.com/htop-dev/htop/archive/refs/tags/3.0.2.tar.gz
THISDIR = $(shell pwd)

all: download_test extract_test config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(SRC_NAME).tar.gz ]; then \
		echo "下载 htop 源码..."; \
		wget -t5 --timeout=30 --no-check-certificate -O $(SRC_NAME).tar.gz $(SRC_URL) || \
		wget -t5 --timeout=30 --no-check-certificate --content-disposition $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		echo "解压 htop 源码..."; \
		rm -rf $(SRC_NAME) tmp_htop_extract 2>/dev/null; \
		# 先解压看看目录结构
		tar zxf $(SRC_NAME).tar.gz 2>/dev/null; \
		# 检查可能的目录名
		if [ -d "htop-3.0.2" ]; then \
			mv htop-3.0.2 $(SRC_NAME); \
		elif [ -d "htop-htop-3.0.2" ]; then \
			mv htop-htop-3.0.2 $(SRC_NAME); \
		elif [ -d "htop-htop-3.0.2" ]; then \
			mv htop-htop-3.0.2 $(SRC_NAME); \
		else \
			# 如果都不匹配，用strip方式
			mkdir -p $(SRC_NAME); \
			tar zxf $(SRC_NAME).tar.gz --strip-components=1 -C $(SRC_NAME); \
		fi; \
		# 最终检查
		if [ ! -f $(SRC_NAME)/configure ]; then \
			echo "错误: 解压后找不到 configure 文件"; \
			ls -la; \
			ls -la $(SRC_NAME)/ 2>/dev/null || echo "目录不存在"; \
			exit 1; \
		fi; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
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
