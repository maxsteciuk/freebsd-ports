# Created by: Gerald Pfeifer <gerald@FreeBSD.org>
# $FreeBSD$

PORTNAME=	wine
DISTVERSION=	3.21
PORTEPOCH=	1
CATEGORIES=	emulators
MASTER_SITES=	SF/${PORTNAME}/Source \
		http://mirrors.ibiblio.org/wine/source/3.x/
PKGNAMESUFFIX?=	-devel
DISTFILES=	${DISTNAME}${EXTRACT_SUFX}

MAINTAINER?=	gerald@FreeBSD.org
COMMENT?=	Microsoft Windows compatibility environment

LICENSE=	LGPL21 LGPL3
LICENSE_COMB=	dual
LICENSE_FILE=	${WRKSRC}/LICENSE

ONLY_FOR_ARCHS=	amd64 i386

BUILD_DEPENDS=	${LOCALBASE}/bin/flex:textproc/flex \
		prelink:sysutils/prelink
LIB_DEPENDS=	libxml2.so:textproc/libxml2

CONFLICTS_INSTALL=	wine-[0-9]* wine-staging-[0-9]* i386-wine-[0-9]* \
			i386-wine-staging-[0-9]* i386-wine-devel-[0-9]*

GNU_CONFIGURE=	yes
CONFIGURE_ARGS+=--verbose \
		--disable-kerberos \
		--disable-tests \
		--without-alsa --without-capi --without-dbus \
		--without-gettext --without-gettextpo \
		--without-gphoto --without-gsm --without-gstreamer \
		--without-krb5 \
		--without-opencl \
		--without-osmesa \
		--with-oss \
		--without-pulse \
		--without-sane \
		--without-tiff \
		--without-udev
CONFIGURE_ENV=	CPPBIN="${CPP}" FLEX="${LOCALBASE}/bin/flex"
WINELIBDIR?=	${PREFIX}/lib
USES=		bison compiler:c11 desktop-file-utils gmake localbase shebangfix tar:xz
.if !defined(USE_LDCONFIG32)
USE_LDCONFIG=	${WINELIBDIR} ${WINELIBDIR}/wine
.endif
SHEBANG_FILES=	tools/make_requests tools/winemaker/winemaker \
		tools/winedump/function_grep.pl

SUB_FILES=	pkg-message

PORTDATA=	l_intl.nls wine.inf

OPTIONS_DEFINE=	CUPS DOCS DOSBOX GNUTLS HAL LDAP LIBXSLT MPG123 OPENAL STAGING V4L VKD3D VULKAN WINEMAKER X11 GECKO MONO
OPTIONS_DEFAULT=GNUTLS X11
OPTIONS_SUB=	yes

DOSBOX_DESC=	Use DOSBox to run MS-DOS programs
LIBXSLT_DESC=	Use libxslt (only used by msxml3.dll)
VKD3D_DESC=	Direct3D 12 support via Vulkan
VULKAN_DESC=	Vulkan API support
WINEMAKER_DESC=	Fully support winemaker (requires Perl)
GECKO_DESC=	Bundle Gecko MSI package for Wine
MONO_DESC=	Bundle Mono MSI package for Wine

CUPS_CONFIGURE_WITH=	cups
CUPS_LIB_DEPENDS=	libcups.so:print/cups

DOSBOX_RUN_DEPENDS=	dosbox:emulators/dosbox

GECKO_RUN_DEPENDS=	${DATADIR}/gecko/wine_gecko-2.47-x86.msi:emulators/wine-gecko-devel

GNUTLS_CONFIGURE_WITH=	gnutls
GNUTLS_LIB_DEPENDS=	libgnutls.so:security/gnutls

HAL_CONFIGURE_WITH=	hal
HAL_LIB_DEPENDS=	libhal.so:sysutils/hal

LDAP_CONFIGURE_WITH=	ldap
LDAP_USE=		OPENLDAP=yes

LIBXSLT_CONFIGURE_WITH=	xslt
LIBXSLT_LIB_DEPENDS=	libxslt.so:textproc/libxslt

MONO_RUN_DEPENDS=	${DATADIR}/mono/wine-mono-4.7.3.msi:emulators/wine-mono-devel

MPG123_CONFIGURE_WITH=	mpg123
MPG123_LIB_DEPENDS=	libmpg123.so:audio/mpg123

OPENAL_CONFIGURE_WITH=	openal
OPENAL_USES=		openal

V4L_CONFIGURE_WITH=	v4l
V4L_BUILD_DEPENDS=	${LOCALBASE}/include/linux/videodev2.h:multimedia/v4l_compat
V4L_LIB_DEPENDS=	libv4l1.so:multimedia/libv4l

VKD3D_CONFIGURE_WITH=	vkd3d
VKD3D_USES=		pkgconfig
VKD3D_LIB_DEPENDS=	libvkd3d.so:graphics/vkd3d

