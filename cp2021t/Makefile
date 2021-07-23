### Name of the zip files?
zipname ?= cp2021t.zip
deliveryzipname ?= cp2021t-entrega.zip


## Flag-independant environment variables

### Build a list of all project source files
ALL_FILES = cp2021t_media src latex Makefile README.md


## Targets

### Build Targets

#### Executable targets
pdf: cp2021t.pdf

prepare: setup
	/bin/cp -rf cp2021t_media obj
	/bin/cp -rf latex/* obj
	/bin/cp -rf src/* obj
	cd obj && \
	[ ! -e cp2021t.pdf ] && \
	lhs2TeX cp2021t.lhs > cp2021t.tex && \
	pdflatex cp2021t; true

cp2021t.pdf: prepare
	cd obj && \
	lhs2TeX cp2021t.lhs > cp2021t.tex && \
	bibtex cp2021t.aux && \
	makeindex cp2021t.idx && \
	lhs2TeX cp2021t.lhs > cp2021t.tex && \
	pdflatex cp2021t && \
	cp cp2021t.pdf .. && \
	echo Success || echo FAILED TO COMPILE PDF!!!


### Setup & Cleanup targets

#### Sets up the system enviorenment and required packages for the project
setup: setup-linux-pre setup-common
	touch setup

setup-linux-pre:
	apt install -y texlive-base texlive-lang-portuguese texlive-pictures texlive-latex-extra
	apt install -y haskell-platform lhs2tex
	touch setup-linux-pre

setup-common:
	cabal install matrix lhs2tex QuickCheck gloss
	mkdir -p latex
	mkdir -p obj
	mkdir -p out
	mkdir -p src
	mv *.hs src 2>/dev/null; true
	mv *.lhs src 2>/dev/null; true
	mv cp2021t.bib latex 2>/dev/null; true
	mv cp2021t.sty latex 2>/dev/null; true
	find . -maxdepth 0 ! -name 'README.md' ! -name 'Makefile' ! -name '.gitignore' -type f -exec rm -f {} +
	touch setup-common

#### Cleans up all binaries, objects, documentation, created zip files, and marks the project as not setup nor installed
clean-objs:
	rm -rf obj/*

clean: clean-objs
#	Apaga os diretórios, conteúdo e ficheiros desnecessários
	rm -rf setup $(zipname) $(deliveryzipname)


### Other targets

setup-watch:
	apt install -y inotify-tools
	touch setup-watch

watch: setup-watch
	while inotifywait -e close_write src/cp2021t.lhs; do make pdf; done

ghci:
	ghci -isrc src/cp2021t.lhs

$(zipname):
	zip -r -9 $(zipname) $(ALL_FILES)

zip: $(zipname)

out/$(deliveryzipname): pdf
	cd obj && \
	zip -r -9 $(deliveryzipname) cp2021t.lhs cp2021t.pdf && \
	cp $(deliveryzipname) ..

zip-delivery: out/$(deliveryzipname)

#### Phony targets are always rebuilt
.PHONY: zip zip-delivery
