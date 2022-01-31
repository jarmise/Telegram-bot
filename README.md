#pip install pytelegrambotapi
import telebot;
bot = telebot.TeleBot('5058233909:AAEYn2BQqz44dDXm4lDRDGn0TT0irKsCd_w');
import pandas as pd
import numpy
from decimal import Decimal
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
data=pd.read_csv('heart.csv')
data.drop(['time'], inplace=True, axis=1)
data.drop(['Unnamed: 0'], inplace=True, axis=1)
renaming = {
    'age': 'возраст',
    'anaemia': 'анемия',
    'creatinine_phosphokinase': 'креатиназа',
    'diabetes': 'диабет',
    'ejection_fraction': 'фракция_выброса',
    'high_blood_pressure': 'высокое_кровяное_давление',
    'platelets': 'тромбоциты',
    'serum_creatinine': 'креатин',
    'serum_sodium': 'натрий',
    'sex': 'пол',
    'smoking': 'курение',
    'Risk of accute condition': 'риск осложнения'    
}
data = data.rename(columns=renaming)
counter = 0
machine = pd.DataFrame(columns=['возраст','пол','курение','высокое_кровяное_давление','анемия','диабет','фракция_выброса'
,'креатин','креатиназа','натрий','тромбоциты'])
new_patients = pd.DataFrame(columns=['имя','возраст','пол','курение','высокое_кровяное_давление','анемия','диабет','фракция_выброса'
,'креатин','креатиназа','натрий','тромбоциты','риск осложнения'])
yesno=['да','нет','Да','Нет']
yess=['да','Да']
name = '';
age = 0;
gendr ='';
@bot.message_handler(content_types=['text'])
def start(message):    
    bot.send_message(message.from_user.id, "Как вас зовут?");
    bot.register_next_step_handler(message, get_Age);
def get_Age(message):
    name = message.text;
    new_patients.loc[counter, 'имя'] = name
    bot.send_message(message.from_user.id,'Сколько вам лет?');
    bot.register_next_step_handler(message, get_Age1);
def get_Age1(message):
    age = message.text;
    age = int(age)
    if (age<100 and age>16):
        new_patients.loc[counter, 'возраст'] = age
        machine.loc[counter, 'возраст'] = age
        er = bot.send_message(message.from_user.id,'Ваш пол?(муж или жен)');
        bot.register_next_step_handler(er,get_Gendr);
    else : 
        bot.send_message(message.from_user.id,'введи коректный возраст');
        bot.register_next_step_handler(message, get_Age1);
def get_Gendr(message):
    gendr = message.text;
    if gendr in ['муж','жен','вертолет']: 
        new_patients.loc[counter, 'пол'] =gendr
        machine.loc[counter, 'пол'] =gendr
        ar = bot.send_message(message.from_user.id,'У вас высокое_кровяное_давление?');
        bot.register_next_step_handler(ar,get_High_blood);
    else: 
        bot.send_message(message.from_user.id,'введи коректный пол');
        bot.register_next_step_handler(message,get_Gendr);
def get_High_blood(message):
    High_blood_pressure = message.text;
    if High_blood_pressure in yesno:
        new_patients.loc[counter, 'высокое_кровяное_давление'] =High_blood_pressure
        machine.loc[counter, 'высокое_кровяное_давление'] =High_blood_pressure
        yr = bot.send_message(message.from_user.id,'Вы курите?');
        bot.register_next_step_handler(yr,get_Smok );
    else: 
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_High_blood);
def get_Smok(message):
    Smok = message.text;
    if Smok in yesno:
        new_patients.loc[counter, 'курение'] = Smok
        machine.loc[counter, 'курение'] = Smok
        ed = bot.send_message(message.from_user.id,'У вас есть сахарный диабет?');
        bot.register_next_step_handler(ed,get_diabetes);
    else: 
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_Smok);
def get_diabetes(message):
    diabetes = message.text;
    if diabetes in yesno:
        new_patients.loc[counter, 'диабет'] =diabetes
        machine.loc[counter, 'диабет'] =diabetes
        ad = bot.send_message(message.from_user.id,'У вас есть анемия?');
        bot.register_next_step_handler(ad,get_anaemia);
    else:                                   
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_diabetes);
def get_anaemia(message):
    anaemia = message.text;     
    if anaemia in yesno:
        new_patients.loc[counter, 'анемия'] =anaemia
        machine.loc[counter, 'анемия'] =anaemia
        yd = bot.send_message(message.from_user.id,'Введите ваше содержание креатинина в сыворотке');
        bot.register_next_step_handler(yd,get_serum_creatinine);
    else:    
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_anaemia);
def get_serum_creatinine(message):        
    serum_creatinine = message.text;
    new_patients.loc[counter,'креатин'] =serum_creatinine
    machine.loc[counter,'креатин'] =serum_creatinine        
    ew = bot.send_message(message.from_user.id,'Введите вашу фракцию выброса');
    bot.register_next_step_handler(ew,get_ejection_fraction);
