# BOOKGEN #
# <https://github.com/marrus-sh/BookGen/>
#
# I canʼt imagine why you would want to TOUCH this code, but just in case :
#
# This program is free software : you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or ( at your option ) any later version.
#
# This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with this program. If not, see <https://www.gnu.org/licenses/>.
#
# See README.md for usage.

SHELL := /bin/sh
srcdir := $(patsubst %/GNUmakefile,%,$(lastword $(MAKEFILE_LIST)))
override octothorpe := \#
override empty :=
override space := $(empty) $(empty)
override PYTHONPATH := $(srcdir)
export PYTHONPATH

#  DEFAULT VALUES FOR OVERRIDES  #

CHAPTERPREFIX := Chapters/

APPENDIXPREFIX := $(CHAPTERPREFIX)A
BIBLIOGRAPHY :=
BIBREQUIRE := [notes,annotation]{biblatex-chicago}
BUILD := Build
DRAFTS :=
FILEPREFIX :=
FULLTEXT := text
HTML := HTML
INDEX := index
LATEX := LaTeX
MARKDOWN := Markdown
PDF := PDF
PNG := PNG
STYLE :=
STYLES := Styles
YAML := info.yml
ZIP := Zip

# PROGRAMS #
BIBER := biber
CITEPROC := pandoc-citeproc
CONVERT := magick
GS := gs
INFOZIP := zip
PANDOC := pandoc
PDFTOTEXT := pdftotext
TEX := xelatex

# BASIC RULES #

all everything: md xhtml tex pdf png;
htm html html5 xht xhtml5 xml $(HTML): xhtml;
latex $(LATEX): tex;
markdown $(MARKDOWN): md;
$(PDF): pdf;
$(PNG): png
dist $(ZIP): zip;
clean: ; rm -rf $(BUILD)
unclean: ; rm -rf $(if $(DRAFTS),$(FILEPREFIX)$(MARKDOWN),) $(FILEPREFIX)$(LATEX) $(FILEPREFIX)$(HTML) $(FILEPREFIX)$(PDF) $(FILEPREFIX)$(PNG) $(FILEPREFIX)$(ZIP)
clobber distclean gone: clean unclean;
zip: ;
.PHONY: all everything htm html html5 xht xhtml5 xml $(HTML) latex $(LATEX) markdown $(MARKDOWN) $(PDF) $(PNG) dist $(ZIP) clean unclean clobber distclean gone zip;
.SUFFIXES: ;
.SECONDEXPANSION: ;

# SOURCE FILES AND NAMES #

numbers = $(sort $(foreach filename,$(patsubst $(1)%$(2),%,$(3)),$(firstword $(subst -, ,$(filename)))))
allnumbers = $(call numbers,$(1),$(2),$(wildcard $(1)[0-9][0-9]$(2)) $(wildcard $(1)[0-9][0-9]-*$(2)))
numbered = $(foreach number,$(call numbers,$(1),$(2),$(3)),$(firstword $(wildcard $(1)$(number)$(2)) $(wildcard $(1)$(number)-*$(2))))
allnumbered = $(foreach number,$(call allnumbers,$(1),$(2)),$(firstword $(wildcard $(1)$(number)$(2)) $(wildcard $(1)$(number)-*$(2))))

chapternames = $(patsubst $(FILEPREFIX)$(MARKDOWN)/$(CHAPTERPREFIX)%.md,%,$(1))
appendixnames = $(patsubst $(FILEPREFIX)$(MARKDOWN)/$(APPENDIXPREFIX)%.md,%,$(1))
standalonenames = $(patsubst $(FILEPREFIX)$(MARKDOWN)/%.md,%,$(1))

srcs = $(patsubst %,$(FILEPREFIX)$(MARKDOWN)/%.md,$(1))

