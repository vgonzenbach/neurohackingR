---
output: html_document
---



# NIfTI

NifTI (Neuroimaging Informatics Tehcnology Image)
-Standardized representation of images
-Most commonly used tpy eof analytic file
-Developed to facilitate cross-platform, cross-software interpretability
-3-dimensional array: stacking individual slices on top of each other
-DICOM: one sheet of paper; NIfTI: stack of papers

## From DICOM to NIfTI

-DICOM to NIFTI using the `dicom2nifti` function in the `oro.dicom` package
-The `nifti` object becomes an R object
-After saving a file to a `nifti` file it can be used without R

Let's read all DICOM images:


```r
library(oro.dicom)
```

```
## oro.dicom 0.5.3
```

```r
all_slices_T1 = readDICOM("Neurohacking_data/BRAINIX/DICOM/T1")
dim(all_slices_T1$img[[11]])
```

```
## [1] 512 512
```

```r
hdr = all_slices_T1$hdr[[11]]
hdr[hdr$name == "PixelSpacing", "value"]
```

```
## [1] "0.46875 0.46875"
```

Now lets's convert them to NIFTI:

```r
nii_T1 = dicom2nifti(all_slices_T1)
```

```
## Warning in create3D(dcm, ...): ImagePositionPatient is moving in more than one
## dimension.
```

```
## Warning in swapDimension(img, dcm): Oblique acquisition in
## ImageOrientationPatient (hope for the best).
```

```
## Warning in is.axial(imageOrientationPatient): Oblique acquisition in
## ImageOrientationPatient.
```

```
## Warning in is.coronal(imageOrientationPatient): Oblique acquisition in
## ImageOrientationPatient.
```

```
## Warning in is.sagittal(imageOrientationPatient): Oblique acquisition in
## ImageOrientationPatient.
```

```r
d = dim(nii_T1); d; class(nii_T1)
```

```
## [1] 512 512  22
```

```
## [1] "nifti"
## attr(,"package")
## [1] "oro.nifti"
```

And plot one slice:

```r
# Plot the 11th slice
image(1:d[1], 1:d[2], nii_T1[,,11], col=gray(0:64/64), xlab="", ylab="")
```

<img src="02-niftilecture_files/figure-html/unnamed-chunk-3-1.png" width="672" />

## Write and Read NIfTI files

- Use the `writeNIfTI, readNIfTI` functions in the `oro.nifti` package
- Default for `writeNIfTI` is to save the compressed NIfTI files


```r
library(oro.nifti)
```

```
## oro.nifti 0.10.1
```

```r
dir_name = "Neurohacking_data/BRAINIX/NIfTI/"
fname = "Output_3D_File"
writeNIfTI(nim=nii_T1, file = paste0(dir_name, fname))
```

And check that the file si there after saving

```r
list.files(dir_name, pattern = fname)
```

```
## [1] "Output_3D_File.nii.gz"
```

Now lets read a different file:


```r
list.files(dir_name, pattern="T") #Check that file is in directory
```

```
## [1] "T1.nii.gz" "T2.nii.gz"
```

```r
nii_T2=readNIfTI(paste0(dir_name,"T2.nii.gz"), reorient=FALSE)
dim(nii_T2)
```

```
## [1] 512 512  22
```

## Compressed Image Files

-A non-compressed file can be obtained using the argument `gzipped=FALSE` in the function `writeNIfTI`