def get_ejection_fraction(message):
    ejection_fraction = message.text;
    if (ejection_fraction.isdigit()==True):
        new_patients.loc[counter,'фракция_выброса'] =ejection_fraction
        machine.loc[counter,'фракция_выброса'] =ejection_fraction
        aw = bot.send_message(message.from_user.id,'Введите ваше содержание креатинкиназы');
        bot.register_next_step_handler(aw,get_creatinine_phosphokinase);
    else: 
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_ejection_fraction);
def get_creatinine_phosphokinase(message):
    creatinine_phosphokinase = message.text; 
    if (creatinine_phosphokinase.isdigit()==True):
        new_patients.loc[counter,'креатиназа'] =creatinine_phosphokinase
        machine.loc[counter,'креатиназа'] =creatinine_phosphokinase
        qw = bot.send_message(message.from_user.id,'Введите ваше содержание тромбоцитов');
        bot.register_next_step_handler(qw,get_platelets);
    else: 
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message, get_creatinine_phosphokinase);
def get_platelets(message):
    platelets = message.text;
    if (platelets.isdigit()==True):
        new_patients.loc[counter,'тромбоциты'] =platelets
        machine.loc[counter,'тромбоциты'] =platelets
        qr = bot.send_message(message.from_user.id,'Введите ваше содержание натрия в сыворотке');
        bot.register_next_step_handler(qr,get_sodium);
    else:         
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message,get_platelets);
def get_sodium(message):       
    sodium = message.text;
    machine['пол'].replace('муж', 1 ,inplace=True)
    machine['пол'].replace('жен', 0 ,inplace=True)
    machine['пол'].replace('вертолет', 1 ,inplace=True)


    machine['курение'].replace('да', 1 ,inplace=True)
    machine['курение'].replace('Да', 1 ,inplace=True)
    machine['курение'].replace('нет', 0 ,inplace=True)
    machine['курение'].replace('Нет', 0 ,inplace=True)

    machine['диабет'].replace('да', 1 ,inplace=True)
    machine['диабет'].replace('Да', 1 ,inplace=True)
    machine['диабет'].replace('нет', 0 ,inplace=True)
    machine['диабет'].replace('Нет', 0 ,inplace=True)

    machine['высокое_кровяное_давление'].replace('да', 1 ,inplace=True)
    machine['высокое_кровяное_давление'].replace('Да', 0 ,inplace=True)
    machine['высокое_кровяное_давление'].replace('нет', 1 ,inplace=True)
    machine['высокое_кровяное_давление'].replace('Нет', 1 ,inplace=True)

    machine['анемия'].replace('да', 1 ,inplace=True)
    machine['анемия'].replace('да', 1 ,inplace=True)
    machine['анемия'].replace('нет', 0 ,inplace=True)
    machine['анемия'].replace('Нет', 0 ,inplace=True)
    new_patients['риск осложнения'] = new_patients['риск осложнения'].astype('str')

    if (sodium.isdigit()==True):
        new_patients.loc[counter,'натрий'] =sodium
        machine.loc[counter,'натрий'] =sodium
        qd = bot.send_message(message.from_user.id,'Мнение врача');
        bot.register_next_step_handler(qd,method1);
    else:
        bot.send_message(message.from_user.id,'введите корректный ответ');
        bot.register_next_step_handler(message, get_sodium);
def method1(message):    
        
    from sklearn.model_selection import train_test_split
    from sklearn.linear_model import LogisticRegression
    from sklearn.metrics import accuracy_score 
    from sklearn.metrics import precision_score 
    from sklearn.metrics import recall_score 
    from sklearn.metrics import f1_score
    import numpy as np
    np.random.seed(50)

    x = data.drop(['риск осложнения'], axis=1)
    y = data['риск осложнения']

    x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.4)

    Ir = LogisticRegression(C=5).fit(x_train, y_train)
    Ir_pred = Ir.predict_proba(machine)
    rounded = [numpy.round(x) for x in Ir_pred]
    
    #Ir_pred = Ir.predict(x_test)
    #Ir_pred=[1]
    g= Ir_pred[0][1] *100
    #new_patients.loc[counter,'риск осложнения'] = Ir_pred[1]
    if (Ir_pred[0][1]<= 0.5  ): 
        bot.send_message(message.from_user.id,'У вас нет риска осложнения риск осложнения, модель уверена в своем предсказании');
        bot.send_message(message.from_user.id,Ir_pred[0][1] *100);
       
    else:
        bot.send_message(message.from_user.id,'У вас есть риск осложнения, модель уверена в своем предсказании');
        bot.send_message(message.from_user.id,Ir_pred[0][1] *100);       
