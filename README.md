# Implement [Mask R-CNN benchmark](https://github.com/facebookresearch/maskrcnn-benchmark) with [YCB Video dataset](https://www.ycbbenchmarks.com/)[[Download](https://rse-lab.cs.washington.edu/projects/posecnn/)]
## Install all the dependences required by [Mask R-CNN benchmark](https://github.com/facebookresearch/maskrcnn-benchmark)
- Create the dir ./output for storing the trained models, which is ignored in git.
## Generate the annotation with COCO format
- Run [./video_data_annotations_generator.py](video_data_annotations_generator.py) to generates the real data
- If you would like to generate annotation of synthetic data, run [./data_syn_annotations_generator.py](data_syn_annotations_generator.py) 
- **Before you run the annotation generator, do change the corresponding path to your dataset**
- **Save the annotations in "/path to your dataset/annotations", in which you need create the dir /path to your dataset/annotations**
## Train Mask RCNN on YCB Dataset

   **1) Build [YCB dataset class](maskrcnn_benchmark/data/datasets/ycb_video.py)** it is done already, [here to see how to build](https://github.com/roobooot/maskrcnn-benchmark/commit/98a385e671492fff99dc3bf63eb616e08ed570b8)
   
      1- root = path/to/YCB_Dataset/data, ann = path/to/annotation.json
      
      2- from image_id to file_name
         1> img = Image.open(os.path.join(self.root,     path)).convert('RGB')
         
   **2) Train based on config “e2e_mask_rcnn_X_101_32x8d_FPN_1x.yaml”**
  This is the one I tried to train on.
   **3) Train Instruction:**
   
            1. amend the datset in config file (e2e_mask_rcnn_******.yaml), 
            
            DATASETS:
              TRAIN: ("ycb_video_train",)
              TEST: ("ycb_video_val",)

            2. amend the parameters of train solver since there is no enough GPU memory
            
            SOLVER: # reduce the demand of graphi card memory
              BASE_LR: 0.0025   #BASE_LR: 0.01
              WEIGHT_DECAY: 0.0001
              STEPS: (480000, 640000) #STEPS: (120000, 160000)
              MAX_ITER: 720000 #MAX_ITER: 180000
              IMS_PER_BATCH: 2  #IMS_PER_BATCH: 8

            
            3. amend the output directory (maskrcnn_benchmark/config/defaults.py)
            
            4. In ./tools/tran_net_test.py, set default --config-file as the config file that you like to apply.
            
            5. run the ./tools/tran_net_test.py
            
## Inference using trained model

## Evaluation 
Set ./tools/test_net_test.py up by the way same as ./tools/tran_net_test.py
### results:

               1)  configs/e2e_mask_rcnn_X_101_32x8d_FPN_1x.yaml               
                    Task: bbox                    
                    AP, AP50, AP75, APs, APm, APl                    
                    0.6593, 0.9323, 0.7783, 0.0614, 0.6129, 0.6676                    
                    Task: segm                    
                    AP, AP50, AP75, APs, APm, APl                    
                    0.6593, 0.9169, 0.7829, 0.0132, 0.5701, 0.7016
                    
               2)  configs/e2e_mask_rcnn_R_101_FPN_1x.yaml
                    Task: bbox
                    AP, AP50, AP75, APs, APm, APl
                    0.7047, 0.9172, 0.8247, 0.0347, 0.6678, 0.7129
                    Task: segm
                    AP, AP50, AP75, APs, APm, APl
                    0.6799, 0.9046, 0.7879, 0.0306, 0.6053, 0.7169


