ECHO=

TALKS= \
	talk-01 \
	talk-02-tei \
	talk-03-basic \
	talk-04-header \
	talk-05-univers \
	talk-06-ednum \
	talk-07-cartes \
	talk-08-names \
	talk-09-ling \
	talk-10-outils \
	talk-11-xslt 

EXERCISES= \
	ex-1-oxygen \
	ex-2-basic \
	ex-3-header \
	ex-4-roma \
	ex-5-cartes \
	ex-6-names \
	ex-7-outils \
	ex-8-xslt

default: pdfslides

exercises:
	for i in $(EXERCISES) ; do \
		teitopdf $$i.xml; \
	done

slides: pdfslides 

pdfslides:  
	for i in $(TALKS) ; do \
		teitoslides --profile=tei $$i.xml; \
		mv $$i.xml.slides $$i.pdf; \
	done


clean:
	-rm -f *.aux
	-rm -f *.log
	-rm -f *.nav
	-rm -f *.out
	-rm -f *.snm
	-rm -f *.tex
	-rm -f *.toc
	-rm -f *.vrb
	-rm -f *.eps *.dvi
	-rm -f *.xhtml
	-rm -f *~
	-rm booklet.pdf

all:
	xmllint --xinclude talks.xml > diapos.xml
	teitoepub --profile=tei --odd diapos.xml
	teitodocx --profile=tei diapos.xml
	rm -f travaux.zip
	zip -r travaux `find Travaux -type f | grep -v .svn`

handout: 
	cat booklet-top.txt > booklet-xi.xml
	echo '<?tex \\newpage ?>' >> booklet-xi.xml
	for i in $(EXERCISES); do \
		echo "<div>" >> booklet-xi.xml; \
		grep "<title>" $$i.xml | head -1 | sed 's/title>/head>/g' >> booklet-xi.xml; \
		echo "<xi:include href=\"$$i.xml#xmlns(t=http://www.tei-c.org/ns/1.0)xpointer(/t:TEI/t:text/t:body/t:*)\"/>" >> booklet-xi.xml; \
		echo "</div>" >> booklet-xi.xml; \
		echo '<?tex \\newpage ?>'>> booklet-xi.xml ; \
	done
	echo '<?tex \\newpage ?>'>> booklet-xi.xml
	cat booklet-end.txt >> booklet-xi.xml
	xmllint --xinclude booklet-xi.xml > booklet.xml
	saxon booklet.xml booklet.xsl > booklet.tex
	perl -p -i -e 's+.color{red}++g' booklet.tex
	echo perl -p -i -e 's+\\maketitle++g' booklet.tex
	pdflatex booklet
	pdflatex booklet
	pdflatex booklet
	mv booklet.pdf exercices.pdf

validate:
	-for i in $(TALKS) ; do onvdl ../teitalks.nvdl `basename $$i .xml`.xml \
	 | grep -v ': error: unfinished element$$' \
	 | grep -v ': error: unfinished element .* required to finish the element$$'; done

demo:
	xmllint --xinclude demo.odd > x.odd; roma2 --docpdf --nodtd --dochtml --isoschematron x.odd .
	roma --nodtd --noxsd --schema=demosimple x.odd .
	saxon demo.isosch http://www.tei-c.org/release/xml/tei/odd/Utilities/iso_schematron_message_xslt2.xsl > demo.isosch.xsl

test:
	jing demo.rng demo.xml
	rnv demo.rnc demo.xml
	saxon demo.xml demo.isosch.xsl 
	jing demo.xsd demo.xml


