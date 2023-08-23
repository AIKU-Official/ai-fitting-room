###########################
    Dress Code Dataset
###########################

Dress Code is an image-based virtual try-on dataset with multi-category garments and high-resolution images.

The main folder contains:
1. One folder for each category (e.g. dresses, lower_body, upper_body).
2. Annotation txt files:
   - train_pairs.txt contains paired model-garment annotation couples for training on the entire dataset		(model | garment)
   - test_pairs_paired.txt contains model-garment pairs of the paired test set          						(model | garment)
   - test_pairs_unpaired.txt contains model-garment pairs of the unpaired test set      						(model | garment)
   - multigarment_test_triplets.txt contains the image triples of the multi-garment test set    				(model | garment upper | garment lower)

In each category folder, there are five subfolders:
1. images:    	contains both model and garment images
2. keypoints: 	contains json keypoints [x, y, score, keypoint_number].
3. skeletons:  	contains jpg skeleton images
4. label_maps: 	contains 1 channel label map of model images
5. dense:     	contains .npz (uv) and .png (labels) files extracted using DensePose

Each category folder also contains the train/test files to train and test the model only on a specific categroy.


###########################
Keypoints
###########################

Each image has 18 keypoints. Due to the fact that keypoint are automatically generated, some keypoint might be missing. In that case a record [-1, -1, 0.0, -1] represent a keypoint not found.
Note that the keypoints have been computed on 512x384 (HxW) images.

keypoints_map={
    0.0: "nose",
    1.0: "neck",
    2.0: "right shoulder",
    3.0: "right elbow",
    4.0: "right wrist",
    5.0: "left shoulder",
    6.0: "left elbow",
    7.0: "left wrist",
    8.0: "right hip",
    9.0: "right knee",
    10.0: "right ankle",
    11.0: "left hip",
    12.0: "left knee",
    13.0: "left ankle",
    14.0: "right eye",
    15.0: "left eye",
    16.0: "right ear",
    17.0: "left ear"
}


###########################
Label Maps
###########################

Each label map is composed of 18 classes according to the following dict

label_map={
    "background": 0,
    "hat": 1,
    "hair": 2,
    "sunglasses": 3,
    "upper_clothes": 4,
    "skirt": 5,
    "pants": 6,
    "dress": 7,
    "belt": 8,
    "left_shoe": 9,
    "right_shoe": 10,
    "head": 11,
    "left_leg": 12,
    "right_leg": 13,
    "left_arm": 14,
    "right_arm": 15,
    "bag": 16,
    "scarf": 17,
}


############################
Additional Info
############################

| NAME         | Symbol | Extension |
|--------------|--------|-----------| 
| model        |   _0   |    .jpg   |
| garment      |   _1   |    .jpg   |
| keypoints    |   _2   |    .json  |
| skeleton     |   _3   |    .jpg   |
| label map    |   _4   |    .png   |
| dense uv     |   _5   |    .npz   |
| dense labels |   _5   |    .jpg   |


############################
Contacts
############################

If you have any general doubt about our dataset, drop us an e-mail at davide.morelli@unimore.it or marcella.cornia@unimore.it.


0      = Background
1, 2   = Torso
3      = Right Hand
4      = Left Hand
5      = Right Foot
6      = Left Foot
7, 9   = Upper Leg Right
8, 10  = Upper Leg Left
11, 13 = Lower Leg Right
12, 14 = Lower Leg Left
15, 17 = Upper Arm Left
16, 18 = Upper Arm Right
19, 21 = Lower Arm Left
20, 22 = Lower Arm Right
23, 24 = Head