# Programs
C++     	= g++
LD	= ld
RM 	= rm
ECHO	= /bin/echo
CAT	= cat
PRINTF	= printf
AR	= ar
LINK	= ln
SED	= sed
DOXYGEN = doxygen
######################################
# Project Name (generate executable with this name)
TARGET = cs296_exe_19
INSTALL_PATH = $(HOME)/Desktop/inst
# Project Paths
PROJECT_ROOT=$(HOME)/Desktop/296project/RGM/cs296_base_code
EXTERNAL_ROOT=$(PROJECT_ROOT)/external
SRCDIR = $(PROJECT_ROOT)/src
OBJDIR = $(PROJECT_ROOT)/myobjs
BINDIR = $(PROJECT_ROOT)/mybins
LIBDIR	= $(PROJECT_ROOT)/mylibs
DOCDIR = $(PROJECT_ROOT)/doc
PLOTDIR = $(PROJECT_ROOT)/plots
SCRIPTSDIR = $(PROJECT_ROOT)/scripts
# Library Paths
BOX2D_ROOT=$(EXTERNAL_ROOT)
GLUI_ROOT=/usr
GL_ROOT=/usr

#Libraries
LIBS = -lBox2D -lglui -lglut -lGLU -lGL

# Compiler and Linker flags
CPPFLAGS =-g -O3 -Wall 
CPPFLAGS+=-I $(BOX2D_ROOT)/include -I $(GLUI_ROOT)/include
LDFLAGS+=-L $(BOX2D_ROOT)/lib -L $(GLUI_ROOT)/lib

######################################

NO_COLOR=\e[0m
OK_COLOR=\e[1;32m
ERR_COLOR=\e[1;31m
WARN_COLOR=\e[1;33m
MESG_COLOR=\e[1;34m
FILE_COLOR=\e[1;37m

OK_STRING="[OK]"
ERR_STRING="[ERRORS]"
WARN_STRING="[WARNINGS]"
OK_FMT="${OK_COLOR}%30s\n${NO_COLOR}"
ERR_FMT="${ERR_COLOR}%30s\n${NO_COLOR}"
WARN_FMT="${WARN_COLOR}%30s\n${NO_COLOR}"
######################################

SRCS := $(wildcard $(SRCDIR)/*.cpp)
INCS := $(wildcard $(SRCDIR)/*.hpp)
OBJS := $(SRCS:$(SRCDIR)/%.cpp=$(OBJDIR)/%.o)
SPLOBJS := $(OBJDIR)/callbacks.o $(OBJDIR)/cs296_base.o $(OBJDIR)/dominos.o $(OBJDIR)/render.o 
#boolean value STATIC_LIB
STATIC_LIB	= "FALSE"

.PHONY: all setup shared static exe exelib clean distclean doc report dist install

all: setup exe exelib

setup:
	@$(ECHO) "Setting up compilation..."
	@mkdir -p $(OBJDIR)
	@mkdir -p $(LIBDIR)
	@mkdir -p $(BINDIR)

exe: setup $(OBJS) $(BINDIR)
	$(PRINTF) "Building executable: $(FILE_COLOR) %16s$(NO_COLOR)" "$(notdir $@)"
	$(C++) -o $(BINDIR)/$(TARGET) $(LDFLAGS) $(OBJS) $(LIBS) 2> temp.log || touch temp.err
	if test -e temp.err; \
	then $(PRINTF) $(ERR_FMT) $(ERR_STRING) && $(CAT) temp.log; \
	elif test -s temp.log; \
	then $(PRINTF) $(WARN_FMT) $(WARN_STRING) && $(CAT) temp.log; \
	else $(PRINTF) $(OK_FMT) $(OK_STRING); \
	fi;
	$(RM) -f temp.log temp.err

-include $(OBJS:.o=.d)

$(OBJS): $(OBJDIR)/%.o : $(SRCDIR)/%.cpp
	$(PRINTF) "$(MESG_COLOR)Compiling: $(NO_COLOR) $(FILE_COLOR) %25s$(NO_COLOR)" "$(notdir $<)"
	$(C++) $(CPPFLAGS) -c $< -o $@ -MD 2> temp.log || touch temp.err
	if test -e temp.err; \
	then $(PRINTF) $(ERR_FMT) $(ERR_STRING) && $(CAT) temp.log; \
	elif test -s temp.log; \
	then $(PRINTF) $(WARN_FMT) $(WARN_STRING) && $(CAT) temp.log; \
	else printf "${OK_COLOR}%30s\n${NO_COLOR}" "[OK]"; \
	fi;
	$(RM) -f temp.log temp.err
	
static:	$(OBJS)
ifeq ($(STATIC_LIB), "TRUE")
	@$(AR) -cvq $(LIBDIR)/libCS296.a $(SPLOBJS)   
endif

shared: $(SPLOBJS)

ifeq ($(STATIC_LIB), "FALSE")
	@$(C++) -shared -Wl,-soname,libCS296.so.1 -o $(LIBDIR)/libCS296.so.1.0 $(SPLOBJS) \
		&& $(LINK) -sf $(LIBDIR)/libCS296.so.1.0 $(LIBDIR)/libCS296.so.1 \
		&& $(LINK) -sf $(LIBDIR)/libCS296.so.1.0 $(LIBDIR)/libCS296.so
endif

exelib: setup shared static
	@$(PRINTF) "Building executable of exelib: "
	@$(C++) -Wall $(OBJDIR)/main.o -L$(LIBDIR) -lCS296 $(LDFLAGS) $(LIBS) \
		-o $(BINDIR)/cs296_exelib_19 2> temp.log || touch temp.err
	
	@$(RM) -f temp.log temp.err
	
DOC_TARGET= g19_proj_report

report: 
	cd doc/ ; pdflatex $(DOC_TARGET) ; bibtex $(DOC_TARGET) ; pdflatex $(DOC_TARGET) ; pdflatex $(DOC_TARGET) 
dist: clean
	cd ../ ; tar -zcvf g19_proj_dist.tar.gz cs296_base_code/
doc:
	@$(ECHO) -n "Generating Doxygen Documentation ...  "
	@$(RM) -rf doc/html
	@$(DOXYGEN) $(DOCDIR)/Doxyfile 2 > /dev/null
	@$(ECHO) "Done"
install:
	python3 scripts/installProjAll.py
	cp -r mybins/ $(INSTALL_PATH)
	cp -r doc/ $(INSTALL_PATH)
	cp -r scripts/ $(INSTALL_PATH)

clean:
	@$(ECHO) -n "Cleaning up..."
	@$(RM) -rf $(OBJDIR) *~ $(DEPS) $(SRCDIR)/*~ $(BINDIR) $(LIBDIR) $(PNGDIR)
	@rm -rf *.aux *.blg *.bbl *.log *.dvi
	@rm -rf $(DOCDIR)/*.aux $(DOCDIR)/*.blg $(DOCDIR)/*.bbl $(DOCDIR)/*.log $(DOCDIR)/*.dvi $(DOCDIR)/*.html $(DOCDIR)/*.pdf
	@$(ECHO) "Done"


