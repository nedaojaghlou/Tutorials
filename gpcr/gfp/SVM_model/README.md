SVM model training / prediction 

`svmfp_caliberate.sh `: Determine which SVM-FPs give the best predictive model

### Setup: 

```python
x[['smiles','Index']].to_csv('train.smi', header=None, index=False, sep=' ') 
x[['Index','gap']].to_csv('activity.smi', header=None, index=False, sep=' ') 
```

To generate files: 

```
svmfp_calibrate.sh -niter 2 -TRpct 70 -A ../activity.smi -smiles ../train.smi -F results.txt
```

To summarize results and find the best possible FPs: 

```
svmfp_summarise_results.sh -F results.txt -B best_model/ -v -R R2
```
```
svmfp_summarise_results.sh -F results.txt -B best_fingerprint -v -R R2
```
Best performing FPs for the run for instance : `-ERG -EC4:Y -RS -PSA -CLOGP -NCIW7`

Now to create your own model: 

1. Option 1: This is used together in the svmfp_make.V2.sh! 
    * SVM lite: This seems to be the default SVM model 
    * gfp_to_svm_lite.v2.sh
    * svm_learn.v2.sh

2. Option 2: For descriptor type files 
    * Build SVM from descriptor file and evaluate
    * svmd_build.sh
    * svmd_evaluate.sh 

3. Option 3: Most straight forward
    * `svmfp_make.v2.sh` -- this uses svm lite! Builds a final SVM fingerprint model - build model on whole training set.

### Testing it out:

```bash
gfp_make.sh -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 ../test.smi >> test.gfp
```

Information from what_kind_of_model - this is generated in the svm_make output 
threshold_b 0.17361492
support_vectors 2969

`gfp_svmfp_score.sh` 

gfp_svmfp_score.sh evaluates an svmfp predictive model built using svmfp_make.v2.sh.  Output file is a table with two columns: id prediction-value.


## Building my own model: 

To look at different FP types: 
```bash
svmfp_calibrate.sh -niter 2 -TRpct 70 -A ../activity.smi -smiles ../train.smi -F results.txt
```
- How to do this locally? 

Determine which FP gives best results
```bash
svmfp_summarise_results.sh -F results.txt -B best_model/ -v -R R2
```

To train own model: 
```bash
svmfp_make.v2.sh -smiles train.smi -A activity.smi -gfp -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 -gfp -mdir build_my_own -v
```

To test it on the new data: 

1. Option 1: 
```bash
gfp_make.sh -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 ../test.smi >> test.gfp
gfp_svmfp_score.sh -X ../build_my_own/bit_xref -U ../build_my_own/for_gfp_svmfp_evaluate.gfp -b 0.17361492 -s 2969 -N ../build_my_own/activity_normalisation_data -v -H 'ACTIVITY' -o allow test.gfp >> SVM_predict_test.txt
```

2. Option 2: 
Same as Option 1 but single script for doing so: 
```bash 
svmfp_evaluate.v2.sh -mdir ../build_my_own/ -v ../test.smi >> test.results
```

## List of cmd that went into svmfp_caliberate
grep 'Executing' SVMFPCC.o88557132.75 >> executing_cmds_svm

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/ruby/svmfp_make.v2.rb -v -gfp -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 -gfp -mdir /node/scratch/88557132.75.all.normal.q013819 -tmpdir /node/scratch/88557132.75.all.normal.q -smi ./svmcmtmp26\
907R0.smi -A ./svmcmtmp26907.activity -m 500'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/normalise.sh -j -N 11 -C /node/scratch/88557132.75.all.normal.q013819/activity_normalisation_data ./svmcmtmp26907.activity > /node/scratch/88557132.75.all.normal.q/svmfpmaketmp138240\
804.anorm'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/iwcut.sh -f 1,2 ./svmcmtmp26907R0.smi | /lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/unique_rows.sh -v -c 2 -| tee /node/scratch/88557132.75.all.normal.q013819/train.smi | /lrlh\
ps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/gfp_make.sh -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 - > /node/scratch/88557132.75.all.normal.q013819/train.gfp'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/gfp_to_svm_lite.v2.sh -p 2 -A /node/scratch/88557132.75.all.normal.q/svmfpmaketmp138240804.anorm -C /node/scratch/88557132.75.all.normal.q013819/bit_xref /node/scratch/88557132.75.al\
l.normal.q013819/train.gfp> /node/scratch/88557132.75.all.normal.q013819/train.svml'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/svm_learn.v2.sh -m 500 -t 4 -z r /node/scratch/88557132.75.all.normal.q013819/train.svml /node/scratch/88557132.75.all.normal.q013819/train.svml.model'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/iwcut.sh -f 1,2 ./svmcmtmp26907.activity  > /node/scratch/88557132.75.all.normal.q013819/train.activity'

Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/ruby/svmfp_evaluate.v2.rb  -v -tmpdir /node/scratch/88557132.75.all.normal.q -mdir /node/scratch/88557132.75.all.normal.q013819 ./svmcmtmp26907E0.smi > /node/scratch/88557132.75.all.nor\
mal.q/svmfpcbc13819.pred.0'

**Testing out?**
.Executing '/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/gfp_make.sh -ERG -EC4:Y -RS -PSA -CLOGP -NCIW7 ./svmcmtmp26907E0.smi |/lrlhps/users/l017301/repo/gc3tk/c3tk-core/bin/sh/gfp_svmfp_score.sh -X /node/scratch/88557132.75.all.normal.q0\
13819/bit_xref -U /node/scratch/88557132.75.all.normal.q013819/for_gfp_svmfp_evaluate.gfp -b 0.18651706 -s 2344 -N /node/scratch/88557132.75.all.normal.q013819/activity_normalisation_data -v -H 'ACTIVITY' -o allow -o quiet -'