ifdef DRAFTS
chaptersrcs := $(sort $(patsubst $(DRAFTS)/%/,$(FILEPREFIX)$(MARKDOWN)/%.md,$(dir $(call allnumbered,$(DRAFTS)/$(CHAPTERPREFIX),/*.md))))
appendixsrcs := $(sort $(patsubst $(DRAFTS)/%/,$(FILEPREFIX)$(MARKDOWN)/%.md,$(dir $(call allnumbered,$(DRAFTS)/$(APPENDIXPREFIX),/*.md))))
standalonesrcs := $(sort $(filter-out $(chaptersrcs) $(appendixsrcs),$(patsubst $(DRAFTS)/%/,$(FILEPREFIX)$(MARKDOWN)/%.md,$(dir $(wildcard $(DRAFTS)/*/*.md)))))
else
chaptersrcs := $(call allnumbered,$(FILEPREFIX)$(MARKDOWN)/$(CHAPTERPREFIX),.md)
appendixsrcs := $(call allnumbered,$(FILEPREFIX)$(MARKDOWN)/$(APPENDIXPREFIX),.md)
standalonesrcs := $(sort $(filter-out $(chaptersrcs) $(appendixsrcs),$(wildcard $(FILEPREFIX)$(MARKDOWN)/*.md)))
endif
allsrcs := $(standalonesrcs) $(chaptersrcs) $(appendixsrcs)

allstandalonenames := $(call standalonenames,$(standalonesrcs))
allchapternames := $(call chapternames,$(chaptersrcs))
allappendixnames := $(call appendixnames,$(appendixsrcs))
allnames := $(allstandalonenames) $(addprefix $(CHAPTERPREFIX),$(allchapternames)) $(addprefix $(APPENDIXPREFIX),$(allappendixnames))

types = $(foreach src,$(1),$(if $(findstring $(src),$(appendixsrcs)),appendix,$(if $(findstring $(src),$(chaptersrcs)),chapter,standalone)))
localizedtypes = $(foreach src,$(1),$(LOCALIZATION_$(if $(findstring $(src),$(appendixsrcs)),APPENDIX,$(if $(findstring $(src),$(chaptersrcs)),CHAPTER,STANDALONE))))

chapternumbers = $(call numbers,$(FILEPREFIX)$(MARKDOWN)/$(CHAPTERPREFIX),.md,$(1))
appendixnumbers = $(call numbers,$(FILEPREFIX)$(MARKDOWN)/$(APPENDIXPREFIX),.md,$(1))
autonumbers = $(call $(call types,$(1))numbers,$(1))

# FOLDERS AND LINKS #

makefolders = remaining="$(or $(1),$@)"; while [[ "$$remaining" == */* ]]; do [[ -d "$${remaining%%/*}" ]] || mkdir "$${remaining%%/*}"; cd "$${remaining%%/*}"; remaining="$${remaining$(octothorpe)*/}"; done

returntoroot = $(subst $(space),,$(foreach dir,$(strip $(subst /, ,$(dir /$(1)))),../))

relativepath = $(if $(2),./$(call returntoroot,$(1))$(2))

define link
$(makefolders)
ln -fs "$(realpath $(or $(1),$<))" $(or $(2),$@)
endef

# ZIP CREATION #

define makezip
$(call makefolders,$@)
rm -f $@
$(if $(or $(1),$^),cd $(FILEPREFIX)$(firstword $(subst /, ,$(patsubst $(FILEPREFIX)%,%,$(firstword $(or $(1),$^))))); $(INFOZIP) -MM -9DX$(if $(VERBOSE),v,q)r ../$(patsubst $(FILEPREFIX)%,%,$(basename $@)) $(patsubst $(FILEPREFIX)$(firstword $(subst /, ,$(patsubst $(FILEPREFIX)%,%,$(firstword $(or $(1),$^)))))/%,%,$(or $(1),$^)),)
$(if $(or $(1),$^),@echo "Zip generated at $@",)
endef

# STYLES AND FILES #

stylesrcs = $(sort $(wildcard $(STYLES)/*.$(1)))
ifdef STYLE
stylenames = $(filter $(STYLE),$(patsubst $(STYLES)/%.$(1),%,$(call stylesrcs,$(1))))
else # If no styles are specified, use all of them.
stylenames = $(patsubst $(STYLES)/%.$(1),%,$(call stylesrcs,$(1)))
endif

styles = $(foreach file,$(1),$(word 2,$(subst /, ,$(patsubst $(FILEPREFIX)%,%,$(1)))))

# For the remaining rules in this section:
# $(1): The folder name
# $(2): The style name OR file extension
# $(3): The resulting file extension
# $(4): File names

files = $(if $(findstring /,$(3)),$(patsubst %,$(FILEPREFIX)$(1)/$(2)/%/$(notdir $(3)),$(4)),$(patsubst %,$(FILEPREFIX)$(1)/$(2)/%.$(3),$(4)))
chapters = $(call files,$(1),$(2),$(3),$(addprefix $(CHAPTERPREFIX),$(allchapternames)))
appendixes = $(call files,$(1),$(2),$(3),$(addprefix $(APPENDIXPREFIX),$(allappendixnames)))
standalones = $(call files,$(1),$(2),$(3),$(allstandalonenames))
everything = $(call files,$(1),$(2),$(3),$(4) $(allnames))

filenames = $(if $(findstring /,$(3)),$(patsubst $(FILEPREFIX)$(1)/$(2)/%/$(notdir $(3)),%,$(4)),$(patsubst $(FILEPREFIX)$(1)/$(2)/%.$(3),%,$(4)))

allfiles = $(foreach style,$(call stylenames,$(2)),$(call files,$(1),$(style),$(3),$(4)))
allchapters = $(call allfiles,$(1),$(2),$(3),$(addprefix $(CHAPTERPREFIX),$(allchapternames)))
allappendixes = $(call allfiles,$(1),$(2),$(3),$(addprefix $(APPENDIXPREFIX),$(allappendixnames)))
allstandalones = $(call allfiles,$(1),$(2),$(3),$(allstandalonenames))
alleverything = $(call allfiles,$(1),$(2),$(3),$(4) $(allnames))

stylezip = $(FILEPREFIX)$(ZIP)/$(1)/$(STYLES)/$(2).zip

allfilezips = $(patsubst %,$(FILEPREFIX)$(ZIP)/$(1)/%.zip,$(4) $(allnames))
allstylezips = $(foreach style,$(call stylenames,$(2)),$(call stylezip,$(1),$(style)))

# UNSTYLED FILES #
# For use with plain LaTeX/text.

# $(1): The folder name
# $(2): The resulting file extension
# $(3): File names

unstyledfiles = $(if $(findstring /,$(2)),$(patsubst %,$(FILEPREFIX)$(1)/%/$(notdir $(2)),$(3)),$(patsubst %,$(FILEPREFIX)$(1)/%.$(2),$(3)))
unstyledchapters = $(call unstyledfiles,$(1),$(2),$(addprefix $(CHAPTERPREFIX),$(allchapternames)))
unstyledappendixes = $(call unstyledfiles,$(1),$(2),$(addprefix $(APPENDIXPREFIX),$(allappendixnames)))
unstyledstandalones = $(call unstyledfiles,$(1),$(2),$(allstandalonenames))
unstyledeverything = $(call unstyledfiles,$(1),$(2),$(allnames) $(3))

unstylednames = $(if $(findstring /,$(3)),$(patsubst $(FILEPREFIX)$(1)/%/$(notdir $(2)),%,$(3)),$(patsubst $(FILEPREFIX)$(1)/%.$(2),%,$(3)))

unstyledzip = $(FILEPREFIX)$(ZIP)/$(1).zip

# COMMON TARGETS #
# For use with $(eval).

define targettype
$(if $(findstring /,$(3)),$(patsubst %/,%,$(dir $(3))),$(3)): $$(if $$(NOARCHIVE),$$(call alleverything,$(1),$(2),$(3),$(4)),$$(call allstylezips,$(1),$(2)) $$(call allfilezips,$(1),$(2),$(3),$(4)));
.PHONY: $$(3)
endef

# This rule uses a double­‑colon since it is possible that multiple different file types use the same style name.
define targetstyles
$$(call stylenames,$(2)):: $$(if $$(NOARCHIVE),$$$$(call everything,$(1),$$$$@,$(3),$(4)),$$$$(call stylezip,$(1),$$$$@));
.PHONY: $$(call stylenames,$(2))
endef

define targetstylezips
$$(call allstylezips,$(1),$(2)): $(FILEPREFIX)$(ZIP)/$(1)/$(STYLES)/%.zip: $$$$(call everything,$(1),$$$$*,$(3),$(4)); $(if $(findstring /,$(3)),$$(call makezip,$$(call everything,$(1),$$*,/*,$(4))),$$(makezip))
zip: $$(call allstylezips,$(1),$(2))
endef

define targetfilezips
$$(call allfilezips,$(1),$(2),$(3),$(4)): $(FILEPREFIX)$(ZIP)/$(1)/%.zip: $$$$(call allfiles,$(1),$(2),$(3),$$$$*); $(if $(findstring /,$(3)),$$(call makezip,$$(call allfiles,$(1),$(2),/*,$$*)),$$(makezip))
zip: $$(call allfilezips,$(1),$(2),$(3),$(4))
endef

define targets
$$(eval $$(call targettype,$(1),$(2),$(3),$(4)))
$$(eval $$(call targetstyles,$(1),$(2),$(3),$(4)))
$$(eval $$(call targetstylezips,$(1),$(2),$(3),$(4)))
$$(eval $$(call targetfilezips,$(1),$(2),$(3),$(4)))
endef

# COMMON UNSTYLED TARGETS #
# For use with $(eval).

define unstyledtargettype
$(if $(findstring /,$(2)),$(patsubst %/,%,$(dir $(2))),$(2)): $$(if $$(NOARCHIVE),$$(call unstyledeverything,$(1),$(2),$(3)),$$(call unstyledzip,$(1))) ;
.PHONY: $$(2)
endef

define unstyledtargetzip
$$(call unstyledzip,$(1)): $$(call unstyledeverything,$(1),$(2),$(3)); $(if $(findstring /,$(2)),$$(call makezip,$$(call unstyledeverything,$(1),/*,$(3))),$$(makezip))
zip: $$(call unstyledzip,$(1))
endef

define unstyledtargets
$$(eval $$(call unstyledtargettype,$(1),$(2),$(3)))
$$(eval $$(call unstyledtargetzip,$(1),$(2),$(3)))
endef

# YAML #

# We do not want to set the chapter for standalone documents.

chapteryaml = $(if $(findstring $(call types,$<),chapter appendix),echo "number: $(call autonumbers,$<)";)

fileyaml = echo "filename: $(call names, $<)"; $(chapteryaml)$(if $(DRAFTS),echo "draft: $(basename $(notdir $(lastword $(sort $(wildcard $(DRAFTS)/$(call standalonenames,$<)/*.md)))))";) echo "type: $(call types,$<)";

# Empty YAML if no file exists.
$(YAML):
	touch $(YAML)

# Markdown #

$(eval $(call unstyledtargets,$(MARKDOWN),md))

ifdef DRAFTS
# This rule is just for symlink creation in the case that a `DRAFTS` override has been set.
$(allsrcs): $(FILEPREFIX)$(MARKDOWN)/%.md: $$(lastword $$(sort $$(wildcard $(DRAFTS)/$$*/*.md))); $(link)
endif

