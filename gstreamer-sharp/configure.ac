AC_INIT
AC_CONFIG_SRCDIR([README])
AC_CANONICAL_TARGET
AM_INIT_AUTOMAKE(gstreamer-sharp, 0.10)
AM_MAINTAINER_MODE
AC_PROG_INSTALL

dnl Package settings
ASSEMBLY_NAME=$PACKAGE
ASSEMBLY_VERSION="0.10.0.0"
ASSEMBLY_NAMESPACE="GStreamer"
ASSEMBLY_SHORTNAME="gstreamer"
ASSEMBLY_TITLE="GStreamer#"
ASSEMBLY_DESCRIPTION="GStreamer bindings for Mono"
ASSEMBLY_COMPANY=""
ASSEMBLY_COPYRIGHT="2008 Paul Burton"
ASSEMBLY_PC=$ASSEMBLY_NAME-0.10

AC_SUBST(ASSEMBLY_NAME)
AC_SUBST(ASSEMBLY_VERSION)
AC_SUBST(ASSEMBLY_NAMESPACE)
AC_SUBST(ASSEMBLY_SHORTNAME)
AC_SUBST(ASSEMBLY_TITLE)
AC_SUBST(ASSEMBLY_DESCRIPTION)
AC_SUBST(ASSEMBLY_COMPANY)
AC_SUBST(ASSEMBLY_COPYRIGHT)
AC_SUBST(ASSEMBLY_PC)

dnl Check for pkg-config
AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
if test "x$PKG_CONFIG" = "xno"; then
	AC_MSG_ERROR(['pkg-config' is not in your PATH.])
fi

dnl Check for Mono
PKG_CHECK_MODULES(MONO_DEPENDENCY, mono >= 1.0, has_mono=true, has_mono=false)
PKG_CHECK_MODULES(MONO_DEPENDENCY, mint >= 1.0, has_mint=true, has_mint=false)

if test "x$has_mono" = "xtrue"; then
	if test `uname -s` = "Darwin"; then
		AC_PATH_PROG(RUNTIME, mono, no)
		AC_PATH_PROG(CSC, mcs, no)
		LIB_PREFIX=
		LIB_SUFFIX=.dylib
	else
		AC_PATH_PROG(RUNTIME, mono, no)
		AC_PATH_PROG(CSC, mcs, no)
		LIB_PREFIX=.so
		LIB_SUFFIX=
	fi
else
	if test "x$has_mint" = "xtrue"; then
		AC_PATH_PROG(RUNTIME, mint, no)
		AC_PATH_PROG(CSC, mcs, no)
		LIB_PREFIX=.so
		LIB_SUFFIX=
	else
		AC_PATH_PROG(CSC, csc.exe, no)
		if test x$CSC = "xno"; then
			AC_MSG_ERROR([You need to install either mono or .Net])
		else
			RUNTIME=
			LIB_PREFIX=
			LIB_SUFFIX=.dylib
		fi
	fi
fi

CS="C#"
if test "x$CSC" = "xno" ; then
    AC_MSG_ERROR([No $CS compiler found])
fi

AC_PATH_PROG(GACUTIL, gacutil, no)
if test "x$GACUTIL" = "xno" ; then
	AC_MSG_ERROR([No gacutil tool found])
fi

GACUTIL_FLAGS="/package $ASSEMBLY_NAME /root "'$(DESTDIR)$(prefix)/lib'
                                     
AC_SUBST(RUNTIME)
AC_SUBST(CSC)
AC_SUBST(GACUTIL)
AC_SUBST(GACUTIL_FLAGS)
AC_SUBST(LIB_PREFIX)
AC_SUBST(LIB_SUFFIX)

dnl Check for glib-sharp
PKG_CHECK_MODULES(GLIB_SHARP, glib-sharp-2.0 >= 2.12.0)
AC_SUBST(GLIB_SHARP_CFLAGS)
AC_SUBST(GLIB_SHARP_LIBS)
glibsharp_prefix=`pkg-config --variable=prefix glib-sharp-2.0`
AC_SUBST(glibsharp_prefix)

