## WSIAnalysis
-----------------
### Background <br>
Whole-slide images(WSI) are gigapixel high-resolution histopathology images. Traditional analysis procedures could not work efficiently if directly applied to WSIs. Most successful solutions adopted **patch-based** paradigm. Below is an example of [prostatectomy whole–slide images and patches](https://www.researchgate.net/publication/321501785_Patch-Based_Techniques_in_Medical_Imaging_Third_International_Workshop_Patch-MI_2017_Held_in_Conjunction_with_MICCAI_2017_Quebec_City_QC_Canada_September_14_2017_Proceedings).
<div align="center">
  <img src="https://www.researchgate.net/profile/Henning_Mueller2/publication/319389848/figure/fig1/AS:538753372102661@1505460221339/Sample-prostatectomy-whole-slide-images-and-patches-Far-right-WSI-and-patches.png"><br><br>
</div>

----------------------------------

### Overview <br>
This repo currently contains codes for patch extraction (from WSI) and will be updated constantly. :) (Codes for classification and segmentation will be added when they are ready).

#### Patch extraction <br>
There are several tricky parts when extracting patches from WSIs:
1. **Memory limit.** <br>
The RAM size of our lab is 31 GB, and it could hardly hold a level0 WSI. So be careful when loading the whole image.<br>
It is also helpful to use **del** and **gc.collect()** to free up memory.<br>And in order to process level0/1/2 WSIs, we need to split the original image up. 
2. **Coordinates scaling level/reference frame.** <br>
The read_region() method in [OpenSlide](http://openslide.org/api/python/) processes WSIs in level 0 reference frame. So
necessary transformation is needed when we crop patches from WSIs using read_region() method.
3. **Shape difference between Pillow Image object and NumPy arrays.** <br>
numpy.asarray() / numpy.array() would switch the position of WIDTH and HEIGHT in shape, and vice versa. 
If an Image object' shape is (WIDTH, HEIGHT, CHANNEL), the shape will be (HEIGHT, WIDTH, CHANNEL) after the np.asarray() transformation.
4. **Magnification level choice** <br>
Below is an patch-extraction example (performed on one sample from [Camelyon 2017 dataset](https://camelyon17.grand-challenge.org/data/)). Red boxes are selected patches and green ones annotated tumor areas. As we can see, when we extract 500 x 500 patches from a WSI in **level3** scale, the portion of tumor areas are too small, which means discriminative information could be significantly diluted if we use all these selected patches to train CNN. <br>This urges us to use smaller magnification level (higher resolution scale).
![slide09-1](http://119.29.151.114/images/level3_patche_extraction.jpeg)
