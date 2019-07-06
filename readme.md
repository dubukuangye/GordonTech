### 20190706总结

查看模型tag的方法：
>  saved_model_cli show --dir ./Model_stock.label1minLongRts/002008.SZ/model_160_Long/

### 20190630总结

1. 训练分为base（通用模型训练）和stock（单只股票训练）
2. stock训练，文件目录xDev/New_AI_Trainning/stock_model_trainning。步骤为：

 * xDev/New_AI_Trainning/stock_model_trainning/noramlized_dataset_generator.py （这一步可以不用，归一化后的h5下面步骤没有用到）
 * xDev/New_AI_Trainning/stock_model_trainning/DataSetIndexCreator.py
 * xDev/New_AI_Trainning/stock_model_trainning/Stock_Main_trainning.py: 使用了base训练生成的通用模型

> stock训练无法在虚机上进行，会core，怀疑是内存不够导致。

3. base训练

normalized_dataset_genertor（产生modelSet.json用来下面filter过滤优质因子）[dump/preprocessed_dataset/500/factors_std.h5 /dump/preprocessed_dataset/500/modelSet.json]

->

DataSet_filter[dump/preprocessed_dataset/500_filtered/factors_std.h5]

->DataSetIndexCreator_filter[dump/preprocessed_dataset/500_filtered/index_160.h5]

-> MainModelTrainning(use SmartDataSet_ha)

### 20190620环境安装总结
1. python3-devel
2. yum install unixODBC-devel

### 20190501 总结：
1. 运行Base_model_trainning/main.py 依赖:
    *  dump/preprocessed_dataset/new_index_160.h5 依赖：
        - DataSetIndexCreator.py
        - /dump/stocks_200.csv [NOTFOUND] (貌似这是200只股票名称,比如600615.SH,指向dump/stocks/600615.SH.h5)
    *  dump/preprocessed_dataset/factors_std2.h5 [NOTFOUND] (只看到怎么生成factors_std2.h5)

### 20190502 总结:
1. normalized_dataset_genertor.py 产生大数据集的归一化参数。生成：
    * dump/preprocessed_datase/500/factors_std.h5
    * dump/preprocessed_datase/500/modelSet.json
 
 > 5.19更新: dump/stocks/601318SZ.h5可以由xDev/main.py生成。该文件为因子计算结果。
 
2. DataSet_filter.py 过滤优质数据集。
3. DataSetIndexCreator_filter.py。生成:
    * dump/preprocessed_datase/500/index_160.h5
    > 20190529update: 500->500_filterd
4. SmartDataSet.py 对数据切分，乱序。做了如下改动：
    * 第一步生成的factors_std.h5重命名为factors_std2.h5
    * 修改代码：factors_std改为factors(通过安装vitables查看factors_std.h5文件只存在factors数组)

```diff
-        self.__np_factors_data = h5_dataset_file.root.factors_std.read() 
+        self.__np_factors_data = h5_dataset_file.root.factors.read() 
```

5. 运行MainModelTrainning.py得到Model3文件夹, 并输出如下内容：

```
labels ['label1minLongRts']
GPU 1
slice lag 160
create directory for checkpoint
Number of variables: 24309
2548 4589
epoch 0 step  0 0:00:05.786443 train loss:  0.28742 valid_loss:  0.28011 valid rq:  -9104.40381 best loss 10
shuffled
####################################################################################
train loss: 0.054099999368190765 valid loss: 0.04545 valid r2 score:-238.6888
epoch 1 step  4 0:00:19.282144 train loss:  0.0541 valid_loss:  0.04545 valid rq:  -238.6888 best loss 0.04545
shuffled
####################################################################################
train loss: 0.007499999832361937 valid loss: 0.00886 valid r2 score:-8.10092
epoch 2 step  8 0:00:03.483721 train loss:  0.0075 valid_loss:  0.00886 valid rq:  -8.10092 best loss 0.00886
shuffled
epoch 3 step  12 0:00:03.388336 train loss:  0.04843 valid_loss:  0.04941 valid rq:  -282.27739 best loss 0.00886
shuffled
####################################################################################
train loss: 0.006000000052154064 valid loss: 0.0066 valid r2 score:-4.05084
epoch 4 step  16 0:00:03.943791 train loss:  0.006 valid_loss:  0.0066 valid rq:  -4.05084 best loss 0.0066
shuffled
epoch 5 step  20 0:00:03.381469 train loss:  0.02 valid_loss:  0.02019 valid rq:  -46.30195 best loss 0.0066
shuffled
epoch 6 step  24 0:00:03.446354 train loss:  0.01324 valid_loss:  0.01417 valid rq:  -22.30125 best loss 0.0066
shuffled
epoch 7 step  28 0:00:03.392426 train loss:  0.02609 valid_loss:  0.02664 valid rq:  -81.34402 best loss 0.0066
shuffled
Early Stopped
step 32, epochs 8, best_valid loss 0.006598, best_valid_rq -4.050842, best_valid_rmse 0.006597
```

```
(Base_model_trainning) guoqd@ubuntu:~/workspace/20190422deeplearning/xDev/New_AI_Trainning/Base_model_trainning$ ls -lrt ../Model3/*/*

-rw-rw-r-- 1 guoqd guoqd 4902924 May  2 02:15 ../Model3/model_160_Long/saved_model.pb
-rw-rw-r-- 1 guoqd guoqd     239 May  2 02:15 ../Model3/model_160_Long/Loss.json

../Model3/checkpoint/model_160_Long:
total 23624
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:13 model.ckpt-13.index
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:13 model.ckpt-13.data-00000-of-00001
-rw-rw-r-- 1 guoqd guoqd 2557194 May  2 02:13 model.ckpt-13.meta
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:13 model.ckpt-17.data-00000-of-00001
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:13 model.ckpt-17.index
-rw-rw-r-- 1 guoqd guoqd 2557194 May  2 02:13 model.ckpt-17.meta
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:13 model.ckpt-21.index
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:13 model.ckpt-21.data-00000-of-00001
-rw-rw-r-- 1 guoqd guoqd 2557194 May  2 02:13 model.ckpt-21.meta
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:14 model.ckpt-25.data-00000-of-00001
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:14 model.ckpt-25.index
-rw-rw-r-- 1 guoqd guoqd 2557194 May  2 02:14 model.ckpt-25.meta
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:14 model.ckpt-29.index
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:14 model.ckpt-29.data-00000-of-00001
-rw-rw-r-- 1 guoqd guoqd     259 May  2 02:14 checkpoint
-rw-rw-r-- 1 guoqd guoqd 2557194 May  2 02:14 model.ckpt-29.meta

../Model3/model_160_Long/variables:
total 2224
-rw-rw-r-- 1 guoqd guoqd    3857 May  2 02:15 variables.index
-rw-rw-r-- 1 guoqd guoqd 2273156 May  2 02:15 variables.data-00000-of-00001
```

20190616:

1. 因子多了一个 37？
2. csv和h5结果对不上
3. json配置参数确认
4. 与之前600615.h5比对 
