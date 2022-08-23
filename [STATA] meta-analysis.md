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

![image](https://user-images.githubusercontent.com/41531140/186229834-d16a8930-5782-4754-94cf-d4e3c209ecfa.png)


```stata
metan OR LCI UCI, random label(namevar=Study)
```

![image](https://user-images.githubusercontent.com/41531140/186229866-6fac065f-f7bb-41a4-8fea-800f3a6f6274.png)


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
![image](https://user-images.githubusercontent.com/41531140/186229923-8a54c6f3-bfa8-448a-b425-c4b1b65961fe.png)


We can get publication bias like below.

```stata
metabias logor selogor, egger
```
![image](https://user-images.githubusercontent.com/41531140/186229946-82cd34f4-341b-46aa-8a9f-8290ae93e2ad.png)


## **HR**

It is very similar to the things we've done above. 

After you import your HR data (I have variable **Author, Year, HR, LHR, UHR**),

```stata
metan HR LHR UHR, random label(namevar=Author)
metan HR LHR UHR, random label(namevar=Author) sortby(Author)
```
![image](https://user-images.githubusercontent.com/41531140/186229975-4f447a31-568f-4425-873c-aad3cc1a80c8.png)


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
![image](https://user-images.githubusercontent.com/41531140/186230006-2655e5e0-0b5a-4133-897d-c399b59de4d2.png)


You also can get funnel plot

```stata
metafunnel sensitivity SE_sen
metafunnel specificity SE_spe
```