VULKAN_CONFIGURE_WITH=	vulkan
VULKAN_LIB_DEPENDS=	libvulkan.so:graphics/vulkan-loader

WINEMAKER_RUN_DEPENDS=	p5-XML-LibXML>0:textproc/p5-XML-LibXML

X11_CONFIGURE_WITH=	x cms fontconfig freetype glu jpeg opengl png xinerama xinput2 xrandr xrender
X11_USES=		jpeg
X11_USE=		GL=glu XORG=x11,xext,xcomposite,xcursor,xi,xinerama,xrandr,xrender
X11_LIB_DEPENDS=	libfontconfig.so:x11-fonts/fontconfig \
			libfreetype.so:print/freetype2 \
			liblcms2.so:graphics/lcms2 \
			libpng.so:graphics/png

.include <bsd.port.pre.mk>

.if ${ARCH} == amd64
# Wine is composed of three parts:
#  - wine (aka this port) is the 32-bit component and runs 32-bit programs (on FreeBSD/i386)
#  - wine64 (aka this part of this port) is the 64-bit component and runs 64-bit programs
#    (on FreeBSD/amd64)
#  - wow64 (a subset of emulators/i386-wine-devel) is a part of the 64-bit component that runs
#    32-bit programs (on FreeBSD/amd64)
# We also currently have:
#  - "wine32" (aka emulators/i386-wine-devel) is the 32-bit component and runs 32-bit programs
#    (on FreeBSD/amd64).  This will be superseded by wow64.

# --libdir is required since Wine defaults to "${PREFIX}/lib64" on amd64.
CONFIGURE_ARGS+=	--enable-win64 --libdir=${PREFIX}/lib
PLIST_SUB+=		WINE32="@comment " WINE64=""

.if ${CHOSEN_COMPILER_TYPE} == clang && ${COMPILER_VERSION} < 38
CPPFLAGS+=		-D__builtin_ms_va_list=__builtin_va_list \
			-D__builtin_ms_va_start=__builtin_va_start \
			-D__builtin_ms_va_end=__builtin_va_end \
			-D__builtin_ms_va_copy=__builtin_va_copy
.endif

# Wine assumes a WoW64 package is available, however WoW64 is not yet
# available on FreeBSD. Also fix the desktop file.
post-patch:
	${REINPLACE_CMD} 's|/wine|/wine64|g' ${WRKSRC}/tools/wineapploader.in
	${REINPLACE_CMD} '/Exec/s|wine|wine64|g' ${WRKSRC}/loader/wine.desktop

.else
PLIST_SUB+=		WINE32="" WINE64="@comment "
.endif

.if ${PORT_OPTIONS:MSTAGING}
MASTER_SITES+=	https://github.com/wine-staging/wine-staging/archive/:cho
COMP_DIST=	v${DISTVERSION}.tar.gz
DISTFILES+=	${COMP_DIST}:cho

EXTRACT_DEPENDS=bash:shells/bash \
		git:devel/git \
		autoconf>0:devel/autoconf
LIB_DEPENDS+=	libtxc_dxtn.so:graphics/s2tc

post-extract: fix-shebang
	cd ${WRKSRC} && ${TAR} xf ${DISTDIR}/${COMP_DIST} --strip-components 1
	cd ${WRKSRC} && bash ./patches/patchinstall.sh --backend=patch DESTDIR=${WRKSRC} --all
.endif

pre-build:
	cd ${WRKSRC} && ${MAKE_CMD} depend

post-install:
	@${MV} -f ${STAGEDIR}${WINELIBDIR}/libwine.so.1.0 \
	          ${STAGEDIR}${WINELIBDIR}/libwine.so.1
	@${LN} -sf libwine.so.1 ${STAGEDIR}/${WINELIBDIR}/libwine.so
	@${MKDIR} ${STAGEDIR}${DOCSDIR}
.for i in README ANNOUNCE AUTHORS
	${INSTALL_DATA} ${WRKSRC}/${i} ${STAGEDIR}${DOCSDIR}
.endfor
	${INSTALL_DATA} ${WRKSRC}/programs/winedbg/README ${STAGEDIR}${DOCSDIR}/README.winedbg
	${INSTALL_DATA} ${WRKSRC}/tools/winedump/README ${STAGEDIR}${DOCSDIR}/README.winedump

check-wine-devel-vs-staging:
	@${GREP} %%STAGING%% pkg-plist | while read f; do \
	  f=`echo $$f | ${SED} -e 's/%%STAGING%%//'`; \
	  if [ -f "$$STAGEDIR/$$PREFIX/$$f" ]; then \
	    ${PRINTF} "%s no longer contingent on %%STAGING%%?\n" "$$f"; \
	  fi \
	done

.include <bsd.port.post.mk>