# LaTeX #

$(eval $(call unstyledtargets,$(LATEX),tex,$(INDEX)))

$(call unstyledeverything,$(LATEX),tex): $(FILEPREFIX)$(LATEX)/%.tex: $$(call srcs,$$*) $(YAML) $(srcdir)/default.yml $(srcdir)/template.tex $(srcdir)/pandoc-custom.py $(srcdir)/pandoc-latex.py
	$(makefolders)
	(cat $<; echo; echo; echo "---"; cat $(YAML); $(fileyaml) echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "...") | $(PANDOC) -f markdown-smart -t latex-smart --standalone --template "$(srcdir)/template.tex" --filter "$(srcdir)/pandoc-custom.py" --filter "$(srcdir)/pandoc-latex.py" -o $@ --top-level-division=chapter $(if $(BIBLIOGRAPHY),--biblatex,)
	@echo "LaTeX file for $< generated at $@"

$(FILEPREFIX)$(LATEX)/$(INDEX).tex: $$(call unstyledeverything,$(LATEX),tex) $(YAML) $(srcdir)/default.yml $(srcdir)/pandoc-custom.py $(srcdir)/pandoc-latex.py
	$(makefolders)
	(echo "---"; cat $(YAML); echo "type: index"; echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "...") | $(PANDOC) -f markdown-smart -t latex-smart --standalone --template "$(srcdir)/template.tex" --filter "$(srcdir)/pandoc-custom.py" --filter "$(srcdir)/pandoc-latex.py" -o $@ --top-level-division=chapter $(if $(BIBLIOGRAPHY),--biblatex,)
	(echo "\\\\frontmatter"; $(foreach standalone,$(allstandalonenames),echo "\\\\include{$(standalone)}";) echo "\\\\cleardoublepage\\\\tableofcontents"; echo; echo "\\\\clearpage\\\\null\\\\thispagestyle{cleared}\\\\cleartooddpage[\\\\null\\\\thispagestyle{cleared}]\\\\mainmatter"; echo; $(foreach chapter,$(allchapternames),echo "\\\\include{$(CHAPTERPREFIX)$(chapter)}";) echo; $(if $(allappendixnames),echo "\\\\clearpage\\\\null\\\\thispagestyle{cleared}\\\\cleartooddpage[\null\thispagestyle{cleared}]\\\\appendix\\\\appendixpage"; $(foreach appendix,$(allappendixnames),echo "\\\\include{$(APPENDIXPREFIX)$(appendix)}";) echo;,) echo "\\\\clearpage$(if $(BIBLIOGRAPHY),\\\\null\\\\thispagestyle{cleared}\\\\cleartooddpage[\\\\null\\\\thispagestyle{cleared}]\\\\backmatter\\\\printbibliography,)") >> $@
	@echo "LaTeX index generated at $@"

