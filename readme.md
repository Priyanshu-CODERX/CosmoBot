# CosmoBot - A Cosmology Assistant

<h5>CosmoBot is a chatbot that can clear doubts of cosmology student's and enthusiast's by finding the similarity between the word vector's.</h5>

<h5>This Chatbot uses Cosine Similarity Model to find the most appropriate answer to your question regarding cosmology. It has a GUI where you can type your questions and get and appropriate answer for it. 
</h5>

<h5>It can narrate the answer to you and also show the text on the screen.</h5>

# Installing Modules

<h6>
    <li>conda install nltk</li>
    <li>conda install sklearn</li>
    <li>conda install numpy</li>
    <li>pip install pyttsx3</li>
</h6>

# Development Environment

<h6>
    <li>Anaconda</li>
    <li>Jupyter Notebook</li>
</h6>

# File Structure

<h6>
   	<li>CosmoBot.ipynb: Python Notebook File</li>
    <li>logo1: Logo File For GUI</li>
    <li>universe.txt: Dataset</li>
    <li>readme.md: Readme File</li>
    <li>requirements.txt: Modules Information</li>
</h6>

#### Demo Video

##### [CosmoBot Demo Video](https://youtu.be/JLCOq47vASw)

# Code Snippets

#### Importing Modules

```python
import nltk
import numpy as np
import random 
import string
from sklearn.metrics.pairwise import cosine_similarity
from sklearn.feature_extraction.text import TfidfVectorizer
import pyttsx3
from tkinter import *
```

#### Initializing Speech Engine

```python
engine = pyttsx3.init()
speaker = engine.getProperty('voice')
engine.setProperty('voice', 'HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Speech\\Voices\\Tokens\\TTS_MS_EN-US_ZIRA_11.0') # Setting voice property
def speak(speech):
	engine.say(speech)
	engine.runAndWait()
```

#### Reading Text Database

```python
filepath='./universe.txt'
corpus=open(filepath,'r',errors = 'ignore')
data=corpus.read()
print (data)
```

#### Text Normalization

##### Lowercasing Text

```python
data = data.lower()
data
```

##### Sentence Segmentation

```python
sentence = nltk.sent_tokenize(data)
sentence
```

##### Tokenizing Words

```python
words = nltk.word_tokenize(data)
words
```

##### Lemmatization

```python
lem = nltk.stem.WordNetLemmatizer()
def LemmatizeTokens(tokens):
    return [lem.lemmatize(token) for token in tokens]
```

##### Removing Punctuations

```python
removePunctuations = dict((ord(punct), None) for punct in string.punctuation)
def LemNormalize(text):
    return LemmatizeTokens(nltk.word_tokenize(text.lower().translate(removePunctuations)))
```

##### Removing Stop Words

```python
sw = nltk.corpus.stopwords.words('english')
sw
```

##### Greetings and Responses

```python
greetings = ["hello", "hi", "greetings", "sup", "what's up","hey", "hey there" , 'konnichiwa', 'namaste', "hela"]
responses = ["hi", "hey", "hi there", "hello", "I am glad! You are talking to me", 'satsriyakala']
```

```python
def Greet(sentence):
    for word in sentence.split(): # Looks at each word in your sentence
        if word.lower() in greetings: 
            return random.choice(responses)
```

##### Implementing Bag Of Words, TFIDF & Cosine Similarity

```python
def userResponse(res):
    
    botRes = ''
    sentence.append(res)
    tfidfVector = TfidfVectorizer(tokenizer = LemNormalize, stop_words = 'english')
    tfidf = tfidfVector.fit_transform(sentence)
    values = cosine_similarity(tfidf[-1], tfidf)
    idx = values.argsort()[0][-2]
    flat = values.flatten()
    flat.sort()
    reqTfidf = flat[-2]
    
    if(reqTfidf==0):
        botRes = botRes + "Sorry! I can't understand you"
        return botRes
    else:
        botRes = botRes + sentence[idx]
#         speak(botRes)
        return botRes
```

#### Deployment

##### CLI Version

```python
flag=True
while(flag==True):
    user_response = input()
    user_response=user_response.lower()
    if(user_response!='bye'):
        if(user_response=='thanks' or user_response=='thank you' ):
            flag=False
            print("Cosmo Bot: You are welcome..")
        else:
            if(Greet(user_response)!=None):
                print("Cosmo Bot: "+Greet(user_response))
            else:
                print("Cosmo Bot: ",end="")
                print(userResponse(user_response))
                sentence.remove(user_response)
    else:
        flag=False
        print("Cosmo Bot: Bye! take care..")
```

##### GUI Version(Tkinter)

```python
def bot():
    user_response = inputField.get().lower()
    if(user_response!='bye'):
        if(user_response=='thanks' or user_response=='thank you' ):
            outputVar.set("You are welcome..")
            speak("You are welcome..")
        else:
            if(Greet(user_response)!=None):
                g = Greet(user_response)
                outputVar.set(g)
                speak(g)
                
            else:
                usr = userResponse(user_response)
#                 print("ROBO: ",end="")
                outputVar.set(usr)
                speak(usr)
                sentence.remove(user_response)
    else:
        speak("Bye! see you soon")
```

```python
# Implementing GUI using Tkinter

def clearOuput():
    outputVar.set(" ")

# Color Codes
bgColor = "#212121"
spareColor = "#FF8F00"
fgColor = "#ffffff"

# Base Window
root = Tk()
outputVar = StringVar()

# icon
ico = PhotoImage(file = "./logo1.png")
root.iconphoto(False, ico)

root.title("Cosmo Bot")
root.geometry("720x480")

# Background and Foreground Color
root['background'] = bgColor

# Header Text
header = Label(root, text="Cosmo Bot")
header.config(font=("Josefin Sans", 44, "bold"), fg = fgColor, bg = bgColor)
header.pack()

# Input Handler
inputField = Entry(root, width=40, font=("Josefin Sans", 20, "bold")) # Takes input of command
inputField.pack()

# Buttons
enterBtn = Button(root, text="Ask", font=("Josefin Sans", 20),  fg = fgColor, bg = bgColor, command = bot) # Button to submit
enterBtn.pack()

# Output Header Text
outputHeader = Label(root, text="Your Answer")
outputHeader.config(font=("Josefin Sans", 20, "bold"), fg = fgColor, bg = bgColor)
outputHeader.pack()

# Output
output = Label(root, textvariable=outputVar, wraplength=720)
output.config(font=("Josefin Sans", 20, "bold"), fg = spareColor, bg = bgColor)
output.pack()

# Buttons
clearBtn = Button(root, text="Clear Output", font=("Josefin Sans", 20),  fg = fgColor, bg = bgColor, command = clearOuput) # Button to submit
clearBtn.pack()

root.mainloop()
```



