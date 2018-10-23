---  
category : Data Analysis
title : Kaggle Toxic Classification (2) Preprocessing 
tags : [Kaggle, toxic classification, preprocessing, Data Analysis]
--- 

Toxic Comment Classification Kaggle 대회 : [링크](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge)  

지난 포스트  
(1) [Toxic Classification EDA](https://inspiringpeople.github.io/data%20analysis/Toxic_EDA/) 

train / test data에 대해 Text 전처리 하는 부분이다.  
기본적인 소문자화, 숫자 제거, 축약어 풀기 등 외에도 중복 알파벳, 중복 단어 제거 등의 로직이 있다.

## Definition (Library & Functions)


```python
import numpy as np 
import pandas as pd

import os
print(os.listdir("./input"))

import pandas as pd
import re

# Replace all numeric with 'n'
replace_numbers = re.compile(r'\d+', re.IGNORECASE)

COMMENT_COL = 'comment_text'
ID_COL = 'id'
input_dir = './input/'
output_dir = './input/'
```

    ['sample_submission.csv', 'test_labels.csv', '.ipynb_checkpoints', 'test.csv', 'imagesforkernal', 'train.csv']



```python
# redundancy words and their right formats
redundancy_rightFormat = {
    'ckckck': 'cock',
    'fuckfuck': 'fuck',
    'lolol': 'lol',
    'lollol': 'lol',
    'pussyfuck':'fuck',
    'gaygay': 'gay',
    'haha': 'ha',
    'sucksuck': 'suck'}

redundancy = set(redundancy_rightFormat.keys())

# all the words below are included in glove dictionary
# combine these toxic indicators with 'CommProcess.revise_triple_and_more_letters'
toxic_indicator_words = [
    'fuck', 'fucking', 'fucked', 'fuckin', 'fucka', 'fucker', 'fucks', 'fuckers',
    'fck', 'fcking', 'fcked', 'fckin', 'fcker', 'fcks',
    'fuk', 'fuking', 'fuked', 'fukin', 'fuker', 'fuks', 'fukers',
    'fk', 'fking', 'fked', 'fkin', 'fker', 'fks',
    'shit', 'shitty', 'shite',
    'stupid', 'stupids',
    'idiot', 'idiots',
    'suck', 'sucker', 'sucks', 'sucka', 'sucked', 'sucking',
    'ass', 'asses', 'asshole', 'assholes', 'ashole', 'asholes',
    'gay', 'gays',
    'niga', 'nigga', 'nigar', 'niggar', 'niger', 'nigger',
    'monster', 'monsters',
    'loser', 'losers',
    'nazi', 'nazis',
    'cock', 'cocks', 'cocker', 'cockers',
    'faggot', 'faggy',
]
toxic_indicator_words_sets = set(toxic_indicator_words)

print(redundancy)
print(toxic_indicator_words_sets)
```

    {'lolol', 'sucksuck', 'gaygay', 'lollol', 'ckckck', 'pussyfuck', 'fuckfuck', 'haha'}
    {'niggar', 'niga', 'nigga', 'monsters', 'fuks', 'fcked', 'fuked', 'fucka', 'stupids', 'fucker', 'fck', 'fk', 'fucked', 'fks', 'asholes', 'monster', 'fcks', 'ass', 'loser', 'fckin', 'faggot', 'niger', 'sucking', 'fuk', 'shit', 'idiots', 'cocks', 'fukin', 'fuckers', 'gays', 'fkin', 'nigar', 'cock', 'fked', 'nazis', 'asses', 'fukers', 'ashole', 'faggy', 'shite', 'fker', 'sucka', 'fuckin', 'fuker', 'shitty', 'asshole', 'cocker', 'fucking', 'fuking', 'idiot', 'stupid', 'fking', 'suck', 'fuck', 'fcking', 'sucker', 'assholes', 'losers', 'gay', 'nazi', 'fcker', 'sucks', 'nigger', 'cockers', 'fucks', 'sucked'}



```python
deny_origin = {
    "you're": ['you', 'are'],
    "i'm": ['i', 'am'],
    "he's": ['he', 'is'],
    "she's": ['she', 'is'],
    "it's": ['it', 'is'],
    "they're": ['they', 'are'],
    "can't": ['can', 'not'],
    "couldn't": ['could', 'not'],
    "don't": ['do', 'not'],
    "don;t": ['do', 'not'],
    "didn't": ['did', 'not'],
    "doesn't": ['does', 'not'],
    "isn't": ['is', 'not'],
    "wasn't": ['was', 'not'],
    "aren't": ['are', 'not'],
    "weren't": ['were', 'not'],
    "won't": ['will', 'not'],
    "wouldn't": ['would', 'not'],
    "hasn't": ['has', 'not'],
    "haven't": ['have', 'not'],
    "what's": ['what', 'is'],
    "that's": ['that', 'is'],
}
denies = set(deny_origin.keys())

print(denies)
```

    {"wouldn't", "they're", "that's", "doesn't", "it's", "wasn't", "can't", "you're", "weren't", "don't", "hasn't", "he's", "didn't", "won't", "what's", "haven't", "i'm", "aren't", 'don;t', "isn't", "couldn't", "she's"}



```python
def _get_toxicIndicator_transformers():
    toxicIndicator_transformers = dict()
    for word in toxic_indicator_words:
        tmp_1 = []
        for c in word:
            if len(tmp_1) > 0:
                tmp_2 = []
                for pre in tmp_1:
                    tmp_2.append(pre + c)
                    tmp_2.append(pre + c + c)
                tmp_1 = tmp_2
            else:
                tmp_1.append(c)
                tmp_1.append(c + c)
        toxicIndicator_transformers[word] = tmp_1
    return toxicIndicator_transformers


toxicIndicator_transformers = _get_toxicIndicator_transformers()
```

**Functions**  
- clean_text : 알파벳, 특수기호만 남기고 숫자는 공백 처리, 소문자화, 특수기호는 앞뒤 공백 추가  
- revise_deny : 사전 정의된 축약어를 full words로 변환  
- revise_star : * 공백 치환  
- revise_triple_and_more_letters : 중복된 알파벳을 최대 2개 중복 알파벳으로 변환 (aaaaaaaa -> aa)  
- revise_redundancy_words : 사전정의된 redundancy words의 중복단어 제거  
- fill_na : "" string을 NA로 치환  


```python
class CommProcess(object):
    @staticmethod
    def clean_text(t):
        t = re.sub(r"[^A-Za-z0-9,!?*.;’´'\/]", " ", t)
        t = replace_numbers.sub(" ", t)
        t = t.lower()
        t = re.sub(r",", " ", t)
        t = re.sub(r"’", "'", t)
        t = re.sub(r"´", "'", t)
        t = re.sub(r"\.", " ", t)
        t = re.sub(r"!", " ! ", t)
        t = re.sub(r"\?", " ? ", t)
        t = re.sub(r"\/", " ", t)
        return t

    @staticmethod
    def revise_deny(t):
        ret = []
        for word in t.split():
            if word in denies:
                ret.append(deny_origin[word][0])
                ret.append(deny_origin[word][1])                
            else:
                ret.append(word)
        ret = ' '.join(ret)
        ret = re.sub("'", " ", ret)
        ret = re.sub(r";", " ", ret)

        return ret

    @staticmethod
    def revise_star(t):
        ret = []
        for word in t.split():            
            if ('*' in word) and (re.sub('\*', '', word) in toxic_indicator_words_sets):
                word = re.sub('\*', '', word)
            ret.append(word)
        ret = re.sub('\*', ' ', ' '.join(ret))
        return ret

    @staticmethod
    def revise_triple_and_more_letters(t):
        for letter in 'abcdefghijklmnopqrstuvwxyz':
            reg = letter + "{2,}"
            t = re.sub(reg, letter + letter, t)
        return t

    @staticmethod
    def revise_redundancy_words(t):
        ret = []
        for word in t.split(' '):
            for redu in redundancy:
                if redu in word:
                    word = redundancy_rightFormat[redu]
                    break
            ret.append(word)
        return ' '.join(ret)

    @staticmethod
    def fill_na(t):
        if t.strip() == '':
            return 'NA'
        return t
```


```python
df_train = pd.read_csv(input_dir + 'train.csv')
test = df_train[df_train['toxic']==1].head()
print(test)

print(execute_comm_process(test))

```

                      id                                       comment_text  \
    6   0002bcb3da6cb337       COCKSUCKER BEFORE YOU PISS AROUND ON MY WORK   
    12  0005c987bdfc9d4b  Hey... what is it..\n@ | talk .\nWhat is it......   
    16  0007e25b2121310b  Bye! \n\nDon't look, come or think of comming ...   
    42  001810bf8c45bf5f  You are gay or antisemmitian? \n\nArchangel WH...   
    43  00190820581d90ce           FUCK YOUR FILTHY MOTHER IN THE ASS, DRY!   
    
        toxic  severe_toxic  obscene  threat  insult  identity_hate  
    6       1             1        1       0       1              0  
    12      1             0        0       0       0              0  
    16      1             0        0       0       0              0  
    42      1             0        1       0       1              1  
    43      1             0        1       0       1              0  
                      id                                       comment_text  \
    6   0002bcb3da6cb337       cocksucker before you piss around on my work   
    12  0005c987bdfc9d4b  hey what is it talk what is it an exclusive gr...   
    16  0007e25b2121310b  bye ! do not look come or think of comming bac...   
    42  001810bf8c45bf5f  you are gay or antisemmitian ? archangel white...   
    43  00190820581d90ce           fuck your filthy mother in the ass dry !   
    
        toxic  severe_toxic  obscene  threat  insult  identity_hate  
    6       1             1        1       0       1              0  
    12      1             0        0       0       0              0  
    16      1             0        0       0       0              0  
    42      1             0        1       0       1              1  
    43      1             0        1       0       1              0  


apply 함수로 CommProcess 안의 function 들을 각 comment에 대해 차례대로 모두 적용


```python
def execute_comm_process(df):
    comm_process_pipeline = [
        CommProcess.clean_text,
        CommProcess.revise_deny,
        CommProcess.revise_star,
        CommProcess.revise_triple_and_more_letters,
        CommProcess.revise_redundancy_words,
        CommProcess.fill_na,
    ]
    for cp in comm_process_pipeline:
        df[COMMENT_COL] = df[COMMENT_COL].apply(cp)
    return df

```

## Preprocessing train / test data


```python
# Process whole train data
print('Comm processing whole train data')
df_train = pd.read_csv(input_dir + 'train.csv')
df_train = execute_comm_process(df_train)
df_train.to_csv('train_processed.csv', index=False)

# Process test data
print('Comm processing test data')
df_test = pd.read_csv(input_dir + 'test.csv')
df_test = execute_comm_process(df_test)
df_test.to_csv('test_processed.csv', index=False)
```

    Comm processing whole train data
    Comm processing test data

## Reference  
- https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge/discussion/52644  
- https://www.kaggle.com/xbf6xbf/processing-helps-boosting-about-0-0005-on-lb  