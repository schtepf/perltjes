default:
	@echo "make install  ...   install into ~/bin"
	@echo "make clean    ...   delete temporary files (from testing)"

clean:
	Hoover -v
	-rm -f test.bib

install:
	install -m 755 bibextract bibformat "`echo ~/bin`"
