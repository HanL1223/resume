---
title: "Todo List App"
description: "A Todo list web application built using React"
dateString: May 2021
draft: false
tags: ["Python","ScikitLearn","Machine Learning","Ensemble Learning"]
showToc: false
weight: 202
cover:
    image: "projects/todo-list-app/cover.jpg"
--- 

## Description

A Data Science project to predict the visa application.This is a text project to comapre the overall performance between signle model vs Emsemble method

```python
#Loading Require Libraries
import pandas as pd
from transformers import BertForPreTraining,BertTokenizer,AutoTokenizer,AutoModelForSequenceClassification
import re
import string
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
from nltk.stem import PorterStemmer
from torch.utils.data import TensorDataset, DataLoader
import torch
import torch.nn as nn
import torch.optim as optim
from sklearn.model_selection import train_test_split
import numpy as np
from sklearn.metrics import accuracy_score, f1_score, precision_score, recall_score, roc_auc_score, roc_curve
import optuna
import os
from nltk.stem import WordNetLemmatizer
import optuna
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, TensorDataset
from transformers import BertForSequenceClassification,AutoModelForSequenceClassification,AutoTokenizer
from sklearn.model_selection import train_test_split
tokenizer = AutoTokenizer.from_pretrained("mrm8488/bert-tiny-finetuned-sms-spam-detection")
from sklearn.metrics import accuracy_score, f1_score, precision_score, recall_score, roc_auc_score, roc_curve
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader, TensorDataset
from transformers import BertForSequenceClassification
from sklearn.model_selection import train_test_split
```
```python
# Loading data

```



