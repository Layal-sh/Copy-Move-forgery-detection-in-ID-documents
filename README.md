# Copy-Move forgery detection in ID-documents
In this project, we used python in order to implement a Convolutional Neural Network (CNN) for the purpose of extracting features in the problem of copy-move forgery detection in id documents. 

 
## Introduction: 
In our increasingly digital world, where online transactions and identity verification are the norm, the need for robust and secure identity document authentication has never been greater. Governments worldwide are enacting stricter regulations to combat fraud and protect their citizens from identity theft.
The stakes are high for businesses offering digital services, from online banking to e-commerce. Fraudulent activities like unpaid invoices and money laundering often rely on fake or stolen IDs, making it crucial to verify the legitimacy of identification documents like passports, national ID cards, and driver's licenses.
This verification process, a cornerstone of digital security and online onboarding, presents a significant challenge. It requires striking a balance between user convenience and the need for rigorous security measures. However, the payoff is immense, as successful identity verification helps prevent fraud, protect customers, and build trust in the digital ecosystem.
In response to the growing threat of identity document forgery, governments have equipped official IDs with an array of sophisticated security features. These measures, which include optical variable devices (OVDs) and holograms, aim to make counterfeiting and forgery virtually impossible.
OVDs, for example, display dynamic patterns, logos, and fonts that change depending on the light and viewing angle. This dynamic nature makes them exceptionally difficult to replicate.
In addition to OVDs, a variety of other security features can be found on modern identity documents. Some of which are Guilloche, anti-scan patterns, watermarks, microtypes and many others. These security features, often difficult to reproduce accurately, act as a powerful deterrent to counterfeiters and forgers. They provide an additional layer of protection for the integrity of identification documents and the individuals they represent.
Therefore, a potential solution to combat the rising threat of identity document forgery lies in developing an automated, rapid, and in-depth verification system. This system would leverage deep learning algorithms to scrutinize the intricate security features embedded within ID documents, swiftly identifying any signs of tampering or counterfeiting.
The integrity of identification documents is paramount. Whether it’s for border control, banking, or online transactions, ensuring the authenticity of these documents is crucial. However, a subtle yet pervasive form of document tampering known as Copy-move forgery – the sneaky act of duplicating and pasting parts of an image – is becoming increasingly sophisticated in the world of fake IDs. This blog post delves into the world of copy-move forgery in ID documents, exploring the methods used to detect it and presenting a novel approach to tackle this growing problem.
 
## Problem Statement: 
Copy-move forgery involves copying a part of an image and pasting it within the same image, often to hide or alter important information. Existing solutions have focused on detecting copy-move forgery through visual analysis, but their effectiveness has been limited, particularly in the context of ID documents where the forged elements may be subtle and well-integrated. While these methods have been somewhat effective, they often fall short in terms of accuracy and computational efficiency. 
Our approach leverages advanced machine learning algorithms.
By developing a robust and adaptable system, we seek to provide a more comprehensive solution that can reliably identify and mitigate copy-move forgery in identity documents.
Methodology: 
To tackle this problem, we employed a multi-faceted methodology:
A. Data Collection and Preprocessing: We used the MIDV2020 dataset that contained genuine ID documents. The MIDV-2020 dataset consists of 1000 video clips, 2000 scanned images, and 1000 photos of 1000 unique dummy (template) identity documents with their annotations to read the ground-truths. These samples comprise identity documents and passports for 10 different countries. Specifically, the 1000 unique dummy identity documents and the 1000 upper-right scanned identity documents are selected to represent the genuine dataset. While, the forged dataset is generated from the genuine dataset. A copy-move tampering operation of specific zones  in the genuine identity document of a specific country is applied, in our case the Albanian ID. These zones are selected as follows: For each image, the zones where textual information resides are selected as the candidate zones for applying copy-move operations to generate a forged dataset.
![Image Description](/Images/forged example.png.jpg)

Fig. 1 Genuine ID image with its forged replica
Fig. 1 illustrates the forged ID generated from the real ID where the date of issue and expiry date were selected as a set of zones for applying a copy-move operation on, in this case the date of birth was copied and moved onto them.
The reason why we decided to apply this methodology to create the forged dataset is based on the fact that forged documents are often created with copy-move operations. Fraudsters usually hide their modifications by reusing some patterns of the genuine document. For example, if the expiry date of the ID was expired, fraudsters might copy-move other dates onto it to override it. The copy-move operation will create irregularities in the global pattern that our approach wants to detect.
The data was then preprocessed to enhance feature extraction and model training.
 