# HTML #

$(eval $(call targets,$(HTML),css,xhtml,$(INDEX) $(basename $(BIBLIOGRAPHY))))

$(call alleverything,$(HTML),css,xhtml): $$(call srcs,$$(call filenames,$(HTML),$$(call styles,$$@),xhtml,$$@)) $(YAML) $(srcdir)/default.yml $(STYLES)/$$(call styles,$$@).css $(srcdir)/template.xhtml $(srcdir)/pandoc-custom.py $$(wildcard $(STYLES)/$$(call styles,$$@).py) $(srcdir)/pandoc-html.py
	$(makefolders)
	(cat $<; echo; echo; echo "---"; echo "suppress-bibliography: true";$(if $(BIBLIOGRAPHY), echo "bibliography: $(addsuffix .bib,$(basename $(BIBLIOGRAPHY)))"; echo "citation-style: '$(realpath $(srcdir)/chicago-note-bibliography-16th-edition.csl)'";) echo "style: $(call styles,$@)"; echo "styles:"; echo "- name: $(call styles,$@)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$(call styles,$@).css | sed 's/^/    /'; echo '    ```';$(if $(ALLSTYLES),$(foreach style,$(filter-out $(call styles,$@),$(stylenames)), echo "- name: $(style)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$(style).css | sed 's/^/    /'; echo '    ```';)) cat $(YAML); $(fileyaml) echo "self: $(call relativepath,$(call standalonenames,$<).xhtml,$(call standalonenames,$<)).xhtml"; echo "index: $(call relativepath,$(call standalonenames,$<).xhtml,$(INDEX)).xhtml"; echo "first: $(or $(call relativepath,$(call standalonenames,$<).xhtml,$(filter-out $(call standalonenames,$<),$(firstword $(allnames)))),$(octothorpe) ).xhtml"; echo "last: $(or $(call relativepath,$(call standalonenames,$<).xhtml,$(filter-out $(call standalonenames,$<),$(lastword $(allnames)))),$(octothorpe) ).xhtml"; echo "prev: $(or $(call relativepath,$(call standalonenames,$<).xhtml,$(lastword $(subst :,$(space),$(firstword $(subst $(call standalonenames,$<), ,$(subst $(space),:,$(allnames))))))),$(octothorpe) ).xhtml"; echo "next: $(or $(call relativepath,$(call standalonenames,$<).xhtml,$(firstword $(subst :,$(space),$(lastword $(subst $(call standalonenames,$<), ,$(subst $(space),:,$(allnames))))))),$(octothorpe) ).xhtml";$(if $(BIBLIOGRAPHY), echo "biblio: $(call relativepath,$(call standalonenames,$<).xhtml,$(basename $(BIBLIOGRAPHY))).xhtml";) echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "...") | $(PANDOC) -f markdown-smart -t html5-smart --standalone --template "$(srcdir)/template.xhtml" --filter "$(srcdir)/pandoc-custom.py"$(if $(wildcard $(STYLES)/$(call styles,$@).py), --filter $(STYLES)/$(call styles,$@).py) --filter "$(srcdir)/pandoc-html.py"$(if $(BIBLIOGRAPHY), --filter $(CITEPROC)) -o $@ --self-contained --mathml
	@echo "$(call styles,$@) HTML file for $< generated at $@"

