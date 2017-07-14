Configuring the Object Detection Training Pipeline

0. 概述

配置文件主要分为5部分:

（1）. model configuration. 
（2）. train_config
（3）. eval_config
（4）. train_input_config
（5）. eval_input_config

示例如下：
model {
(... Add model config here...)
}

train_config : {
(... Add train_config here...)
}

train_input_reader: {
(... Add train_input configuration here...)
}

eval_config: {
}

eval_input_reader: {
(... Add eval_input configuration here...)
}

下面我们分步骤介绍。

1. 设置模型参数（model configuration）
object_detection/samples/model_configs 文件夹中有配置文件的示例，可以将文件中的model部分拷贝到个人文件中，但需要根据自己的需求修改num_classes。

2. 设置输入
label map表明了id和类的名字之间的关系。具体配置示例如下：

tf_record_input_reader {
  input_path: "/usr/home/username/data/train.record"
}
label_map_path: "/usr/home/username/data/label_map.pbtxt"

3. 配置训练过程（train_config）
主要包括三项内容：参数模型初始化、输入预处理、模型参数

（1）参数模型初始化
该步骤为可选，主要用于保存训练过程中的断点。
（2）输入预处理
该步骤也为可选项目，主要用于数据增强。
（3）模型参数
主要用于设置模型的学习率。

具体示例如下：

batch_size: 1
optimizer {
  momentum_optimizer: {
    learning_rate: {
      manual_step_learning_rate {
        initial_learning_rate: 0.0002
        schedule {
          step: 0
          learning_rate: .0002
        }
        schedule {
          step: 900000
          learning_rate: .00002
        }
        schedule {
          step: 1200000
          learning_rate: .000002
        }
      }
    }
    momentum_optimizer_value: 0.9
  }
  use_moving_average: false
}
fine_tune_checkpoint: "/usr/home/username/tmp/model.ckpt-#####"
from_detection_checkpoint: true
gradient_clipping_by_norm: 10.0
data_augmentation_options {
  random_horizontal_flip {
  }
}

4. 设置评估过程
目前的评估设置是根据PASCAL VOC设置的，一般无需修改。

Currently evaluation is fixed to generating metrics as defined by the PASCAL VOC challenge. The parameters for eval_config are set to reasonable defaults and typically do not need to be configured.
