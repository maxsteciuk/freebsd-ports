# Created by: roam@FreeBSD.org
# $FreeBSD$

PORTNAME=	bsdproxy
PORTVERSION=	0.04
PORTREVISION=	1
CATEGORIES=	net
MASTER_SITES=	http://daveb.net/bsdproxy/

MAINTAINER=	ports@FreeBSD.org
COMMENT=	TCP proxy, demonstrating use of the kevent(2)/kqueue(2) API

DEPRECATED=	Uses obsolete glib12
EXPIRATION_DATE=	2018-12-20

GNU_CONFIGURE=	yes
USE_GNOME=	glib12

PLIST_FILES=	bin/${PORTNAME}
PORTDOCS=	ChangeLog README

OPTIONS_DEFINE=	DOCS

post-install:
	${MKDIR} ${STAGEDIR}${DOCSDIR}
	${INSTALL_DATA} ${PORTDOCS:S,^,${WRKSRC}/,} ${STAGEDIR}${DOCSDIR}

.include <bsd.port.mk>