$(call allfiles,$(HTML),css,xhtml,$(INDEX)): $(FILEPREFIX)$(HTML)/%/$(INDEX).xhtml: $(YAML) $(srcdir)/default.yml $(STYLES)/%.css $(srcdir)/template.xhtml $(srcdir)/pandoc-custom.py $$(wildcard $(STYLES)/%.py) $(srcdir)/pandoc-html.py
	$(makefolders)
	(echo '<div id="BookGen.toc">'; echo; echo '```{=html}'; echo '<nav xmlns:epub="http://www.idpf.org/2007/ops" epub:type="toc" role="doc-toc">'; echo '```'; echo; echo '$(octothorpe) []{data-from-metadata="localization-type-index"}'; echo;$(foreach standalone,$(allstandalonenames), echo '$(octothorpe). [$(standalone)]($(call relativepath,$(INDEX).xhtml,$(standalone)).xhtml$(octothorpe)BookGen.main)';) echo '$(octothorpe). [[]{data-from-metadata="localization-type-index"}]($(call relativepath,$(INDEX).xhtml,$(INDEX)).xhtml$(octothorpe)BookGen.main)';$(if $(allchapternames), echo '$(octothorpe). ::: plain :::'; echo '[]{data-from-metadata="localization-chapters"}'; echo ':::::::::::::';$(foreach chapter,$(allchapternames), echo '    $(octothorpe). [[]{data-from-metadata="localization-type-chapter"} $(chapter)]($(call relativepath,$(INDEX).xhtml,$(CHAPTERPREFIX)$(chapter)).xhtml$(octothorpe)BookGen.main)';))$(if $(allappendixnames), echo '$(octothorpe). ::: plain :::'; echo '[]{data-from-metadata="localization-appendices"}'; echo ':::::::::::::';$(foreach appendix,$(allappendixnames), echo '    $(octothorpe). [[]{data-from-metadata="localization-type-appendix"} $(appendix)]($(call relativepath,$(INDEX).xhtml,$(APPENDIXPREFIX)$(appendix)).xhtml$(octothorpe)BookGen.main)';))$(if $(BIBLIOGRAPHY), echo '$(octothorpe). [[]{data-from-metadata="localization-type-biblio"}]($(call relativepath,$(INDEX).xhtml,$(basename $(BIBLIOGRAPHY))).xhtml$(octothorpe)BookGen.main)';) echo; echo '```{=html}'; echo '</nav>'; echo '```'; echo; echo '</div>'; echo "---"; echo "style: $(call styles,$@)"; echo "styles:"; echo "- name: $(call styles,$@)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$*.css | sed 's/^/    /'; echo '    ```';$(if $(ALLSTYLES),$(foreach style,$(filter-out $*,$(stylenames)), echo "- name: $(style)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$*.css | sed 's/^/    /'; echo '    ```';)) cat $(YAML); echo "type: index"; echo "self: $(call relativepath,$(INDEX).xhtml,$(INDEX)).xhtml"; echo "index: $(call relativepath,$(INDEX).xhtml,$(INDEX)).xhtml";$(if $(BIBLIOGRAPHY), echo "biblio: $(call relativepath,$(INDEX).xhtml,$(basename $(BIBLIOGRAPHY))).xhtml";) echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "...") | $(PANDOC) -f markdown-smart -t html5-smart --standalone --template "$(srcdir)/template.xhtml" --filter "$(srcdir)/pandoc-custom.py"$(if $(wildcard $(STYLES)/$*.py), --filter $(STYLES)/$*.py) --filter "$(srcdir)/pandoc-html.py" $(if $(BIBLIOGRAPHY),--filter $(CITEPROC)) -o $@ --self-contained
	@echo "$* HTML index generated at $@"