dnl Check for gstreamer
PKG_CHECK_MODULES(GSTREAMER, gstreamer-0.10)
AC_SUBST(GSTREAMER_CFLAGS)
AC_SUBST(GSTREAMER_LIBS)
gstreamer_prefix=`pkg-config --variable=prefix gstreamer-0.10`
AC_SUBST(gstreamer_prefix)

dnl Check for gapi
AC_PATH_PROG(GAPI_PARSER, gapi2-parser, no)
if test "x$GAPI_PARSER" = "xno"; then
	AC_MSG_CHECKING(for gapi2-parser.exe)
	GAPI_PARSER=`which gapi2-parser.exe 2> /dev/null`
	if test "x$GAPI_PARSER" = "xno" ; then
		AC_MSG_ERROR(['gapi2_parser'/'gapi2-parser.exe' not found.])
	fi
	AC_MSG_RESULT($GAPI_PARSER)
	GAPI_PARSER="$MONO $GAPI_PARSER"
fi
AC_SUBST(GAPI_PARSER)

GAPI_FIXUP="$MONO "`pwd`"/gapi-fixup.exe"
#AC_PATH_PROG(GAPI_FIXUP, gapi2-fixup, no)
if test "x$GAPI_FIXUP" = "xno"; then
	AC_MSG_CHECKING(for gapi2-fixup.exe)
	GAPI_FIXUP=`which gapi2-fixup.exe 2> /dev/null`
	if test "x$GAPI_FIXUP" = "xno" ; then
		AC_MSG_ERROR(['gapi2_fixup'/'gapi2-fixup.exe' not found.])
	fi
	AC_MSG_RESULT($GAPI_FIXUP)
	GAPI_FIXUP="$MONO $GAPI_FIXUP"
fi
AC_SUBST(GAPI_FIXUP)

GAPI_CODEGEN="$MONO "`pwd`"/gapi2_codegen.exe"
#AC_PATH_PROG(GAPI_CODEGEN, gapi2-codegen, no)
if test "x$GAPI_CODEGEN" = "xno"; then
	AC_MSG_CHECKING(for gapi2_codegen.exe)
	GAPI_CODEGEN=`which gapi2_codegen.exe 2> /dev/null`
	if test "x$GAPI_CODEGEN" = "xno" ; then
		AC_MSG_ERROR(['gapi2-codegen'/'gapi2_codegen.exe' not found.])
	fi
	AC_MSG_RESULT([$GAPI_CODEGEN])
	GAPI_CODEGEN="$MONO $GAPI_CODEGEN"
fi
AC_SUBST(GAPI_CODEGEN)

dnl Check for monodoc
AC_PATH_PROG(MDASSEMBLER, mdassembler, no)
AC_PATH_PROG(MONODOCER, monodocer, no)
if test "x$MONODOCER" = "xno" -o "x$MDASSEMBLER" = "xno"; then
	enable_monodoc=no
	doc_sources_dir=
else
	enable_monodoc=yes
	doc_sources_dir="`pkg-config --variable=sourcesdir monodoc`"
fi
AC_SUBST(MDASSEMBLER)
AC_SUBST(MONODOCER)

AM_CONDITIONAL(ENABLE_MONODOC, test "x$enable_monodoc" = "xyes")

AC_CONFIG_FILES([
	Makefile
	doc/Makefile
	samples/Makefile
	sources/Makefile
	sources/glue/Makefile
	sources/AssemblyInfo.cs
	sources/gstreamer-sharp-0.10.pc:sources/gstreamer-sharp.pc.in
	sources/gstreamer-sharp.dll.config
])
AC_CONFIG_COMMANDS([default],[[ echo timestamp > stamp-h ]],[[]])
AC_OUTPUT

echo ""
echo "Configuration summary"
echo "---------------------"
echo ""
echo "   * Installation prefix: $prefix"
echo "   * compiler: $CSC"
echo "   * Documentation: $enable_monodoc ($MONODOC)"
echo ""

