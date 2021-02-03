# Makefile syntax
# <target_file> : <dependency1> ...
# <TAB> command to produce target file

# If the dependencies or recipe need to take up more than one line, the line
# must be continued using a backslash.

all : kickapoo_lexicon.lexc \
	kickapoo_gen.hfstol \
	kickapoo_ana.hfstol \
	kickapoo_ana.png \
	kickapoo_lexicon.png \
	kickapoo_sc.twol

kickapoo_lexicon.lexc : root.lexc nouns.lexc verbs.lexc
	cat root.lexc nouns.lexc verbs.lexc > kickapoo_lexicon.lexc

kickapoo_gen.hfst : kickapoo_lexicon.lexc kickapoo_sc.twol
	hfst-lexc < kickapoo_lexicon.lexc > kickapoo_gen.hfst
	hfst-twolc -o kickapoo_sc.hfst kickapoo_sc.twol
	hfst-compose-intersect -1 kickapoo_gen.hfst -2 kickapoo_sc.hfst -o kickapoo_gen_sc.hfst

kickapoo_gen.hfstol : kickapoo_gen.hfst
	hfst-fst2fst --optimized-lookup-unweighted -i kickapoo_gen.hfst -o kickapoo_gen.hfstol

kickapoo_ana.hfst : kickapoo_gen.hfst
	hfst-invert -i kickapoo_gen.hfst -o kickapoo_ana.hfst

kickapoo_ana.hfstol : kickapoo_ana.hfst
	hfst-fst2fst --optimized-lookup-unweighted -i kickapoo_ana.hfst -o kickapoo_ana.hfstol

kickapoo_ana.png : kickapoo_ana.hfst
	hfst-fst2txt kickapoo_ana.hfst | python3 att2dot.py | dot -T png -o kickapoo_ana.png

kickapoo_lexicon.png : kickapoo_lexicon.lexc
	python3 lexc2dot.py <kickapoo_lexicon.lexc | dot -T png -o kickapoo_lexicon.png  # BUG

.PHONY : clean
clean :
	-rm *.hfst *.hfstol kickapoo_lexicon.lexc

.PHONY : test
test :
	sh tests.sh  # sh is a command to run the argument filename as a shell script (usually bash)

strings.txt : kickapoo_gen_sc.hfst
	hfst-fst2strings -X obey-flags kickapoo_gen_sc.hfst > strings.txt