$(call allfiles,$(HTML),css,xhtml,$(basename $(BIBLIOGRAPHY))): $(FILEPREFIX)$(HTML)/%/$(basename $(BIBLIOGRAPHY)).xhtml: $(BIBLIOGRAPHY) $(YAML) $(srcdir)/default.yml $(STYLES)/%.css $(srcdir)/template.xhtml $(srcdir)/pandoc-custom.py $$(wildcard $(STYLES)/%.py) $(srcdir)/pandoc-html.py
	$(makefolders)
	(echo "---"; echo "bibliography: $(addsuffix .bib,$(basename $(BIBLIOGRAPHY)))"; echo "citation-style: '$(realpath $(srcdir)/chicago-note-bibliography-16th-edition.csl)'"; echo "style: $(call styles,$@)"; echo "styles:"; echo "- name: $(call styles,$@)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$*.css | sed 's/^/    /'; echo '    ```'; $(if $(ALLSTYLES),$(foreach style,$(filter-out $*,$(stylenames)),echo "- name: $(style)"; echo "  css: |"; echo '    ```{=html}'; cat $(STYLES)/$(style).css | sed 's/^/    /'; echo '    ```';)) cat $(YAML); echo "type: bibliography"; echo "self: $(call relativepath,$(basename $(BIBLIOGRAPHY)).xhtml,$(basename $(BIBLIOGRAPHY))).xhtml"; echo "index: $(call relativepath,$(basename $(BIBLIOGRAPHY)).xhtml,$(INDEX)).xhtml"; echo "biblio: $(call relativepath,$(basename $(BIBLIOGRAPHY)).xhtml,$(basename $(BIBLIOGRAPHY))).xhtml"; echo "nocite: '@*'"; echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "..."; echo '$(octothorpe) []{data-from-metadata="localization.type.biblio"}') | $(PANDOC) -f markdown-smart -t html5-smart --standalone --template "$(srcdir)/template.xhtml" --filter "$(srcdir)/pandoc-custom.py"$(if $(wildcard $(STYLES)/$*.py), --filter $(STYLES)/$*.py,) --filter "$(srcdir)/pandoc-html.py" --filter $(CITEPROC) -o $@ --self-contained --mathml

# PDF BUILDFILES #

buildtext = cd $(BUILD)/$(call styles,$@) && $(TEX) --jobname=$(call filenames,PDF,$(call styles,$@),pdf,$@) $(if $(VERBOSE),,--interaction=batchmode --halt-on-error) --file-line-error "\documentclass{style}\nofiles\usepackage{bookgen}$(if $(BIBLIOGRAPHY),\usepackage$(BIBREQUIRE)\addbibresource{bibliography.bib}\nocite{*},)\begin{document}$(if $(findstring $(call types,$(call srcs,$(call filenames,PDF,$(call styles,$@),pdf,$@))),chapter appendix),\mainmatter ,\frontmatter )$(if $(findstring $(call types,$(call srcs,$(call filenames,PDF,$(call styles,$@),pdf,$@))),appendix),\appendix )\makeatletter\@nameuse{cp@$(call filenames,PDF,$(call styles,$@),pdf,$@)@pre}\makeatother\input{$(call filenames,PDF,$(call styles,$@),pdf,$@)}\end{document}"

buildfulltext = cd $(BUILD)/$* && $(TEX) --jobname=$(FULLTEXT) $(if $(VERBOSE),,--interaction=batchmode --halt-on-error) --file-line-error "\documentclass{style}\usepackage{Makefile}\usepackage{bookgen}$(if $(BIBLIOGRAPHY),\usepackage$(BIBREQUIRE)\addbibresource{bibliography.bib}\nocite{*},)\input{GO.xxx}\input{$(INDEX)}\afterfulltext\end{document}"

$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,sty,Makefile)): $(srcdir)/DeluxeMakefile/Makefile.sty; $(link)
$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,sty,bookgen)): $(srcdir)/bookgen.sty; $(link)
$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,cls,style)): $(BUILD)/%/style.cls: $(STYLES)/%.cls; $(link)
$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,bib,bibliography)): $(addsuffix .bib,$(basename $(BIBLIOGRAPHY))); $(link)
$(patsubst $(FILEPREFIX)%,%,$(call alleverything,$(BUILD),cls,tex,$(INDEX))): $$(call unstyledfiles,$(LATEX),tex,$$(call filenames,$(BUILD),$$(call styles,$(FILEPREFIX)$$@),tex,$(FILEPREFIX)$$@)); $(link)

