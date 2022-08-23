## **OR**

```stata
/* install packages for meta */
ssc install metan
ssc install metabias
ssc install metafunnel
ssc install midas
```

```stata
import excel "\\Data1.xlsx", sheet("Fig2") firstrow
```

 The data we have has variable "**Study, OR, LCI, UCI**"

The data is from the paper about aspirin and breast cancer (Ting Lue et al, 2012)

```stata
/* Forest Plot */
metan OR LCI UCI
```

[##_Image|kage@dVrwWg/btrxfvUxos0/V9XSeR6S4dSaWtrim872U0/img.png|CDM|1.3|{"originWidth":469,"originHeight":702,"style":"alignCenter"}_##]

```stata
metan OR LCI UCI, random label(namevar=Study)
```

[##_Image|kage@bZFhfv/btrxgoOpIiF/z7HyDnW5z3K2QdK8pWXc51/img.png|CDM|1.3|{"originWidth":570,"originHeight":707,"style":"alignCenter"}_##]

```stata
metan OR LCI UCI, random label(namevar=Study) sortby(Study)
```

With above code, you can sort the plot by author's name.

Now, we have to convert the values we have to log to assess the publication bias and get funnel plot.

```stata
gen logor = ln(OR)
gen logl = ln(LCI)
gen logu = ln(UCI)
gen selogor = (logu-logl)/(2*invnormal(0.975))
```

We can make the funnel plot

```stata
metafunnel logor selogor
```

[##_Image|kage@Jylw4/btrw6WfjtmG/m4TjcMyEJOE10dIJOhTD4k/img.png|CDM|1.3|{"originWidth":695,"originHeight":504,"style":"alignCenter","width":530,"height":384}_##]

We can get publication bias like below.

```stata
metabias logor selogor, egger
```

[##_Image|kage@cMfYoC/btrw6VAHvhI/MJfIPbWT2WgiBzlaZEcjW1/img.png|CDM|1.3|{"originWidth":575,"originHeight":250,"style":"alignCenter"}_##]

## **HR**

It is very similar to the things we've done above. 

After you import your HR data (I have variable **Author, Year, HR, LHR, UHR**),

```stata
metan HR LHR UHR, random label(namevar=Author)
metan HR LHR UHR, random label(namevar=Author) sortby(Author)
```

[##_Image|kage@zgSyR/btrxdPTwfpn/kZZ1Mm4ifUDKwL93nS13uK/img.png|CDM|1.3|{"originWidth":672,"originHeight":390,"style":"alignCenter"}_##]

For funnel plot and publication bias,

```stata
gen loghr=ln(HR)
gen loglhr=ln(LHR)
gen loguhr=ln(UHR)
gen selogr=(loguhr-loglhr)/(2*invnormal(0.975))

metafunnel loghr seloghr
metabias loghr selogr, egger
```

Then you will get similar results.

## **Getting Sensitivity and Specificity**

After you get your sensitivity and specificity data, you can get forest plots of it. I have (**tp fp fn tn, sensitivity, specificity, SE\_sen, SE\_spe**) If you want to see author's name, you can use 'graph editor'. You can check this [link](https://www.statalist.org/forums/forum/general-stata-discussion/general/1570465-forest-plots-for-sensitivity-specificity).

```stata
midas tp fp fn tn, bfor(dss) id(Author Year) ford fors
```

[##_Image|kage@bgwHeY/btrxb9ZvIjm/W4W4LwlrKnyw15tPNvFue1/img.png|CDM|1.3|{"originWidth":652,"originHeight":493,"style":"alignCenter"}_##]

You also can get funnel plot

```stata
metafunnel sensitivity SE_sen
metafunnel specificity SE_spe
```