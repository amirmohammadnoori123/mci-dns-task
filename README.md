# mci-dns-task
# توضیحات 
![This is an image](https://upload.wikimedia.org/wikipedia/en/f/f4/Hamrahe_Aval_logo.png)
## معماری 
![This is an image](https://raw.githubusercontent.com/amirmohammadnoori123/mci-dns-task/main/mci-task.drawio.png)

این پروژه از چهار ماژول تشکیل شده که به ترتیب هریک را توضیج می دهم

## kafka broker

ماژول اصیلی برنامه که اجازه پرسیت کردن جریان داده دامنه ها بر روی تاپیک 
input 

را به ما می دهد

http://167.71.107.99:9000/ 

تاپیک input برای این پروژه در نظر گرفته شده است

ادرس بالا .. آدرس پنل کاربری کافکا می باشد که در سرور های دیجیتال اوشن بالا اماده است 

همچین با توجه به این که حداکثر متریک مورد نظر یک هفته بود ttl را نیز برابر یم هفته قرار دادیم
## KSQKDB

این ماژول وظیفه ایندکسینگ  و شمارش  و استخراج متریک های مورد نیاز از جریان داده ها را بر عهده دارد

برای این منظور ابتدا با کویری زیر جریان داده ای برای دامنه ها ایجاد کردم



**CREATE STREAM domainsCount (domain VARCHAR KEY, timestamp VARCHAR)
  WITH (KAFKA_TOPIC = 'input',
        VALUE_FORMAT = 'JSON',
        TIMESTAMP = 'timestamp',
        TIMESTAMP_FORMAT = 'yyyy-MM-dd HH:mm:ss',
        PARTITIONS = 1);**



و سپس به ترتیپ برای شمارش دامنه ها برای متریک های ۱ ثانیه , یک دقیه ,یک ساعت ,یک روز , و  در نهایت یک هفته



**Window retention¶** مربوطه رو ایجاد کردم



**CREATE TABLE oneSecound AS 
 SELECT domain, COUNT(*) FROM domainsCount
  WINDOW HOPPING (SIZE 1 SECONDS, ADVANCE BY 1 SECONDS)
  GROUP BY domain
  EMIT CHANGES;**


**CREATE TABLE oneMinouts AS 
 SELECT domain, COUNT(*) FROM domainsCount
  WINDOW HOPPING (SIZE 60 SECONDS, ADVANCE BY 10 SECONDS)
  GROUP BY domain
  EMIT CHANGES;**


**CREATE TABLE oneHours AS 
 SELECT domain, COUNT(*) FROM domainsCount
  WINDOW HOPPING (SIZE 1 HOUR, ADVANCE BY 60 SECONDS)
  GROUP BY domain
  EMIT CHANGES;**


**CREATE TABLE oneDay AS 
 SELECT domain, COUNT(*) FROM domainsCount
  WINDOW HOPPING (SIZE 24 HOUR, ADVANCE BY 1 HOUR)
  GROUP BY domain
  EMIT CHANGES;**


**CREATE TABLE onwWeek AS 
 SELECT domain, COUNT(*) FROM domainsCount
  WINDOW HOPPING (SIZE 7 DAY, ADVANCE BY 1 DAY)
  GROUP BY domain
  EMIT CHANGES;**


همچین وضیعت ksqlDB 
ما نیر از لینک زیر قابل دسترسی است



http://167.71.107.99:8088/info


## http stress (Proxy-pass !!!)


برای این که به توان به ماژول اسنفیرم لود بندازم این پروژه را نوشتم .. البته شایان ذکر است که دیلیل اصلی لود انداختم نبود بلکه عمل proxy-pass
بود


کد این پروژه نیز در گیت قابل دسترس است

https://github.com/amirmohammadnoori123/http-stress-test

در برنچ development

البته کدش زیاد اصول مهندسی نرم افزار رعایت نشده خیلی !!!!
:DD

## url - sniffer (DNS-Producer)

این پروژه وظیفه جدا کردن host
از url ریکوست دریافتی را دارد

سورس این پروژه نیز از لینک زیر قایل بررسی است 

https://github.com/amirmohammadnoori123/mci-url-sniffer/tree/development

در پوسشه دولوپ منت
development