$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,xxx,GO)): $(BUILD)/%/GO.xxx: $(YAML) $(srcdir)/default.yml $(srcdir)/template-index.tex $(srcdir)/pandoc-custom.py $$(wildcard $(STYLES)/%.py) $(srcdir)/pandoc-latex.py
	$(makefolders)
	(echo "---"; cat $(YAML); echo "style: $*"; echo 'outputfile: "$@"'; echo "..."; echo "---"; cat "$(srcdir)/default.yml"; echo "...") | $(PANDOC) -f markdown -t latex --standalone --template "$(srcdir)/template-index.tex" --filter "$(srcdir)/pandoc-custom.py"$(if $(wildcard $(STYLES)/$*.py), --filter $(STYLES)/$*.py,) --filter "$(srcdir)/pandoc-latex.py" -o $@

$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,aux,$(FULLTEXT))): $(BUILD)/%/$(FULLTEXT).aux: $(patsubst $(FILEPREFIX)%,%,$(call alleverything,$(BUILD),cls,tex,$(INDEX))) $(BUILD)/%/Makefile.sty $(BUILD)/%/bookgen.sty $(BUILD)/%/GO.xxx $(BUILD)/%/style.cls
	$(makefolders)
	$(buildfulltext)

$(patsubst $(FILEPREFIX)%,%,$(call allfiles,$(BUILD),cls,bbl,$(FULLTEXT))): $(BUILD)/%/$(FULLTEXT).bbl: $(BUILD)/%/bibliography.bib | $(BUILD)/%/$(FULLTEXT).aux
	cd $(BUILD)/$* && $(BIBER) $(if $(VERBOSE),,--onlylog) $(FULLTEXT)

