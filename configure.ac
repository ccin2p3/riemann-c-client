# -*- Autoconf -*-

AC_INIT([riemann-c-client], [1.3.0], [algernon@madhouse-project.org])
AM_INIT_AUTOMAKE([foreign subdir-objects dist-xz no-dist-gzip])

m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_CONFIG_SRCDIR([lib/riemann/client.c])
AC_CONFIG_HEADERS([lib/riemann/platform.h])
AC_CONFIG_MACRO_DIR([m4])

AC_PROG_CC
AC_PROG_CC_C_O
AC_PROG_CXX

LT_INIT([shared])

AC_CHECK_PROG([HAS_PROTOC_C], [protoc-c], [yes])
if test x$HAS_PROTOC_C != x"yes"; then
   AC_MSG_ERROR([You need protoc-c installed and on your path to proceed. You can find it at http://code.google.com/p/protobuf-c/])
fi

AC_CHECK_HEADERS([arpa/inet.h netdb.h stdlib.h sys/socket.h])
AC_CHECK_FUNCS([memset socket strcasecmp strchr strdup strerror])
AC_FUNC_MALLOC
AC_FUNC_REALLOC

AC_TYPE_SIZE_T
AC_TYPE_UINT32_T
AC_TYPE_UINT8_T
AC_TYPE_INT64_T
AC_TYPE_SSIZE_T

# Check for protobuf-c
AC_ARG_WITH(protobuf-c-dir, AC_HELP_STRING([--with-protobuf-c-dir], [Path to protobuf-c]), 
[if test "x$withval" != "xno" && test "x$withval" != "xyes"; then
    PROTOBUF_C_LDFLAGS="-L$withval/lib"
    PROTOBUF_C_CFLAGS="-I$withval/include"
    AC_SUBST(PROTOBUF_C_LDFLAGS)
    AC_SUBST(PROTOBUF_C_CFLAGS)
fi])

ac_save_LDFLAGS=$LDFLAGS
ac_save_CFLAGS=$CFLAGS

LDFLAGS="$PROTOBUF_C_LDFLAGS $LDFLAGS"
CFLAGS="$PROTOBUF_C_CFLAGS $CFLAGS"
AC_CHECK_LIB(protobuf-c, protobuf_c_message_pack, [], [
    AC_MSG_ERROR([Can't link against protobuf-c lib, use --with-protobuf-c-dir= for non-standard directories])])
LDFLAGS=$ac_save_LDFLAGS
CFLAGS=$ac_save_CFLAGS

# Check for pkg-config-enabled libraries
PKG_PROG_PKG_CONFIG

HAVE_CHECK=yes
PKG_CHECK_MODULES(CHECK, check,,[HAVE_CHECK="no"])
HAVE_JSON_C=yes
PKG_CHECK_MODULES(JSON_C, json-c >= 0.11,,[HAVE_JSON_C="no"])

# Check for linker version script support.
ac_save_LDFLAGS=$LDFLAGS
AC_CACHE_CHECK(whether $LD supports symbol version scripts,
        ac_cv_prog_ld_version_script,
        [ac_cv_prog_ld_version_script=no
         echo "TEST { local: *; };" > conftest.ver
         LDFLAGS="$LDFLAGS -Wl,--version-script,conftest.ver"
         _AC_LINK_IFELSE([AC_LANG_PROGRAM()],
                [ac_cv_prog_ld_version_script=yes], [])
        ])
LDFLAGS=$ac_save_LDFLAGS
AM_CONDITIONAL([HAVE_VERSIONING], [test x$ac_cv_prog_ld_version_script = xyes])

AC_CHECK_PROG([HAVE_RONN], [ronn], [yes])
AM_CONDITIONAL([HAVE_RONN], [test x$HAVE_RONN = xyes])
AM_CONDITIONAL([HAVE_JSON_C], [test x$HAVE_JSON_C != xno])
AM_CONDITIONAL([HAVE_CHECK], [test x$HAVE_CHECK != xno])

AC_DEFINE_UNQUOTED([HAVE_JSON_C],
  `if test x$HAVE_JSON_C != xno; then echo 1; else echo 0; fi`,
  [Define when json-c is available])

AC_CONFIG_FILES([Makefile lib/riemann/riemann-client.pc])
AC_OUTPUT
