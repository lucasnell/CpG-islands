CpG Islands
============

Implementation of Takai and Jones’ algorithm for finding CpG islands in genomes
------

### Requirements for CpG islands
- Length ≥ 500 bp
- GC ≥ 0.55
- (Obs<sub>CpG</sub> / Exp<sub>CpG</sub>) ≥ 0.65


##### ... where
- GC = _(number G + number C) / (Length)_
- Obs<sub>CpG</sub> = _(number CpG) / (Length)_
- Exp<sub>CpG</sub> = _(GC / 2)<sup>2</sup>_ [(doi: 10.1073/pnas.0510310103)](
   http://www.pnas.org/cgi/doi/10.1073/pnas.0510310103)


### Algorithm
1. In 200 bp window at beginning of sequence, get %GC & (Obs<sub>CpG</sub> / 
   Exp<sub>CpG</sub>). Shift by 1 bp until it meets criteria above.
2. If the window meets the criteria, shift the window 200 bp and then evaluate again.
3. Repeat these 200-bp shifts until the window does not meet the criteria.
4. Shift the last window 1 bp back (toward the 5' end) until it meets the criteria.
5. Evaluate total %GC and (Obs<sub>CpG</sub> / Exp<sub>CpG</sub>) for this combined 
   strand.
6. If this large CpG island does not meet the criteria, trim 1 bp from each side until
   it meets the criteria.
7. Two individual CpG islands were connected if they were separated by less than 100 bp.
8. Repeat steps 5–6 to evaluate the new sequence segment until it meets the criteria.
9. Reset start position immediately after the CGI identified at step 8 and go to step 1.

> *Note*: From the [original paper](http://www.pnas.org/content/99/6/3740), it seems like 
> ≥ 500 bp filtering happened at the end.


### Example usage

##### Using on file `Glazer_assembly.fa` with 12 cores, saved to `Glazer_CGI.bed`
```
/path/TJalgorithm.py -c 12 -o Glazer_CGI.bed Glazer_assembly.fa
```

> *Note*: Each thread in the parallelization herein consists of finding all CpG islands
> in a single sequence, so providing more cores than chromosomes/sequences is useless.



### Compiling

If using the Cython version (default), you'll need to compile this first:
```
python3 cySetup.py build_ext --inplace
```

If it gives you the following error:
```
'numpy/arrayobject.h' file not found
```

... run the following, then try again.
```
cp -r /usr/local/lib/python3.5/site-packages/numpy/core/include/numpy \
/usr/local/include
```


Citation
-------
Takai D, Jones PA. 2002. Comprehensive analysis of CpG islands in human chromosomes
21 and 22. *Proc Natl Acad Sci USA* __99__:3740–3745. doi: 10.1073/pnas.052410099