# Created by: ijliao
# $FreeBSD$

PORTNAME=	gregexp
PORTVERSION=	0.4
PORTREVISION=	1
CATEGORIES=	misc
MASTER_SITES=	http://dentrassi.de/download/gregexp/${PORTVERSION}/

MAINTAINER=	ports@FreeBSD.org
COMMENT=	Graphical regular expression explorer

DEPRECATED=	GNOME 2 application
EXPIRATION_DATE=	2018-12-10

LIB_DEPENDS=	libpcre.so:devel/pcre

USES=		pkgconfig
USE_GNOME=	gnomeprefix libgnomeui
GNU_CONFIGURE=	yes
CPPFLAGS+=	-I${LOCALBASE}/include
LDFLAGS+=	-L${LOCALBASE}/lib

.include <bsd.port.mk>
