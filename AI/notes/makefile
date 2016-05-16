notes.pdf: background.md search.md knowlege.md
	pandoc -o $@ $^

notes.html: background.md search.md knowlege.md
	pandoc -s -S --toc -o $@ -c normalize.css $^

.PHONY: clean

clean:
	rm *.html *.pdf
