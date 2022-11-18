## Report：LSTM Model for Audio Recognition

When I firstly change the code and run it successfully, the result was shown as following:

Epoch = 400, training on
**Hardware:** Lenovo Thinkpad Gen5 without GPU
**Software:** Windows 10
**Python:** 3.9
**Date:** Nov 2022

---

**Training time**: 204s **Model size**:1260KB

|            | **Loss** | **Accuracy** |
| ---------- | -------- | ------------ |
| Training   | `0.03`   | `1.00`       |
| Validation | `0.07`   | `1.00`       |
| Testing    | `0.36`   | `0.92`       |

Saying that the accuracy is good enough for most recognition. Even overfitting may occur. Training time is not long because the amount of data is not big.
Later, I try to improve my model. Knowing that we can add more hidden layer and some features may be useless in the recognition, I took two actions:

- A 33-unit LSTM layer is added for imporving accuracy

```
_________________________________________________________________
 Layer (type)                Output Shape              Param #
=================================================================
 lstm_2 (LSTM)               (None, 128, 128)          73216

 lstm_3 (LSTM)               (None, 128, 33)           21384

 lstm_4 (LSTM)               (None, 33)                8844

 dense_1 (Dense)             (None, 5)                 170

=================================================================
Total params: 103,614
Trainable params: 103,614
Non-trainable params: 0
```

**Training time**: 281s **Model size**:1384KB
| | **Loss** | **Accuracy** |
| ----- | ---- | ----- |
| Training | `0.05` | `1.00` |
| Validation | `0.23` | `0.88` |
| Testing | `0.06` | `1.00` |

- LSTM's units are set as 14 (just extract MFCC features) to decrease parameters.

```
_________________________________________________________________
 Layer (type)                Output Shape              Param #
=================================================================
 lstm (LSTM)                 (None, 128, 128)          73216

 lstm_1 (LSTM)               (None, 14)                8008

 dense (Dense)               (None, 5)                 75

=================================================================
Total params: 81,299
Trainable params: 81,299
Non-trainable params: 0
```

**Training time**: 261s **Model size**:996KB (less parameters)
| | **Loss** | **Accuracy** |
| ----- | ---- | ----- |
| Training | `0.01` | `1.00` |
| Validation | `0.08` | `0.92` |
| Testing | `0.01` | `1.00` |

### Conclusion

Adding a layer in the model seems doesn't work. It takes higher value in loss function in 400 epochs. So sometimes more layer shows worse performance cause it's hard to be trained perfectly.
On the other hand, decreasing features can speed up model training. During the training, I find that training is actually done in 250th epoch. Also, less parameters cause less memory use.
As for training time, it's hard to measure which is better because the difference isn't obvious.

---

### Test trained LSTM model

In the `./weights/` you can find 3 trained models weights and model architectures.

```
lstm_genre_classifier_lstm.h5
lstm_genre_classifier_lstm_addLayer.h5
lstm_genre_classifier_lstm_decrease_feature.h5
lstm_genre_classifier_lstm.json
lstm_genre_classifier_lstm_addLayer.json
lstm_genre_classifier_lstm_decrease_feature.json
```

To test the model on your custom audio file, run

     python3 predict_example.py path/to/custom/file.au

or to test the model on our custom files, run

     python3 predict_example.py audio/James.021.au

:heavy_exclamation_mark: If you want to test the model that has decreased feature, you need to change the shape of test data to (128,14) in `predict_example.py`
