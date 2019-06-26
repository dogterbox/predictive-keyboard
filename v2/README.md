# Predictive Keyboard

This project **simple** prediction to next word and correct word on **Thai language**.



## Module

1. keras 2.2.4
2. tensorflow-gpu 1.13.1
3. pythainlp 2.0.5



## Modeling (modeling.ipynb)

#### Step on Modeling

1. Cleansing dataset

2. Word segmentation using [newmm](https://github.com/PyThaiNLP/pythainlp/blob/dev/pythainlp/tokenize/newmm.py) on PyThaiNLP module

3. Train-Test split (0.3)

   ```
   Number of dataset: 562403
   Number of train set: 393682
   Number of test set: 168721
   ```

4. Define model

   1. bi-LSTM layer

      - input shape = (None, 40 time step, 137 unique char)

      - unit cell = 256
   2. Dense layer (output layer)

      - unit = 137 (unique char)
      - activation = sigmoid
   3. Compile
      * optimizer = rmsprop
      * loss function = categorical-crossentropy
      * metrics = accuracy

5. Evaluate: accuracy on test set = **0.7236**



## Make Predictive Keyboard(foo_app.ipynb)

Using **rule-base** work with ML model

#### Step on Predictive

1. Word segmentation input text (**need** same with modeling step) for prediction next char.

   - **Case-1:** The *edit text* is more appropriate.

   - **Case-2:**  The *original input* is more appropriate.

   ```
   [case-1] 
   input >>> 'สุนัข| |เป็น|สัตว์|มี|เขี้ยว|ชนิด|เชื่อง|ที|ถูก|คัดเลือก|ผสม|พั'
   edit >>> 'สุนัข| |เป็น|สัตว์|มี|เขี้ยว|ชนิด|เชื่อง|ที|ถูก|คัดเลือก|ผสมพั'
   
   [case-2] 
   input >>> 'สุนัข| |เป็น|สัตว์|มี|เขี้ยว|ชนิด|เชื่อง|ที|ถูก|คัดเลือก|ผสมพันธุ์'
   edit >>> 'สุนัข| |เป็น|สัตว์|มี|เขี้ยว|ชนิด|เชื่อง|ที|ถูก|คัดเลือกผสมพันธุ์'
   ```

2. Predicting of 2 input (original input and text edit), choose 10 possibility from each result of input and the prediction next character of each possibility until the found character '|'.

3. Applying results (20 result) to analyze according to the words in the **Thai dictionary**. Remove words that are sub-set of other words, because words that are **longer** will be more appropriate.

     ```
     ['หนึ่ง', 'ประการหนึ่ง'] >>> ['ประการหนึ่ง']
     ```

## Result

**Correct result**: get from enter the incorrect text.  
**Predictive result:** get from enter the appropriate text.


```
Text:  ในทางด้านสังคมศาสตร์ปัญหาประการหนึ่
Correct:  ['ประการหนึ่ง']
Predictive:  []

Text:  เจอร้านอาหารอร่อยๆ ถือเป็นความสุข
Correct:  []
Predictive:  ['สร', 'พร', 'ขา']

Text:  คู่มือการติดตามและประเมินผลการจัดกา
Correct:  ['กาล', 'กาย', 'จัดการ']
Predictive:  []

Text:  สุนัข เป็นสัตว์มีเขี้ยวชนิดเชื่องทีถูกคัดเลือกผสมพั
Correct:  ['พัก', 'พัด', 'พัน', 'พัฒนา', 'พัง', 'พัช']
Predictive:  []

Text:  เรียนรู้เกี่ยวกับจิตวิทยาที่อยู่เบื้องหลั
Correct:  ['หลับ', 'หลัด', 'หลัก', 'หลั่น', 'เบื้องหลัง']
Predictive:  []

Text:  'แมว🐱' เป็นสัตว์เลี้ยงลูกด้วยนม อยู่ในตร
Correct:  ['ตรง', 'ตระ', 'ตรู', 'ตรม']
Predictive:  []

Text:  ข้าวมันไก่ เป็นอาหารคาวของไทยและจีน คาดว่าอาหารชนิดนี้ได้รับการเผยแพร่
Correct:  []
Predictive:  ['อม', 'การ', 'งาน', 'วง']
```