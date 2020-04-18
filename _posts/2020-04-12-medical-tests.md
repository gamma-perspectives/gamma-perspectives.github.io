Last week I was reading a twitter thread that talked about
sensitivity/specificity of diagnostic tests (in the context of Covid)
and how the diagnostic value varies with the prevalance of the disease
in the general population. I spent sometime trying to understand the
math behind the whole thing and here it is

Definitions
-----------

Sensitivity or Recall = True posistives / Number of people with disease
in sample

In other words, P(Test=Positive| Patient = Diseased)

Specificity = True Negatives / Number of people with no disease in
sample

In other words, P(Test=Negative | Patient = Non-diseased)

Now, lets look at the False Positives. This is the set of people who do
not have the disease, but the test shows them as having it.

In other words, P(Test=Positive | Patient = Non-diseased) = 1-
P(Test=Negative | Patient = Nondiseased)

FPR = 1 - Specificity

Similarly, we can dervice TPR = 1 - Sensitivity

Interpreting Testing Results
----------------------------

Now, lets say the Test comes out positive. How woried should the person
be that she actually has the disease. In other words, we need to
determine

P(Patient=Diseased | Test=Positive) = P(Patient=Diseased ^
Test=Positive) / P(Test=Positive) = TP / (TP + FP)

In medical jargon, this is called Positive Predictive Value. Computer
Scientists call it Precision.

Lets ask the other side of the question. How comfortable should a
patient be, if the test comes out negative.

In other words, we need to estimate, P(Patient=Non-diseased | Test=
Negative) = TN /(TN+FN)

In medical jargon, this quantity is called Negative Predictive Value.

Both positive and negative predictive values are functions of the test.
However, their practical value depends on the prevalance of the disease
as we will see next.

Effect of Prevalance
--------------------

Recall Precision = TP / (TP+FP)

Now, when there is low prevalance, TP tends to be small in the overall
population. However, FP is determined by the specificity, in particular,
(1 - specificity) of the test since a large section of the population is
non-diseased. Consequently, unless the test has 100% specificity, a
large number of FPs will show up, thereby reducing the precision or PPV.
On the other hand, as the prevalance of the disease in the population
goes up, a high sensitivity test will capture more of the diseased
patients and FP tends to go down. As we can see in the graph belwo, PPV
increases with prevalance.

A similar argument explains why NPV is high when prevalance is low since
FN tends to be small when most of the pouplation is non diseased. On
other hand, NPV goes down with increasing prealance, since the FNs now
start dominating.

    sensitivity = 0.9
    specificity = 0.8
    prevalance_array=c(0.1,0.2,0.3,0.4,0.5,0.6,0.7,0.8,0.9)
    ppv=c()
    npv=c()
    for (prevalance in prevalance_array){
      tp = sensitivity * prevalance
      fp = (1-specificity) * (1-prevalance)
      tn= (specificity) * (1-prevalance)
      fn= (1-sensitivity) * prevalance
      curr_ppv=tp/(tp+fp)
      curr_npv=tn/(tn+fn)
      
      ppv <- append(ppv,curr_ppv)
      npv <- append(npv,curr_npv)
    }
    ppv

    ## [1] 0.3333333 0.5294118 0.6585366 0.7500000 0.8181818 0.8709677 0.9130435
    ## [8] 0.9473684 0.9759036

    npv

    ## [1] 0.9863014 0.9696970 0.9491525 0.9230769 0.8888889 0.8421053 0.7741935
    ## [8] 0.6666667 0.4705882

    plot(prevalance_array,ppv,type="l",col="blue",ylab="Predictive value",xlab="prevalance in population")
    lines(prevalance_array,npv,type="l",col="red")
    legend(0.1,0.8,legend=c("ppv","npv"),col=c("blue","red"),lty=1:2)

![](medical-tests_files/figure-markdown_strict/unnamed-chunk-1-1.png)
