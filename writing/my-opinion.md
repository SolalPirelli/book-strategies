# Tools

While this entire guide is my opinion, this page is the part where I go so far outside the conventional wisdom, at least from CS, that I have to clearly disclaim here that this is just the opinion of a random PhD student who thinks he has decent ideas on how to better write papers. I focused on structure, text, and workflow in the rest of this guide, but here I'll talk about how tools fit into the picture.

**There are two broad categories of word processors: markup and WYSIWYG**. Markup processors have an explicit "compilation" step that takes in text formatted with some kind of markup and outputs the rendered document. For instance, LaTeX-based tools can convert a text file in LaTeX format to a PDF. WYSIWYG processors, as their "What You See Is What You Get" name implies, do not have an explicit input or compilation step but instead allow users to directly edit a document. WYSIWYG processors hide their input format, providing a graphical interface instead. WYSIWYG processors still have a "compilation" step to produce an output, but any graphical changes from the editing interface to the final document can be considered a bug. Some word processors are hybrids, allowing users to view the input and the output at the same time, sometimes even in the same view for simple enough markup such as Markdown. Some word processors have additional views, such as a "draft" mode that ignores the layout constraints such as page height.

**Markup processors are suited to free-flowing documents, not scientific papers**. The goal of a markup processor is to convert a piece of text annotated with markup into a "good-looking" document, defined according to some subjective taste. This is a useful goal for documents whose constraints are unknown at the time of writing, such as the dimensions of the screen used to view a Web page. However, scientific papers are not in this category. The constraints on scientific papers are known ahead of time. A markup processor may return the "best-looking" version of a given input, but this is unlikely to be the best version of the paper, because modifying the text itself is an option. For instance, LaTeX sometimes outputs warnings about overfull horizontal or vertical boxes, indicating that it could not find a way to make the document "good-looking", which can be fixed by modifying the text. Changing the text also allows one to avoid "widows", beginning of paragraphs cut off at the end of a page, "orphans", ends of paragraphs cut off at the start of a page, and words that appear on a line of their own at the end of a paragraph.

**WYSIWYG processors are the way to go when constraints are fixed ahead of time**, as in scientific papers. They allow writers to see exactly what is going on and to fix any visual issues without having to guess what the output of a piece of markup will be. This is important because humans are empirically bad at predicting how a change in text will affect the output. Thus, reducing the edit-compile-view cycle is crucial, and WYSIWYG processors provide minimal latency between editing and viewing. Markup processors force writers to guess how to fix visual issues, whereas WYSIWYG processors allow writers to see exactly what they are doing. Whether a markup processor could produce a "better-looking" result for a given input than a WYSIWYG processor is irrelevant, because that is not the task at hand.