# PDF #

$(eval $(call targets,$(PDF),cls,pdf,$(FULLTEXT)))

$(call alleverything,$(PDF),cls,pdf): $(FILEPREFIX)$(PDF)/$$(call styles,$$@)/$(FULLTEXT).pdf
	$(makefolders)
	$(buildtext)
	$(if $(VECTORIZE),$(GS) -dNoOutputFonts -sDEVICE=pdfwrite $(if $(VERBOSE),,-dQUIET) -o $@ $(BUILD)/$(call styles,$@)/$(call filenames,$(PDF),$(call styles,$@),pdf,$@).pdf,mv -f $(BUILD)/$(call styles,$@)/$(call filenames,$(PDF),$(call styles,$@),pdf,$@).pdf $@)
	@echo "$(call styles,$@) PDF for $(call srcs,$(call filenames,$(PDF),$(call styles,$@),pdf,$@)) generated at $@"

$(call allfiles,$(PDF),cls,pdf,$(FULLTEXT)): $(FILEPREFIX)$(PDF)/%/$(FULLTEXT).pdf: $(BUILD)/%/$(FULLTEXT).aux $(if $(BIBLIOGRAPHY),$(BUILD)/%/$(FULLTEXT).bbl,)
	$(makefolders)
	$(buildfulltext)
	$(if $(VECTORIZE),$(GS) -dNoOutputFonts -sDEVICE=pdfwrite $(if $(VERBOSE),,-dQUIET) -o $@ $(BUILD)/$*/$(FULLTEXT).pdf,mv -f $(BUILD)/$*/$(FULLTEXT).pdf $@)
	@echo "$* fulltext PDF generated at $@"

# PNGs #

$(eval $(call targets,$(PNG),cls,png/index.html,$(FULLTEXT)))

$(call alleverything,$(PNG),cls,png/index.html,$(FULLTEXT)): $(FILEPREFIX)$(PNG)/%/index.html: $(FILEPREFIX)$(PDF)/%.pdf $(srcdir)/StoryTime/index.html
	$(makefolders)
	cp "$(srcdir)/StoryTime/index.html" $@
	echo "<TITLE>$(call standalonenames,$*) PNGs</TITLE>" >> $@
	$(CONVERT) -density 144 $< -quality 100 -colorspace RGB -alpha remove $(dir $@)Page_%d.png; ((( m=0 )); for page in $(dir $@)/*.png; do echo; echo Page_$$m.png | tr '\n' ' '; (( m++ )); $(PDFTOTEXT) -f $$m -l $$m -enc UTF-8 -eol unix -nopgbrk -raw $< - | awk '{gsub(/-\n/, ""); print}' | tr '\n' ' ' | tr -s ' '; done; echo) >> $@
	@echo $(if $(findstring $(FILEPREFIX)$(PNG)/$(call styles,$@)/$(FULLTEXT)/index.html,$@),"$(call styles,$@) fulltext PNGs generated at $(dir $@)","$(call styles,$@) PNGs for $(call srcs,$(call filenames,$(PNG),$(call styles,$@),/index.html,$@)) generated at $(dir $@)")
