# $FreeBSD:$

PORTNAME=	evolvere
DISTVERSION=	1.2dev
CATEGORIES=	x11-themes kde gnome xfce
PKGNAMESUFFIX=	-icon-theme

MAINTAINER=	d.akiyakov@gmail.com
COMMENT=	Suit contains icon themes for KDE, Gnome, Xfce, etc

LICENSE=	CC-BY-SA-4.0
LICENSE_NAME=	Creative Commons Attribution-ShareAlike 4.0 International \
		Public License
LICENSE_FILE=	${WRKSRC}/LICENSE
LICENSE_PERMS=	dist-mirror dist-sell pkg-mirror pkg-sell auto-accept

GH_ACCOUNT=	franksouza183
GH_PROJECT=	EvolvereSuit
GH_TAGNAME=	${GH_COMMIT}
GH_COMMIT=	b10683d

USE_GITHUB=	yes
NO_BUILD=	yes

WRKSRC=		${WRKDIR}/${GH_ACCOUNT}-${GH_PROJECT}-${GH_TAGNAME}/icons

.include "${.CURDIR}/Makefile.options"
.include <bsd.port.options.mk>

#exclude folders which is not suitable
DO_NOT_EXTRACT=	adium-message-style aurorae-themes kde-color-schemes kde-plasma-themes \
		kvantum-themes wallpapers icons/evolbuntu icons/evolbuntu-dark \
		icons/evolbuntu-dark-fallback icons/evolbuntu-dark-light-fallback \
		icons/evolbuntu-vivid icons/evolbuntu-vivid-dark icons/evolbuntu-vivid-dark-fallback \
		icons/evolbuntu-vivid-dark-light-fallback
.for dne in ${DO_NOT_EXTRACT}
EXTRACT_AFTER_ARGS+=	--exclude '${GH_ACCOUNT}-${GH_PROJECT}-${GH_TAGNAME}/${dne}'
.endfor
#exclude all icon themes which is not selected
.for k in ${OPTIONS_MULTI_ICONS}
.if ${PORT_OPTIONS:M${k}}
# we need to make a copy of LICENSE in WRKSRC
.if !target(post-extract) && !exists(${WRKSRC}/LICENSE)
post-extract:
	@cd ${WRKSRC}/${${k}_FOLDER} && \
	${CP} -n LICENSE ../
.endif
.else
EXTRACT_AFTER_ARGS+=	--exclude '${GH_ACCOUNT}-${GH_PROJECT}-${GH_TAGNAME}/icons/${${k}_FOLDER}'
.endif
.endfor

# annotate what should be installed
annotate-install:
# annotate files
	@cd ${WRKSRC} && \
		${FIND} . -type f ! -empty ! -name "LICENSE" \
		| ${SORT} \
		> ${WRKDIR}/files.list
# annotate links
	@cd ${WRKSRC} ; ${FIND} . -type l ! -empty \
		| ${SORT} \
		> ${WRKDIR}/links.list

# generate on the fly packaging lists based on aforementioned annotations
generate-tmpplist:
	@${CAT} ${WRKDIR}/files.list ${WRKDIR}/links.list \
		| ${SED} -E \
			-e 's|^\.|share/icons|' \
				| ${SORT} \
				>> ${TMPPLIST}

pre-install:
.for i in ${OPTIONS_MULTI_ICONS}
.if ${PORT_OPTIONS:M${i}}
#replace menu icons with FreeBSD logo icons
.if ${PORT_OPTIONS:MFBSDLOGO}
.for j in 16 18 22 24 64
	@cd ${WRKSRC}/${${i}_FOLDER}/places/${j} && \
	${LN} -fs ../../apps/${j}/freebsd.svg start-here-kde.svg && \
	${LN} -fs ../../apps/${j}/freebsd.svg start-here.svg && \
	cd ${WRKSRC}/${${i}_FOLDER}/apps/${j} && \
	${LN} -fs freebsd.svg start-here.svg && \
	${LN} -fs freebsd.svg start-here-kde.svg && \
	${LN} -fs freebsd.svg xfce4-panel-menu.svg && \
	${LN} -fs freebsd.svg menu.svg
.endfor
#temporary issue for Nautilus and Nemo with missing icons sizes
.for folder in places mimetypes devices status categories apps actions
	@cd ${WRKSRC}/${${i}_FOLDER}/${folder} && \
	${RM} -rf 32 48 128 256 && \
	${LN} -s 64 32 && ${LN} -s 64 48 && ${LN} -s 64 128 && ${LN} -s 64 256
.endfor
#
.endif
	@cd ${WRKSRC} && \
		${RM} ${${i}_FOLDER}/LICENSE && \
		${COPYTREE_SHARE} ${${i}_FOLDER} \
		${STAGEDIR}${PREFIX}/share/icons
.endif
.endfor

do-install: pre-install annotate-install generate-tmpplist

.include <bsd.port.mk>
