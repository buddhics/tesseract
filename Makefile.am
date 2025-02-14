## run autogen.sh to create Makefile.in from this file
ACLOCAL_AMFLAGS = -I m4

if ENABLE_TRAINING
TRAINING_SUBDIR = src/training
training: all
	@$(MAKE) -C src/training
training-install: training
	@$(MAKE) -C src/training install
training-uninstall:
	@$(MAKE) -C src/training uninstall
clean-local:
	@$(MAKE) -C src/training clean
# Some unit tests use code from training.
check: training
else
training:
	@echo "Need to reconfigure project, so there are no errors"
endif

.PHONY: doc install-langs ScrollView.jar install-jars training

SUBDIRS = . tessdata doc unittest

EXTRA_DIST = README.md LICENSE
EXTRA_DIST += aclocal.m4 config configure.ac autogen.sh
EXTRA_DIST += tesseract.pc.in $(TRAINING_SUBDIR) java doc
EXTRA_DIST += CMakeLists.txt tesseract.pc.cmake cmake VERSION src/vs2010 cppan.yml

DIST_SUBDIRS = $(SUBDIRS) $(TRAINING_SUBDIR)

uninstall-hook:
	rm -rf $(DESTDIR)$(pkgincludedir)

dist-hook:
# Need to remove .svn directories from directories
# added using EXTRA_DIST. $(distdir)/tessdata would in
# theory suffice.
	rm -rf `find $(distdir) -name .deps -type d`
	-rm -f $(distdir)/*/Makefile $(distdir)/*/*/Makefile
	rm -f `find $(distdir) -name '*~'`
	rm -rf $(find $(distdir)/src/training -executable -type f)
	rm -rf $(distdir)/doc/html/* $(distdir)/doc/*.log

ScrollView.jar:
	@cd "$(top_builddir)/java" && $(MAKE) $@

install-jars:
	@cd "$(top_builddir)/java" && $(MAKE) $@

doc:
	-srcdir="$(top_srcdir)" builddir="$(top_builddir)" \
	version="@PACKAGE_VERSION@" name="@PACKAGE_NAME@" \
	doxygen $(top_srcdir)/doc/Doxyfile

doc-pack: doc
	-chmod a+r $(top_builddir)/doc/html/*
	 @tar --create --directory=$(top_builddir)/doc/html --verbose --file=- . | gzip -c -9 > $(top_builddir)/@PACKAGE_NAME@-@PACKAGE_VERSION@-doc-html.tar.gz;

doc-clean:
	rm -rf $(top_builddir)/doc/html/*

pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = tesseract.pc

pkginclude_HEADERS = $(top_builddir)/include/tesseract/version.h
pkginclude_HEADERS += include/tesseract/apitypes.h
pkginclude_HEADERS += include/tesseract/baseapi.h
pkginclude_HEADERS += include/tesseract/capi.h
pkginclude_HEADERS += include/tesseract/genericvector.h
pkginclude_HEADERS += include/tesseract/helpers.h
pkginclude_HEADERS += include/tesseract/ltrresultiterator.h
pkginclude_HEADERS += include/tesseract/ocrclass.h
pkginclude_HEADERS += include/tesseract/osdetect.h
pkginclude_HEADERS += include/tesseract/pageiterator.h
pkginclude_HEADERS += include/tesseract/platform.h
pkginclude_HEADERS += include/tesseract/publictypes.h
pkginclude_HEADERS += include/tesseract/renderer.h
pkginclude_HEADERS += include/tesseract/resultiterator.h
pkginclude_HEADERS += include/tesseract/serialis.h
pkginclude_HEADERS += include/tesseract/strngs.h
pkginclude_HEADERS += include/tesseract/thresholder.h
pkginclude_HEADERS += include/tesseract/unichar.h

# Rules for all subdirectories.

noinst_HEADERS =
noinst_LTLIBRARIES =

AM_CPPFLAGS += -I$(top_srcdir)/include
AM_CPPFLAGS += -I$(top_builddir)/include
if DISABLED_LEGACY_ENGINE
AM_CPPFLAGS += -DDISABLED_LEGACY_ENGINE
endif
if VISIBILITY
AM_CPPFLAGS += -DTESS_EXPORTS
AM_CPPFLAGS += -fvisibility=hidden -fvisibility-inlines-hidden
endif
AM_CPPFLAGS += $(OPENCL_CPPFLAGS)

AM_CXXFLAGS = $(OPENMP_CXXFLAGS)

# Rules for src/api.

libtesseract_api_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/arch
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/ccmain
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/opencl
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/textord
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/viewer
libtesseract_api_la_CPPFLAGS += -I$(top_srcdir)/src/wordrec
libtesseract_api_la_CPPFLAGS += $(libcurl_CFLAGS)

noinst_LTLIBRARIES += libtesseract_api.la

libtesseract_api_la_SOURCES = src/api/baseapi.cpp
libtesseract_api_la_SOURCES += src/api/altorenderer.cpp
libtesseract_api_la_SOURCES += src/api/capi.cpp
libtesseract_api_la_SOURCES += src/api/hocrrenderer.cpp
libtesseract_api_la_SOURCES += src/api/lstmboxrenderer.cpp
libtesseract_api_la_SOURCES += src/api/pdfrenderer.cpp
libtesseract_api_la_SOURCES += src/api/renderer.cpp
libtesseract_api_la_SOURCES += src/api/wordstrboxrenderer.cpp

lib_LTLIBRARIES = libtesseract.la
libtesseract_la_LDFLAGS = $(LEPTONICA_LIBS) $(OPENCL_LDFLAGS) $(libarchive_LIBS)
libtesseract_la_LDFLAGS += $(libcurl_LIBS)
libtesseract_la_LDFLAGS += $(TENSORFLOW_LIBS)
libtesseract_la_SOURCES =
# Dummy C++ source to cause C++ linking.
# see http://www.gnu.org/s/hello/manual/automake/Libtool-Convenience-Libraries.html#Libtool-Convenience-Libraries
nodist_EXTRA_libtesseract_la_SOURCES = dummy.cxx
libtesseract_la_LIBADD = libtesseract_arch.la
libtesseract_la_LIBADD += libtesseract_api.la
libtesseract_la_LIBADD += libtesseract_ccstruct.la
libtesseract_la_LIBADD += libtesseract_ccutil.la
libtesseract_la_LIBADD += libtesseract_classify.la
libtesseract_la_LIBADD += libtesseract_cutil.la
libtesseract_la_LIBADD += libtesseract_dict.la
libtesseract_la_LIBADD += libtesseract_lstm.la
libtesseract_la_LIBADD += libtesseract_main.la
libtesseract_la_LIBADD += libtesseract_native.la
libtesseract_la_LIBADD += libtesseract_opencl.la
libtesseract_la_LIBADD += libtesseract_textord.la
libtesseract_la_LIBADD += libtesseract_viewer.la
libtesseract_la_LIBADD += libtesseract_wordrec.la

libtesseract_la_LDFLAGS += -version-info $(GENERIC_LIBRARY_VERSION) $(NOUNDEFINED)

# Rules for src/arch.

libtesseract_arch_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_arch_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_arch_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_arch_la_CPPFLAGS += -I$(top_srcdir)/src/viewer

noinst_HEADERS += src/arch/dotproduct.h
noinst_HEADERS += src/arch/intsimdmatrix.h
noinst_HEADERS += src/arch/simddetect.h

noinst_LTLIBRARIES += libtesseract_native.la
noinst_LTLIBRARIES += libtesseract_arch.la

libtesseract_native_la_CXXFLAGS = -O3 -ffast-math
if MARCH_NATIVE_OPT
libtesseract_native_la_CXXFLAGS += -march=native -mtune=native
endif
libtesseract_native_la_SOURCES = src/arch/dotproduct.cpp

libtesseract_arch_la_SOURCES = src/arch/intsimdmatrix.cpp src/arch/simddetect.cpp

if HAVE_AVX
libtesseract_avx_la_CXXFLAGS = -mavx
libtesseract_avx_la_SOURCES = src/arch/dotproductavx.cpp
libtesseract_la_LIBADD += libtesseract_avx.la
noinst_LTLIBRARIES += libtesseract_avx.la
endif

if HAVE_AVX2
libtesseract_avx2_la_CXXFLAGS = -mavx2
libtesseract_avx2_la_SOURCES = src/arch/intsimdmatrixavx2.cpp
libtesseract_la_LIBADD += libtesseract_avx2.la
noinst_LTLIBRARIES += libtesseract_avx2.la
endif

if HAVE_FMA
libtesseract_fma_la_CXXFLAGS = -mfma
libtesseract_fma_la_SOURCES = src/arch/dotproductfma.cpp
libtesseract_la_LIBADD += libtesseract_fma.la
noinst_LTLIBRARIES += libtesseract_fma.la
endif

if HAVE_SSE4_1
libtesseract_sse_la_CXXFLAGS = -msse4.1
libtesseract_sse_la_SOURCES = src/arch/dotproductsse.cpp src/arch/intsimdmatrixsse.cpp
libtesseract_la_LIBADD += libtesseract_sse.la
noinst_LTLIBRARIES += libtesseract_sse.la
endif

# Rules for src/ccmain.

libtesseract_main_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/arch
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/lstm
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/viewer
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/wordrec
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/textord
libtesseract_main_la_CPPFLAGS += -I$(top_srcdir)/src/opencl

noinst_HEADERS += src/ccmain/control.h
noinst_HEADERS += src/ccmain/equationdetect.h
noinst_HEADERS += src/ccmain/mutableiterator.h
noinst_HEADERS += src/ccmain/output.h
noinst_HEADERS += src/ccmain/paragraphs.h
noinst_HEADERS += src/ccmain/paragraphs_internal.h
noinst_HEADERS += src/ccmain/paramsd.h
noinst_HEADERS += src/ccmain/pgedit.h
noinst_HEADERS += src/ccmain/tesseractclass.h
noinst_HEADERS += src/ccmain/tessvars.h
noinst_HEADERS += src/ccmain/werdit.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/ccmain/docqual.h
noinst_HEADERS += src/ccmain/fixspace.h
noinst_HEADERS += src/ccmain/reject.h
endif

noinst_LTLIBRARIES += libtesseract_main.la

libtesseract_main_la_SOURCES = src/ccmain/applybox.cpp
libtesseract_main_la_SOURCES += src/ccmain/control.cpp
libtesseract_main_la_SOURCES += src/ccmain/linerec.cpp
libtesseract_main_la_SOURCES += src/ccmain/ltrresultiterator.cpp
libtesseract_main_la_SOURCES += src/ccmain/mutableiterator.cpp
libtesseract_main_la_SOURCES += src/ccmain/output.cpp
libtesseract_main_la_SOURCES += src/ccmain/pageiterator.cpp
libtesseract_main_la_SOURCES += src/ccmain/pagesegmain.cpp
libtesseract_main_la_SOURCES += src/ccmain/pagewalk.cpp
libtesseract_main_la_SOURCES += src/ccmain/paragraphs.cpp
libtesseract_main_la_SOURCES += src/ccmain/paramsd.cpp
libtesseract_main_la_SOURCES += src/ccmain/pgedit.cpp
libtesseract_main_la_SOURCES += src/ccmain/reject.cpp
libtesseract_main_la_SOURCES += src/ccmain/resultiterator.cpp
libtesseract_main_la_SOURCES += src/ccmain/tessedit.cpp
libtesseract_main_la_SOURCES += src/ccmain/tesseractclass.cpp
libtesseract_main_la_SOURCES += src/ccmain/tessvars.cpp
libtesseract_main_la_SOURCES += src/ccmain/thresholder.cpp
libtesseract_main_la_SOURCES += src/ccmain/werdit.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_main_la_SOURCES += src/ccmain/adaptions.cpp
libtesseract_main_la_SOURCES += src/ccmain/docqual.cpp
libtesseract_main_la_SOURCES += src/ccmain/equationdetect.cpp
libtesseract_main_la_SOURCES += src/ccmain/fixspace.cpp
libtesseract_main_la_SOURCES += src/ccmain/fixxht.cpp
libtesseract_main_la_SOURCES += src/ccmain/osdetect.cpp
libtesseract_main_la_SOURCES += src/ccmain/par_control.cpp
libtesseract_main_la_SOURCES += src/ccmain/recogtraining.cpp
libtesseract_main_la_SOURCES += src/ccmain/superscript.cpp
libtesseract_main_la_SOURCES += src/ccmain/tessbox.cpp
libtesseract_main_la_SOURCES += src/ccmain/tfacepp.cpp
endif

# Rules for src/ccstruct.

libtesseract_ccstruct_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/opencl
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/viewer
libtesseract_ccstruct_la_CPPFLAGS += -I$(top_srcdir)/src/wordrec

noinst_HEADERS += src/ccstruct/blamer.h
noinst_HEADERS += src/ccstruct/blobbox.h
noinst_HEADERS += src/ccstruct/blobs.h
noinst_HEADERS += src/ccstruct/blread.h
noinst_HEADERS += src/ccstruct/boxread.h
noinst_HEADERS += src/ccstruct/boxword.h
noinst_HEADERS += src/ccstruct/ccstruct.h
noinst_HEADERS += src/ccstruct/coutln.h
noinst_HEADERS += src/ccstruct/crakedge.h
noinst_HEADERS += src/ccstruct/debugpixa.h
noinst_HEADERS += src/ccstruct/detlinefit.h
noinst_HEADERS += src/ccstruct/dppoint.h
noinst_HEADERS += src/ccstruct/imagedata.h
noinst_HEADERS += src/ccstruct/linlsq.h
noinst_HEADERS += src/ccstruct/matrix.h
noinst_HEADERS += src/ccstruct/mod128.h
noinst_HEADERS += src/ccstruct/normalis.h
noinst_HEADERS += src/ccstruct/ocrblock.h
noinst_HEADERS += src/ccstruct/ocrpara.h
noinst_HEADERS += src/ccstruct/ocrrow.h
noinst_HEADERS += src/ccstruct/otsuthr.h
noinst_HEADERS += src/ccstruct/pageres.h
noinst_HEADERS += src/ccstruct/pdblock.h
noinst_HEADERS += src/ccstruct/points.h
noinst_HEADERS += src/ccstruct/polyaprx.h
noinst_HEADERS += src/ccstruct/polyblk.h
noinst_HEADERS += src/ccstruct/quadlsq.h
noinst_HEADERS += src/ccstruct/quadratc.h
noinst_HEADERS += src/ccstruct/quspline.h
noinst_HEADERS += src/ccstruct/ratngs.h
noinst_HEADERS += src/ccstruct/rect.h
noinst_HEADERS += src/ccstruct/rejctmap.h
noinst_HEADERS += src/ccstruct/seam.h
noinst_HEADERS += src/ccstruct/split.h
noinst_HEADERS += src/ccstruct/statistc.h
noinst_HEADERS += src/ccstruct/stepblob.h
noinst_HEADERS += src/ccstruct/werd.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/ccstruct/fontinfo.h
noinst_HEADERS += src/ccstruct/params_training_featdef.h
endif

noinst_LTLIBRARIES += libtesseract_ccstruct.la

libtesseract_ccstruct_la_SOURCES = src/ccstruct/blamer.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/blobbox.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/blobs.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/blread.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/boxread.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/boxword.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/ccstruct.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/coutln.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/detlinefit.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/dppoint.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/imagedata.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/linlsq.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/matrix.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/mod128.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/normalis.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/ocrblock.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/ocrpara.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/ocrrow.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/otsuthr.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/pageres.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/pdblock.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/points.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/polyaprx.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/polyblk.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/quadlsq.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/quspline.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/ratngs.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/rect.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/rejctmap.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/seam.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/split.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/statistc.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/stepblob.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/werd.cpp

if !DISABLED_LEGACY_ENGINE
libtesseract_ccstruct_la_SOURCES += src/ccstruct/fontinfo.cpp
libtesseract_ccstruct_la_SOURCES += src/ccstruct/params_training_featdef.cpp
endif

# Rules for src/ccutil

libtesseract_ccutil_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_ccutil_la_CPPFLAGS += $(libarchive_CFLAGS)
if !NO_TESSDATA_PREFIX
libtesseract_ccutil_la_CPPFLAGS += -DTESSDATA_PREFIX=@datadir@
endif

noinst_HEADERS += src/ccutil/bits16.h
noinst_HEADERS += src/ccutil/ccutil.h
noinst_HEADERS += src/ccutil/clst.h
noinst_HEADERS += src/ccutil/doubleptr.h
noinst_HEADERS += src/ccutil/elst2.h
noinst_HEADERS += src/ccutil/elst.h
noinst_HEADERS += src/ccutil/errcode.h
noinst_HEADERS += src/ccutil/fileerr.h
noinst_HEADERS += src/ccutil/genericheap.h
noinst_HEADERS += src/ccutil/globaloc.h
noinst_HEADERS += src/ccutil/host.h
noinst_HEADERS += src/ccutil/kdpair.h
noinst_HEADERS += src/ccutil/lsterr.h
noinst_HEADERS += src/ccutil/object_cache.h
noinst_HEADERS += src/ccutil/params.h
noinst_HEADERS += src/ccutil/qrsequence.h
noinst_HEADERS += src/ccutil/sorthelper.h
noinst_HEADERS += src/ccutil/scanutils.h
noinst_HEADERS += src/ccutil/tessdatamanager.h
noinst_HEADERS += src/ccutil/tprintf.h
noinst_HEADERS += src/ccutil/unicharcompress.h
noinst_HEADERS += src/ccutil/unicharmap.h
noinst_HEADERS += src/ccutil/unicharset.h
noinst_HEADERS += src/ccutil/unicity_table.h
noinst_HEADERS += src/ccutil/unicodes.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/ccutil/ambigs.h
noinst_HEADERS += src/ccutil/bitvector.h
noinst_HEADERS += src/ccutil/indexmapbidi.h
noinst_HEADERS += src/ccutil/universalambigs.h
endif

noinst_LTLIBRARIES += libtesseract_ccutil.la

libtesseract_ccutil_la_SOURCES = src/ccutil/ccutil.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/clst.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/elst2.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/elst.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/errcode.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/globaloc.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/mainblk.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/serialis.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/strngs.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/scanutils.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/tessdatamanager.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/tprintf.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/unichar.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/unicharcompress.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/unicharmap.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/unicharset.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/unicodes.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/params.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_ccutil_la_SOURCES += src/ccutil/ambigs.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/bitvector.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/indexmapbidi.cpp
libtesseract_ccutil_la_SOURCES += src/ccutil/universalambigs.cpp
endif

# Rules for src/classify.

libtesseract_classify_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/training
libtesseract_classify_la_CPPFLAGS += -I$(top_srcdir)/src/viewer

noinst_HEADERS += src/classify/classify.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/classify/adaptive.h
noinst_HEADERS += src/classify/blobclass.h
noinst_HEADERS += src/classify/cluster.h
noinst_HEADERS += src/classify/clusttool.h
noinst_HEADERS += src/classify/featdefs.h
noinst_HEADERS += src/classify/float2int.h
noinst_HEADERS += src/classify/fpoint.h
noinst_HEADERS += src/classify/intfeaturespace.h
noinst_HEADERS += src/classify/intfx.h
noinst_HEADERS += src/classify/intmatcher.h
noinst_HEADERS += src/classify/intproto.h
noinst_HEADERS += src/classify/kdtree.h
noinst_HEADERS += src/classify/mf.h
noinst_HEADERS += src/classify/mfdefs.h
noinst_HEADERS += src/classify/mfoutline.h
noinst_HEADERS += src/classify/mfx.h
noinst_HEADERS += src/classify/normfeat.h
noinst_HEADERS += src/classify/normmatch.h
noinst_HEADERS += src/classify/ocrfeatures.h
noinst_HEADERS += src/classify/outfeat.h
noinst_HEADERS += src/classify/picofeat.h
noinst_HEADERS += src/classify/protos.h
noinst_HEADERS += src/classify/shapeclassifier.h
noinst_HEADERS += src/classify/shapetable.h
noinst_HEADERS += src/classify/tessclassifier.h
noinst_HEADERS += src/classify/trainingsample.h
endif

noinst_LTLIBRARIES += libtesseract_classify.la

libtesseract_classify_la_SOURCES = src/classify/classify.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_classify_la_SOURCES += src/classify/adaptive.cpp
libtesseract_classify_la_SOURCES += src/classify/adaptmatch.cpp
libtesseract_classify_la_SOURCES += src/classify/blobclass.cpp
libtesseract_classify_la_SOURCES += src/classify/cluster.cpp
libtesseract_classify_la_SOURCES += src/classify/clusttool.cpp
libtesseract_classify_la_SOURCES += src/classify/cutoffs.cpp
libtesseract_classify_la_SOURCES += src/classify/featdefs.cpp
libtesseract_classify_la_SOURCES += src/classify/float2int.cpp
libtesseract_classify_la_SOURCES += src/classify/fpoint.cpp
libtesseract_classify_la_SOURCES += src/classify/intfeaturespace.cpp
libtesseract_classify_la_SOURCES += src/classify/intfx.cpp
libtesseract_classify_la_SOURCES += src/classify/intmatcher.cpp
libtesseract_classify_la_SOURCES += src/classify/intproto.cpp
libtesseract_classify_la_SOURCES += src/classify/kdtree.cpp
libtesseract_classify_la_SOURCES += src/classify/mf.cpp
libtesseract_classify_la_SOURCES += src/classify/mfdefs.cpp
libtesseract_classify_la_SOURCES += src/classify/mfoutline.cpp
libtesseract_classify_la_SOURCES += src/classify/mfx.cpp
libtesseract_classify_la_SOURCES += src/classify/normfeat.cpp
libtesseract_classify_la_SOURCES += src/classify/normmatch.cpp
libtesseract_classify_la_SOURCES += src/classify/ocrfeatures.cpp
libtesseract_classify_la_SOURCES += src/classify/outfeat.cpp
libtesseract_classify_la_SOURCES += src/classify/picofeat.cpp
libtesseract_classify_la_SOURCES += src/classify/protos.cpp
libtesseract_classify_la_SOURCES += src/classify/shapeclassifier.cpp
libtesseract_classify_la_SOURCES += src/classify/shapetable.cpp
libtesseract_classify_la_SOURCES += src/classify/tessclassifier.cpp
libtesseract_classify_la_SOURCES += src/classify/trainingsample.cpp
endif

# Rules for src/cutil.

libtesseract_cutil_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_cutil_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_cutil_la_CPPFLAGS += -I$(top_srcdir)/src/viewer

noinst_HEADERS += src/cutil/callcpp.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/cutil/bitvec.h
noinst_HEADERS += src/cutil/emalloc.h
noinst_HEADERS += src/cutil/oldlist.h
endif

noinst_LTLIBRARIES += libtesseract_cutil.la

libtesseract_cutil_la_SOURCES = src/cutil/callcpp.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_cutil_la_SOURCES += src/cutil/emalloc.cpp
libtesseract_cutil_la_SOURCES += src/cutil/oldlist.cpp
endif

# Rules for src/dict.

libtesseract_dict_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_dict_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_dict_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_dict_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_dict_la_CPPFLAGS += -I$(top_srcdir)/src/viewer

noinst_HEADERS += src/dict/dawg.h
noinst_HEADERS += src/dict/dawg_cache.h
noinst_HEADERS += src/dict/dict.h
noinst_HEADERS += src/dict/matchdefs.h
noinst_HEADERS += src/dict/stopper.h
noinst_HEADERS += src/dict/trie.h

noinst_LTLIBRARIES += libtesseract_dict.la

libtesseract_dict_la_SOURCES = src/dict/context.cpp
libtesseract_dict_la_SOURCES += src/dict/dawg.cpp
libtesseract_dict_la_SOURCES += src/dict/dawg_cache.cpp
libtesseract_dict_la_SOURCES += src/dict/dict.cpp
libtesseract_dict_la_SOURCES += src/dict/permdawg.cpp
libtesseract_dict_la_SOURCES += src/dict/stopper.cpp
libtesseract_dict_la_SOURCES += src/dict/trie.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_dict_la_SOURCES += src/dict/hyphen.cpp
endif

# Rules for src/lstm.

libtesseract_lstm_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/arch
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/lstm
libtesseract_lstm_la_CPPFLAGS += -I$(top_srcdir)/src/viewer
if TENSORFLOW
libtesseract_lstm_la_CPPFLAGS += -DINCLUDE_TENSORFLOW
libtesseract_lstm_la_CPPFLAGS += -I/usr/include/tensorflow
endif
if !NO_TESSDATA_PREFIX
libtesseract_lstm_la_CPPFLAGS += -DTESSDATA_PREFIX=@datadir@
endif

noinst_HEADERS += src/lstm/convolve.h
noinst_HEADERS += src/lstm/fullyconnected.h
noinst_HEADERS += src/lstm/functions.h
noinst_HEADERS += src/lstm/input.h
noinst_HEADERS += src/lstm/lstm.h
noinst_HEADERS += src/lstm/lstmrecognizer.h
noinst_HEADERS += src/lstm/maxpool.h
noinst_HEADERS += src/lstm/network.h
noinst_HEADERS += src/lstm/networkio.h
noinst_HEADERS += src/lstm/networkscratch.h
noinst_HEADERS += src/lstm/parallel.h
noinst_HEADERS += src/lstm/plumbing.h
noinst_HEADERS += src/lstm/recodebeam.h
noinst_HEADERS += src/lstm/reconfig.h
noinst_HEADERS += src/lstm/reversed.h
noinst_HEADERS += src/lstm/series.h
noinst_HEADERS += src/lstm/static_shape.h
noinst_HEADERS += src/lstm/stridemap.h
noinst_HEADERS += src/lstm/tfnetwork.h
noinst_HEADERS += src/lstm/weightmatrix.h

noinst_LTLIBRARIES += libtesseract_lstm.la

libtesseract_lstm_la_SOURCES = src/lstm/convolve.cpp
libtesseract_lstm_la_SOURCES += src/lstm/fullyconnected.cpp
libtesseract_lstm_la_SOURCES += src/lstm/functions.cpp
libtesseract_lstm_la_SOURCES += src/lstm/input.cpp
libtesseract_lstm_la_SOURCES += src/lstm/lstm.cpp
libtesseract_lstm_la_SOURCES += src/lstm/lstmrecognizer.cpp
libtesseract_lstm_la_SOURCES += src/lstm/maxpool.cpp
libtesseract_lstm_la_SOURCES += src/lstm/network.cpp
libtesseract_lstm_la_SOURCES += src/lstm/networkio.cpp
libtesseract_lstm_la_SOURCES += src/lstm/parallel.cpp
libtesseract_lstm_la_SOURCES += src/lstm/plumbing.cpp
libtesseract_lstm_la_SOURCES += src/lstm/recodebeam.cpp
libtesseract_lstm_la_SOURCES += src/lstm/reconfig.cpp
libtesseract_lstm_la_SOURCES += src/lstm/reversed.cpp
libtesseract_lstm_la_SOURCES += src/lstm/series.cpp
libtesseract_lstm_la_SOURCES += src/lstm/stridemap.cpp
libtesseract_lstm_la_SOURCES += src/lstm/tfnetwork.cpp
libtesseract_lstm_la_SOURCES += src/lstm/weightmatrix.cpp
if TENSORFLOW
libtesseract_lstm_la_SOURCES += src/lstm/tfnetwork.pb.cc
endif

# Rules for src/opencl.

libtesseract_opencl_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_opencl_la_CPPFLAGS += $(OPENCL_CFLAGS)
libtesseract_opencl_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_opencl_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_opencl_la_CPPFLAGS += -I$(top_srcdir)/src/ccmain

noinst_HEADERS += src/opencl/openclwrapper.h
noinst_HEADERS += src/opencl/oclkernels.h

noinst_LTLIBRARIES += libtesseract_opencl.la

libtesseract_opencl_la_SOURCES = src/opencl/openclwrapper.cpp

# Rules for src/textord.

libtesseract_textord_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/include
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/viewer
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/ccmain
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/wordrec
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/api
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_textord_la_CPPFLAGS += -I$(top_srcdir)/src/opencl

noinst_HEADERS += src/textord/alignedblob.h
noinst_HEADERS += src/textord/baselinedetect.h
noinst_HEADERS += src/textord/bbgrid.h
noinst_HEADERS += src/textord/blkocc.h
noinst_HEADERS += src/textord/blobgrid.h
noinst_HEADERS += src/textord/ccnontextdetect.h
noinst_HEADERS += src/textord/cjkpitch.h
noinst_HEADERS += src/textord/colfind.h
noinst_HEADERS += src/textord/colpartition.h
noinst_HEADERS += src/textord/colpartitionset.h
noinst_HEADERS += src/textord/colpartitiongrid.h
noinst_HEADERS += src/textord/devanagari_processing.h
noinst_HEADERS += src/textord/drawtord.h
noinst_HEADERS += src/textord/edgblob.h
noinst_HEADERS += src/textord/edgloop.h
noinst_HEADERS += src/textord/fpchop.h
noinst_HEADERS += src/textord/gap_map.h
noinst_HEADERS += src/textord/imagefind.h
noinst_HEADERS += src/textord/linefind.h
noinst_HEADERS += src/textord/makerow.h
noinst_HEADERS += src/textord/oldbasel.h
noinst_HEADERS += src/textord/pithsync.h
noinst_HEADERS += src/textord/pitsync1.h
noinst_HEADERS += src/textord/scanedg.h
noinst_HEADERS += src/textord/sortflts.h
noinst_HEADERS += src/textord/strokewidth.h
noinst_HEADERS += src/textord/tabfind.h
noinst_HEADERS += src/textord/tablefind.h
noinst_HEADERS += src/textord/tabvector.h
noinst_HEADERS += src/textord/tablerecog.h
noinst_HEADERS += src/textord/textlineprojection.h
noinst_HEADERS += src/textord/textord.h
noinst_HEADERS += src/textord/topitch.h
noinst_HEADERS += src/textord/tordmain.h
noinst_HEADERS += src/textord/tovars.h
noinst_HEADERS += src/textord/underlin.h
noinst_HEADERS += src/textord/wordseg.h
noinst_HEADERS += src/textord/workingpartset.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/textord/equationdetectbase.h
endif

noinst_LTLIBRARIES += libtesseract_textord.la

libtesseract_textord_la_SOURCES = src/textord/alignedblob.cpp
libtesseract_textord_la_SOURCES += src/textord/baselinedetect.cpp
libtesseract_textord_la_SOURCES += src/textord/bbgrid.cpp
libtesseract_textord_la_SOURCES += src/textord/blkocc.cpp
libtesseract_textord_la_SOURCES += src/textord/blobgrid.cpp
libtesseract_textord_la_SOURCES += src/textord/ccnontextdetect.cpp
libtesseract_textord_la_SOURCES += src/textord/cjkpitch.cpp
libtesseract_textord_la_SOURCES += src/textord/colfind.cpp
libtesseract_textord_la_SOURCES += src/textord/colpartition.cpp
libtesseract_textord_la_SOURCES += src/textord/colpartitionset.cpp
libtesseract_textord_la_SOURCES += src/textord/colpartitiongrid.cpp
libtesseract_textord_la_SOURCES += src/textord/devanagari_processing.cpp
libtesseract_textord_la_SOURCES += src/textord/drawtord.cpp
libtesseract_textord_la_SOURCES += src/textord/edgblob.cpp
libtesseract_textord_la_SOURCES += src/textord/edgloop.cpp
libtesseract_textord_la_SOURCES += src/textord/fpchop.cpp
libtesseract_textord_la_SOURCES += src/textord/gap_map.cpp
libtesseract_textord_la_SOURCES += src/textord/imagefind.cpp
libtesseract_textord_la_SOURCES += src/textord/linefind.cpp
libtesseract_textord_la_SOURCES += src/textord/makerow.cpp
libtesseract_textord_la_SOURCES += src/textord/oldbasel.cpp
libtesseract_textord_la_SOURCES += src/textord/pithsync.cpp
libtesseract_textord_la_SOURCES += src/textord/pitsync1.cpp
libtesseract_textord_la_SOURCES += src/textord/scanedg.cpp
libtesseract_textord_la_SOURCES += src/textord/sortflts.cpp
libtesseract_textord_la_SOURCES += src/textord/strokewidth.cpp
libtesseract_textord_la_SOURCES += src/textord/tabfind.cpp
libtesseract_textord_la_SOURCES += src/textord/tablefind.cpp
libtesseract_textord_la_SOURCES += src/textord/tabvector.cpp
libtesseract_textord_la_SOURCES += src/textord/tablerecog.cpp
libtesseract_textord_la_SOURCES += src/textord/textlineprojection.cpp
libtesseract_textord_la_SOURCES += src/textord/textord.cpp
libtesseract_textord_la_SOURCES += src/textord/topitch.cpp
libtesseract_textord_la_SOURCES += src/textord/tordmain.cpp
libtesseract_textord_la_SOURCES += src/textord/tospace.cpp
libtesseract_textord_la_SOURCES += src/textord/tovars.cpp
libtesseract_textord_la_SOURCES += src/textord/underlin.cpp
libtesseract_textord_la_SOURCES += src/textord/wordseg.cpp
libtesseract_textord_la_SOURCES += src/textord/workingpartset.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_textord_la_SOURCES += src/textord/equationdetectbase.cpp
endif

# Rules for src/viewer.

libtesseract_viewer_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_viewer_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil

noinst_HEADERS += src/viewer/scrollview.h
noinst_HEADERS += src/viewer/svmnode.h
noinst_HEADERS += src/viewer/svutil.h

noinst_LTLIBRARIES += libtesseract_viewer.la

libtesseract_viewer_la_SOURCES = src/viewer/scrollview.cpp
libtesseract_viewer_la_SOURCES += src/viewer/svmnode.cpp
libtesseract_viewer_la_SOURCES += src/viewer/svutil.cpp

# TODO: Add rule to generate svpaint from svpaint.cpp.

# Rules for src/wordrec.

libtesseract_wordrec_la_CPPFLAGS = $(AM_CPPFLAGS)
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/include
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/ccutil
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/cutil
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/classify
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/dict
libtesseract_wordrec_la_CPPFLAGS += -I$(top_srcdir)/src/viewer

noinst_HEADERS += src/wordrec/wordrec.h
if !DISABLED_LEGACY_ENGINE
noinst_HEADERS += src/wordrec/associate.h
noinst_HEADERS += src/wordrec/chop.h
noinst_HEADERS += src/wordrec/drawfx.h
noinst_HEADERS += src/wordrec/findseam.h
noinst_HEADERS += src/wordrec/language_model.h
noinst_HEADERS += src/wordrec/lm_consistency.h
noinst_HEADERS += src/wordrec/lm_pain_points.h
noinst_HEADERS += src/wordrec/lm_state.h
noinst_HEADERS += src/wordrec/measure.h
noinst_HEADERS += src/wordrec/outlines.h
noinst_HEADERS += src/wordrec/params_model.h
noinst_HEADERS += src/wordrec/plotedges.h
noinst_HEADERS += src/wordrec/render.h
endif

noinst_LTLIBRARIES += libtesseract_wordrec.la

libtesseract_wordrec_la_SOURCES = src/wordrec/tface.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/wordrec.cpp
if !DISABLED_LEGACY_ENGINE
libtesseract_wordrec_la_SOURCES += src/wordrec/associate.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/chop.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/chopper.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/drawfx.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/findseam.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/gradechop.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/language_model.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/lm_consistency.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/lm_pain_points.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/lm_state.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/outlines.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/params_model.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/pieces.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/plotedges.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/render.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/segsearch.cpp
libtesseract_wordrec_la_SOURCES += src/wordrec/wordclass.cpp
endif

# Rules for tesseract executable.

bin_PROGRAMS = tesseract
tesseract_SOURCES = src/api/tesseractmain.cpp
tesseract_CPPFLAGS =
tesseract_CPPFLAGS += -I$(top_srcdir)/src/arch
tesseract_CPPFLAGS += -I$(top_srcdir)/src/ccstruct
tesseract_CPPFLAGS += -I$(top_srcdir)/src/ccutil
tesseract_CPPFLAGS += -I$(top_srcdir)/src/dict
tesseract_CPPFLAGS += -I$(top_srcdir)/src/viewer
tesseract_CPPFLAGS += $(AM_CPPFLAGS)
if VISIBILITY
tesseract_CPPFLAGS += -DTESS_IMPORTS
endif

tesseract_LDFLAGS = $(OPENCL_LDFLAGS)
tesseract_LDFLAGS += $(OPENMP_CXXFLAGS)

tesseract_LDADD = libtesseract.la
tesseract_LDADD += $(LEPTONICA_LIBS)
tesseract_LDADD += $(TENSORFLOW_LIBS)
tesseract_LDADD += $(libarchive_LIBS)
tesseract_LDADD += $(libcurl_LIBS)

if T_WIN
tesseract_LDADD += -ltiff
tesseract_LDADD += -lws2_32
libtesseract_la_LDFLAGS += -no-undefined -Wl,--as-needed -lws2_32
endif
if ADD_RT
tesseract_LDADD += -lrt
endif

# fuzzer-api is used for fuzzing tests.
# They are run by OSS-Fuzz https://oss-fuzz.com/, but can also be run locally.
# Note: -fsanitize=fuzzer currently requires the clang++ compiler.

# LIB_FUZZING_ENGINE can be overridden by the caller.
# This is used by OSS-Fuzz.
LIB_FUZZING_ENGINE ?= -fsanitize=fuzzer

fuzzer-api: all
fuzzer-api: $(top_srcdir)/unittest/fuzzers/fuzzer-api.cpp
	$(CXX) $(CXXFLAGS) -g $(LIB_FUZZING_ENGINE) \
          -I $(top_srcdir)/include \
          -I $(top_srcdir)/src/ccmain \
          -I $(top_srcdir)/src/ccstruct \
          -I $(top_srcdir)/src/ccutil \
          $< \
          src/api/.libs/libtesseract.a \
          $(LEPTONICA_LIBS) \
          $(TENSORFLOW_LIBS) \
          $(libarchive_LIBS) \
          -o $@
