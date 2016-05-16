notes.html: background.md search.md knowlege.md
	pandoc -s -S --toc -o $@ -c normalize.css $^

notes.pdf: background.md search.md knowlege.md
	pandoc -o $@ $^

.PHONY: clean

clean:
	rm *.html *.pdf
