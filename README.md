Asan medical center medical image defacer(de-identification)
# De-facer: De-identifier reconstructable facial information in Medical image (CT, MRI) 

## Results 
![deface_](https://user-images.githubusercontent.com/49013508/93426650-6eef2b80-f8f7-11ea-926d-dcf81076b3fd.png)  
Removing facial feature : 3D rendering sample 
  
![image](https://user-images.githubusercontent.com/49013508/97131100-95ba4080-1786-11eb-92fe-1dfebf54b3e1.png)  

Face recognition test with Detection_01 model from Microsoft Azure's AI service. The original image was recognized the facial features, but the defaced one was not recognized.  


<br/>

<br/>

__To protect individually identifiable health information through permitting only certain uses, metadata in image headers and reconstructable personal data like 3D facial image must be de-identified.__ 

High resolution three-dimensional medical images that include the face can be exposed their faces as the photo level and there is a risk of infringement of personal information when sharing data. According to U.S. Health Insurance Portability and Accountability Act's Privacy Rule (HIPAA), it is included in "full face photographic images and any comparable images"as direct identifiers and is considered as protected health information. General Data Protection Regulation (GDPR) also categorizes facial images as a biometric data. GDPR stipulates that special restrictions should be placed on the processing of biometric data.   



This source code contains an algorithm for detecting and removing identifiable facial features (eyes, nose, ears and mouth).  
3D Unet-based neural network were used to detect eyes, nose and ears in the input medical image
Creating bounding box at the largest segmented points that location of each predicted feature and __only removing surface facial features to avoid impinging on region of interest__ such as brain.     
this will be help with the secondary use of medical data by obscuring it locally to minimize information loss.  
The Nifti version includes both training and application, and the Dicom version uses a model that has already been trained to create a de-identified file.  
  
Users can create anonymized images that deface the desired parts of among the eyes, nose, and ears, which helps provide data for secondary research without violating relevant regulations.   
  &nbsp;
- Selective de-identification of facial features (eyes, nose, and ears)
- Removing metadata with privacy in dicom header 
- Supporting multi medical image types (nifti, dicom)  

### Also __In the dicom format__, various personal information is recorded in the header.  
This program handles not only the image but also the sensitive header information.
- (0008, 0012) Instance Creation Date
- (0008, 0013) Instance Creation Time 
- (0008, 0020) Study Date      
- (0008, 0030) Study Time
- (0008, 0021) Series Date
- (0008, 0031) Series Time
- (0008, 0022) Acquisition Date 
- (0008, 0032) Acquisition TIme
- (0008, 0023) Content Date        
- (0008, 0033) Content Time                              
- (0008, 0080) Institution Name      
- (0008,0081) Institution Address
- (0008, 0090) Referring Physician's Name  
- (0008,1050) Performing Physician's Name Attribute
- (0008, 1070) Operators' Name          
- (0010, 0010) Patient's Name                
- (0010, 0020) Patient ID                  
- (0010, 0030) Patient's Birth Date             
- (0010, 0040) Patient's Sex                   
- (0010, 1010) Patient's Age    

  
## prerequisites
- python==3.7
- tensorflow == 1.14.0 (in 2.X version, you should correct 'keras' to 'tensorflow.keras' at import keras module) 
- Keras== 2.2.4
## Comping up 
- integrating file transfer function with de-id 
- Web-based service (closed beta service in progress)
- Supporting CT images (now service at Asan medical center)

  
# Model training 
## Data
AIBL neuroimage data Nifty file from ADNI and OASIS.  
FOV 240mm * 256mm * 160mm image T1w MPRage  
training set : 144, validation set : 48, test set : 48, external validation set : 20 (OASIS-3 data, FOV 176mm x 256mm x 256mm)
+ 20 Asan Medical Center 3D 1T image, and CT image

## Labeling
![gitPicture1](https://user-images.githubusercontent.com/49013508/78311618-4fa05400-758c-11ea-8f22-268abaf287e3.png)
  
<br/>  
  
__Version 2__  
  
![label_1604994200 0363073](https://user-images.githubusercontent.com/49013508/99653352-3e18a780-2a9c-11eb-8cd6-5657082288ce.png)  
  
  
labeling sample. the spherical point at the facial feature.  

## Data augmentation
Gaussian noise filter  
Rotataion, shift, filp, shear on each axis randomly.  
Randomly zoom in or zoom out in the range of [0.9, 1.10] 
randomly transposing between axes

I modified a module keras.preprocessing.image and use ImageDataGenerator function to do data augmentation.  
  
See keras_image_preprocessing1.py for detail.
                                        
## Model structure
![image](https://user-images.githubusercontent.com/49013508/79738609-eb291700-8337-11ea-80ad-3d32767d4e55.png)

  
I modify attention gated U-net [1] .  
Convolution block + SENet consists of Convolution(3x3) - Instance Normalization (because batch size is 1) - ReLU - Convolution(3x3) with residual SE net - Instance Normalization - ReLU  Segmentation map is made through 1x1 convoultion with number of channels equal to the number of channels of one hot label data. and activate sofmax.  
  
[1] Schlemper, J., Oktay, O., Schaap, M., Heinrich, M., Kainz, B., Glocker, B., & Rueckert, D. (2019). Attention gated networks: Learning to leverage salient regions in medical images. Medical image analysis, 53, 197-207.
&nbsp;


## Results
![gitPicture2](https://user-images.githubusercontent.com/49013508/78311624-5202ae00-758c-11ea-855b-8a9f2902c70e.png)  
  
<br/>  
 
__Version 2__  
  
  ![results1](https://user-images.githubusercontent.com/49013508/93437550-145dcb80-f907-11ea-817a-8c2432f41c95.png)
Removing facial feature sample -ITK sanp viewer 
  
  <br/>  
  

### CT version test  
  
![CT_test002](https://user-images.githubusercontent.com/49013508/99750834-6c41ca00-2b24-11eb-9bbb-76268e64f8d9.png)  
  
![Ct_test01](https://user-images.githubusercontent.com/49013508/99750847-72d04180-2b24-11eb-8ad0-488a40bed9f7.png)

<br/>  

## paper (accept in JMIR)
De-ientification of Facial Features in Magnetic Resonance Images Using Deep-Learning Technology
-yeonuk jeong, Soyoung Yoo,, Young-Hak Kim, Woo Hyun Shim 
