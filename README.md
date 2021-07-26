# Project-course-L12


In this projcet I tried to implemt BISPECTRAL using Python code in Colab.

find the final version of the code here: (https://github.com/enzojam/Project-course-L12)

The implementation of the code is based on previous approaches mentioned in the report.

the main part of this implementation is BISPECTRAL function which can be summarized in this piece of python code:

1. for i in range(0, nrecs):
2. ys = y[ind[0]:ind[-1]+1]
3. ys = (ys- np.mean(ys))\*wind
4. Yf = np.array(fft(ys,nfft))/ nsamp
5. CYf = np.conjugate(Yf)
6. Pyy = Pyy + Yf \* CYf
7. CYf\_shi = np.roll(CYf,1)
8. Yf12 = hankel(CYf,CYf\_shi)
9. bic = bic + (Yf \* Yf.reshape((-1, 1))) \* Yf12
10. ind = ind + nadvance

The bicoherence is computed by dividing the signal into overlapping segments of length 32 with an overlap of 50%. A 2048-point windowed DFT, &quot;ys&quot;, is estimated for each segment from which the bicoherence is estimated. We divide the signal into &quot;nrecs&quot; segment consideringlength of segments 32 with an overlap of 50%.

In line 3:

The mean value of the ys is reduced and muliplued by wind, wind is our window here. Window variable can be both &quot;hamming&quot; and &quot;hanning&quot;

In line 4:

YF is our Ys Fourier transform. the then values FT is normalized by dividing &quot;nsamp&quot;

In 5: we introduce a new variable CYf which is Yf complex conjugate.

In 6:

The Pyy is the power of Yf

In 7 &amp; 8:

Complex conjugate of signal Ys is shifted with &quot;Hankel&quot; function in order to make the mulityplication less complex.

And in 9 you can see the implementation of below formula

\begin{equation}

\label{eqn:bis}

B\left(\omega\_1,\omega\_2\right)=Y\left(\omega\_1\right)Y\left(\omega\_2\right)Y^\ast\left({\omega\_1+\omega}\_2\right)

\end{equation}

1. bicc     = bic / nrecs
2. angles=(sum(np.angle(bicc)))
3. means[0] = np.mean(angles)
4. X\_mag,X\_phase=librosa.magphase(fftshift(bicc))
5. Pyy     = Pyy  / nrecs
6. Pyy\_shi = np.roll(Pyy,1)
7. mask = hankel(Pyy,Pyy\_shi)

in line 1 :

Bicc is the avvrage value of Bic

In line 2:

The phase value of the Bicc is calculated and the summation applied in order to make it 1D.

In line 3: the man value of angels is computed.

In line 5 &amp; 6 &amp; 7:

The value of Bicc power computed for future uses

  if normalization==1 : # -------- Defualt

    den = (Pyy \* Pyy.reshape((-1, 1))\*mask)

    bic = abs(bicc)\*\*2 /abs(den)

  elif normalization == 2: #---------- papre

    den = (abs(Pyy \* Pyy.reshape((-1, 1)))\*\*2 \* abs(mask)\*\*2)

    bic = (bicc) / np.sqrt(den)

  elif normalization == 3:  # -------- 1 wiki

    den = pow(abs(Pyy),2) \* pow(abs(Pyy.reshape((-1, 1))),2) \* abs(mask)\*\*2

    denn = pow(den,-2)

    bic = abs(bicc) / denn

  elif normalization == 4: # --------- 2 wiki

    den = (abs(Pyy \* Pyy.reshape((-1, 1)))\*\*2 \* abs(mask)\*\*2)

    bic = abs(bicc)\*\*2  / (den)

  elif normalization == 5: # --------- 3 wiki

    den = abs(Pyy \* Pyy.reshape((-1, 1)) \* mask)

    bic = abs(bicc)  / (den)

in above code there are 5 different types of normalization. You can find the formula in the report:

statistic = [var\_ang,var\_bic,std\_ang,std\_bic,skew\_ang,skew\_bic,kurtosis\_ang,kurtosis\_bic]

the output of the Bis function is simply like the above code,

variance, standard deviation, skewness and kurtosis values are calculated both for Bis magnitude and phase.

**Test:**

In order to test the algorithm, we simply made a sinusoidal signal plus a white noise for the input and

Results are as following:

Left fig: is the input signal

Middle fig: bicoherence absolute value of the signal

And in the right fig: you can see the histogram of the bicoherence phase.

![](1.png)

Three type of non-liniarty transformation apllyed on the simpyle sinosied signal plus a white noise (you can find more detailes in report)

These three different version of non-linearty were tested and the value of the variance, standard deviation, skewness, and kurtosis values are calculated both for Bis magnitude and phase where calculated. The results were illustrated in 2D as following:

|
 | Y axis | X axis |
| --- | --- | --- |
| variance | Bic magnitude | Bic phase |
| Standard deviation | Bic magnitude | Bic phase |
| skewness | Bic magnitude | Bic phase |
| kurtosis | Bic magnitude | Bic phase |

![](2.png)

![](3.png) ![](4.png)