B. Analytical Strategies:
Our forgery detection system follows a carefully designed process to ensure accuracy and security:
Is It an ID? Our first step is determining whether the uploaded image is an identification document. We employ a neural network trained on a vast dataset of ID and non-ID photos to make this initial determination. 

Fig. 2 Specifying the true class and its predicted class whether an ID or not
Albanian or not? Next, recognizing that we're specifically targeting Albanian ID documents, we use a second model trained on a dataset of Albanian and non-Albanian IDs to filter out irrelevant documents.


Fig. 3 Specifying the true class and its predicted class whether an albanian ID or other ID
Is it Forged? Once we've confirmed that the image is an Albanian ID, our specialized forgery detection model takes over. This model combines the power of ResNet50, a well-established architecture for image analysis, with additional layers that we've fine-tuned for optimal accuracy. It meticulously examines every pixel, searching for subtle signs of tampering, inconsistencies, or duplicated elements.
 
Fig. 4 Recognizing whether the ID was forged or not

Fig. 5 Model code snippet
The Forgery Deep Dive: After confirming the ID was forged, we've built a CNN model from scratch where its core is built upon Conv2D layers.These are the workhorses of image processing, designed to detect features (edges, textures, etc.) within an image. Along with using  MaxPooling2D layers, that are like downsampling tools. They shrink the image while retaining the most important information. This makes the model more efficient and helps it focus on the bigger picture. As we move through the model, the number of filters in the Conv2D layers increases. This means the model is progressively learning more complex and abstract features.
At the deepest point, the model has 256 filters.  Then we used the UpSampling2D layers that reverse the downsampling from before. They increase the image size back to its original dimensions. The final touch lies in the last layer which is a special Conv2D layer with just one filter and a sigmoid activation function. This is often used when the model needs to produce an output image where each pixel represents a probability (like in image segmentation tasks). While our model is adept at identifying forged areas in ID documents, how do we measure its success? Accuracy alone isn't enough, especially in cases where the forged regions might be small compared to the overall image. That's where the Dice coefficient comes in.
Dice coefficient is a way to gauge how well our model's predicted forgery mask overlaps with the actual, ground truth mask (the one we created manually to label the forged areas). The Dice coefficient ranges from 0 (no overlap) to 1 (perfect overlap). We don't just use the Dice coefficient for evaluation; we also use it to guide our model's learning process. The Dice loss is a function that measures the disagreement between the predicted mask and the ground truth mask.
During training, the model adjusts its parameters to minimize this loss, effectively learning to improve its overlap with the true forged regions. It's like a feedback loop that helps the model become a more accurate forgery detector over time. 
Fig. 6 The original ID with its predicted mask containing the predicted segments
By dividing our process into these distinct stages, we enhance efficiency and ensure that our resources are focused on the most relevant images. The integration of ResNet50, along with our custom modifications, empowers our system to detect even the most sophisticated forgery techniques.
 
## Results and Findings: 
Our extensive testing and evaluation of the proposed solution have yielded promising results. The model has demonstrated a high degree of accuracy in identifying copy-move forgery, with a detection rate of over 98% on our test dataset. Furthermore, the system has proven to be resilient to various forms of image manipulation, including color adjustments, scaling, and rotation, ensuring its effectiveness in real-world scenarios.
Notably, the system's performance has surpassed that of existing copy-move forgery detection techniques, particularly in the context of ID documents, where the forged elements can be strategically integrated to evade detection. This demonstrates the potential of machine learning to bolster ID verification systems. You can find the code here.
## Final Thoughts: 
The battle against fake IDs is ongoing, but advancements in machine learning offer a powerful weapon in our arsenal. By integrating our detection model into existing verification processes, we can significantly enhance security and reduce the risks associated with identity fraud.
However, our work isn't done. Future research will focus on refining the model, exploring additional features that can be used to detect copy-move forgery along with extracting the forged segment in the image, and staying one step ahead of the forgers who are constantly developing new techniques. The goal is to create an even more robust and accurate solution to protect identities and maintain the integrity of our identification systems.
By staying ahead of the curve, we can empower organizations and individuals to trust the integrity of the identity documents they rely on, fostering a safer and more transparent digital landscape.
 


