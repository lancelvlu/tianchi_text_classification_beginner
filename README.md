先说一下最终结果，使用word2vec+textCNN+5fold，test_a达到0.961，test_b达到0.963。

主要分享TextCNN代码，包含实验代码、实验线下结果、实验test_a以及实验训练时间供大家学习参考。


## 代码使用说明
1. textcnn_experiment.ipynb为本次实验的主要代码
2. 如果要使用word2vec向量，请先使用word2vec_helper.ipynb生成数据集的词向量备用
3. 如果需要截取文章的前x和后y，请使用cut_helper.ipynb生成cut_text.csv
4. 如果需要在训练后调用模型预测新的文本请使用predict_example.ipynb调试

## TextCNN模型实验
此类实验中均为5fold validation，线下结果为f1-score (marco)，训练的设备为单卡V100（16GB）

模型 | 线下 | 线上(a榜) | 耗时(min)
-----|-----|-----|-----
textCNN（filters：3、4、5、10）+ no pre-train embedding + truncating=pre + padding=pre + max_epoch=5| 0.89（oof）| | 
textCNN（filters：3、4、5、10）+ skip-gram_d300 + max_len=300 + truncating=pre + padding=pre + max_epoch=5| 0.91(oof) | | 
textCNN（filters：3、4、5、10）+ skip-gram_d300 + max_len=300 + truncating=post + padding=post + max_epoch=5| 0.94(oof) | | 
textCNN（filters：3、4、5、10）+ skip-gram_d300 + max_len=3000 + truncating=pre + padding=pre + max_epoch=50&early_stop| 0.96(oof) | 0.9598 | 1324.973
textCNN（filters：3、4、5、10）+ skip-gram_d300 + max_len=3000 + truncating=post + padding=post + max_epoch=50&early_stop| 0.96(oof) | 0.9607 | 1142.641
textCNN（filters：3、4、5、10）+ skip-gram_d300 + max_len=3000 + truncating=post1500&pre1500 + padding=post  + max_epoch=50&early_stop| 0.96(oof) | 0.9611 | 1285.698
textCNN（filters：3、4、5、10）+ averagepooling&maxpooling + skip-gram_d300 + max_len=3000 + truncating=post1500&pre1500 + padding=post  + max_epoch=50&early_stop| 0.96(oof) |  | 1327.282

a、b榜最好的结果参数组合均为textCNN（filters=3、4、5、10）+ skip-gram_d300 + max_len=3000 + truncating=post1500&pre1500 + padding=post  + max_epoch=50&early_stop

## 下一步计划
- [ ] 参考top方案学习bert的预训练模型和fine-tuning文本分类任务
- [ ] 整理完善countervector+LR、countervector+lightgbm、tfidf+lightgbm、fasttext等经典模型实验