همه ما به عنوان برنامه‌نویس یا تستر با اجرای درخواست‌های HTTP از Postman برای بررسی صحت APIها و ساختار پاسخ‌ و غیره استفاده می‌کنیم. اما این ابزار از ویژگی‌های متنوعی برخوردار است که می‌تواند فرآیند تست API را ساده‌تر و مؤثرتر کند. یکی از این ویژگی‌ها، امکان نوشتن اسکریپت‌ها و اجرای آن ها قبل و بعد از اجرای APIها است.  از این ویژگی می‌توان برای خودکارسازی تست‌های عملکردی API و یا دیباگ کردن در فرآیند بررسی صحت APIها استفاده کرد. در این مقاله نحوه استفاده از این قابلیت بررسی می‌شود.
Postman یک بستر اجرایی بر پایه‌ی NodeJS فراهم میکند که به ما اجازه می‌دهد اسکریپت‌های با زبان جاوا اسکریپت قبل و بعد از اجرای APIها اضافه کنیم. این ویژگی به ما کمک میکند تا درخواست‌هایی با داده‌های داینامیک بسازیم. مثلا قبل از ارسال درخواست مقدار پارامترهای آن را بصورت داینامیک و براساس یک فرمول تولید کنیم. یا همچنین از روندهای تصادفی برای تولید پارامترها استفاده کنیم. یا بخشی از جواب یک درخواست را استخراج کرده و به درخواست بعدی پاس بدهیم (برای خودکارسازی سناریوها تست API عموماً از چنین تکنیکی استفاده میکنیم). همچنین می‌توانیم صحت یک پاسخ را از جنبه‌های مختلف توسط یک اسکریپت بررسی کنیم.شما میتوانید اسکریپت‌هایی به زبان جاوا اسکریپت به تست‌های خود اضافه کنید که در دو حالت زیر اجرا می‌شود:
* **پیش‌پردازش:** پیش از اینکه یک ریکوئست (درخواست) به سرور ارسال شود به عنوان یک Pre-request script در تب Pre-request Scripts اجرا می‌شود. چنین اسکریپتی به عنوان پیش-پردازش عمل می‌کند.
*  **پس‌پردازش:** پس از دریافت پاسخ از سرور به عنوان test script در تب Test اجرا می‌شود. معمولا برای بررسی صحت پاسخ یا استخراج مقادیری از پاسخ از چنین اسکریپت‌هایی که آنها را پس-پردازش می‌نامیم استفاده می‌کنیم.

شما می‌توانید اسکریپت تست و یا pre-request را در سطح ریکوئست، فولدر (folder) و یا کالکشن (collection) ایجاد کنید. با انتساب اسکریپت‌ها به فولدر (یا کالکشن) می‌توان یک اسکریپت‌ را برای مجموعه درخواست‌ها آن فولدر (یا کالکشن) اجرا کرد؛ با این ویژگی در واقع می‌توان از اسکریپت‌ها استفاده مجدد کرده و کارهای همه منظوره‌ای را برای گروهی از درخواست‌ها بکار بست.

### ترتیب اجرای اسکریپت‌ها
در Postman ترتیب اجرای اسکریپت‌ها به صورت زیر است:
* اسکریپت pre-request پیش از ارسال درخواست آن اجرا خواهد شد (گام اول در تصویر زیر).
* بعد از اجرای یک درخواست، اسکریپت تست آن اجرا خواهد شد (گام چهارم در تصویر زیر). 

![Request Script Order](./resources/request-order-script.png?raw=true "Request Script Order")

برای هر درخواست در یک فولدر یا کالکشن، اسکریپت‌ها به ترتیب زیر اجرا می‌شوند:
* پیش از اجرای هر درخواست درون یک کالکشن، اسکریپت pre-request مربوط به آن اجرا خواهد شد (گام اول در تصویر).
* پیش از اجرای هر درخواست درون یک فولدر اسکریپت pre-request مربوط به آن اجرا خواهد شد (گام دوم در تصویر).
* پیش از اجرای درخواست اسکریپت pre-request مربوط به خود درخواست اجرا می‌شود (گام سوم در تصویر)
* پس از اجرای هر درخواست درون یک کالکشن، اسکریپت تست مربوط به آن اجرا خواهد شد. (گام ششم در تصویر)
* پس از اجرای هر درخواست درون یک فولدر، اسکریپت تست مربوط به آن اجرا خواهد شد (گام هفتم در تصویر).
* در نهایت، اسکریپت تست مربوط به خود درخواست اجرا خواهد شد (گام هشتم در تصویر)

![Postman Script Order](./resources/postman-script-order.png?raw=true "Postman Script Order")

#### مثال: بررسی ترتیب اجرای اسکریپت‌ها در بخش‌های مختلف
برای مثال فرض کنید ساختار تست شما به صورت زیر است که دو درخواست درون یک فولدر در یک کالکشن قرار دارند.برای تست ترتیب اجرای هر بخش، با استفاده از console.log عبارتی را در کنسول Postman نمایش می‌دهیم. با افزودن این عبارت در تب‌های tests و pre-request در تمامی بخش‌های درخواست، فولدر و کالکشن این موضوع را می‌توان اثبات کرد.

```javascript
console.log("(request-1)اسکریپت تست درخواست اول")
```

![Script Order Example](./resources/script-order-example-log.png?raw=true "Script Order Example")

همانطور که در شکل زیر مشاهده میکنید ترتیب اجرا به همان ترتیبی است که پیش از این به آن اشاره کردیم. این بخش‌ها توسط sandbox در Postman اجرا می‌شود. Sandbox محیطی است که تمامی اسکریپت‌های موجود در تب tests و pre-request را اجرا میکند. همچنین شما می‌توانید در این تب‌ها اسکریپت‌هایی برای debug کردن تست‌های خود اضافه کنید.


![Script Order Example](./resources/postman-script-order-example.png?raw=true "Script Order Example")

## اسکریپت نویسی در تب pre-request
همانطور که پیش از این اشاره شد تست‌ها بعد از ارسال درخواست‌ها و دریافت پاسخ از سرور اجرا میشوند. اما گاهی نیاز دارید پیش از اجرای درخواست، عملی را انجام دهید. برای مثال شما نیاز دارید داده‌ای را برای درخواست خود ایجاد کنید که این عمل را می‌توانید در تب pre-request انجام دهید. اسکریپت‌های نوشته شده در این تب پیش از ارسال درخواست اجرا میشوند.اگر اسکریپت‌های ایجاد شده در درخواست‌های زیادی مورد نیاز باشند شما میتوانید آن‌ها را در تب pre-request در سطح فولدر و یا کالکشن اضافه کنید. پس از آن اسکریپت نوشته شده در تمامی درخواست‌های داخل آن فولدر و یا کالکشن در دسترس می‌باشد و پیش از اجرای هر درخواست ابتدا pre-request مربوطه اجرا می‌شود.

### مثال: تولید متغیر قیمت پیش از ایجاد آگهی در سایت دیوار
 برای مثال فرض کنید قصد داریم در سایت دیوار یک آگهی ایجاد کنیم و بخشی از مقادیر موجود در بدنه درخواست مانند قیمت را پیش از ارسال درخواست به سرور تولید کنیم و در متغیری ذخیره کرده و در بدنه استفاده کنیم. همانطور که در تصویر مشاهده می‌کنید با استفاده از متد Math در جاوااسکریپت یک عدد تصادفی برای قیمت ایجاد و سپس آن را در متغیری ذخیره کرده‌ایم.

```javascript
let price = Math.floor(Math.random() * 10000000000) + 5000000000;
pm.environment.set("price_key", price);
console.log(pm.environment.get("price_key"));
```

![Pre-request Script](./resources/pre-request-script.png?raw=true "Pre-request Script")

سپس در بخش بدنه درخواست، متغیر ساخته شده را با استفاده از الگوی `{{price_key}}` استفاده می‌کنیم.

![Use Created Variable](./resources/use-created-variable.png?raw=true "Use Created Variable")

پیش از اجرای درخواست، اسکریپت مربوطه اجرا شده و سپس در هنگام اجرا مقدار متغیر ساخته شده‌ در بدنه درخواست به سرور ارسال می‌شود.

## نوشتن تست
با افزودن تست‌های مختلف در این تب و اجرای آن‌ها پس از هر بار تغییر در سیستم میتوان از صحت عملکرد سیستم اطمینان حاصل کرد. شما میتوانید تست‌های خود را به زبان جاوااسکریپت نوشته و یا می‌توانید از اسکریپت‌های آماده در سمت راست در بخش Snippets استفاده کنید. همچنین در صورتی که اسکریپت نوشته شده در درخواست‌های زیادی مشابه هم هستند میتوانید اسکریپت‌ها را در سطح فولدر و یا کالکشن اضافه کنید. در این صورت پس از اجرای هر درخواست و دریافت پاسخ از سرور این اسکریپت‌ها اجرا می‌شوند.
![Snippets](./resources/snippets.png?raw=true "Snippets")
در زیر چند مثال جهت اضافه کردن تست برای بررسی بخش‌های مختلف پاسخ را توضیح داده‌ایم.

### بررسی کد پاسخ در تب Test
یکی از ابتدایی‌ترین تست‌هایی که برای صحت هر درخواست می‌توان نوشت چک کردن کد پاسخ (Response code) و پیغام پاسخ (Response Message) می‌باشد. در مثال زیر سعی داریم برای درخواست ساخته شده جهت ایجاد یک آگهی در سایت دیوار تاییدیه (Assertion) ایجاد کنیم. Assertionها به شما این امکان را می‌دهند که پس از دریافت پاسخ‌‌، نتیجه مورد انتظار و نتیجه واقعی را بررسی کرده و در صورتی که این دو مقدار برابر نباشند، تست شما رد می‌شود. در مثال ایجاد آگهی در سایت دیوار در صورتی که اجرای درخواست موفقیت آمیز باشد و آگهی با موفقیت ایجاد شود کد درخواست 200 دریافت میشود. از آن ‌جایی که ما انتظار داریم که درخواست ارسالی موفقیت‌آمیز بوده و و کد 200 دریافت کنیم کدی مشابه کد زیر در تب Test نوشته و درخواست را اجرا می‌کنیم. این کد به این معناست که Postman انتظار دارد پاسخ دریافتی دارای کد 200 باشد.

```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```
پس از ارسال درخواست و دریافت پاسخ این assertion اجرا شده و در بخشی که نتیجه اجرا نمایش داده می‌شود می‌توانید نتیجه آن را در تب Test Results مشاهده کنید.

![Assertion Result Pass](./resources/assertion-result-pass.png?raw=true "Assertion Result Pass")

با ایجاد تغییراتی در بدنه درخواست و ارسال مجدد آن می‌توانیم موفقیت آمیز نبودن تست خود را مشاهده کنیم. 

![Assertion Result Fail](./resources/assertion-result-fail.png?raw=true "Assertion Result Fail")

### بررسی بدنه پاسخ دریافتی

همانطور که در تصویر زیر مشاهده می‌کنید مقدار قیمت ایجاد شده توسط ما پیش از اجرای تست در بدنه پاسخ نمایش داده شده است. قصد داریم assertionای را برای چک کردن این مقدار ایجاد کرده به طوری که قیمت دریافتی در پاسخ با قیمتی که پیش از اجرا تولید شد برابر باشد.

![Response](./resources/response.png?raw=true "Response")

از آن جایی که پاسخ دریافتی با فرمت JSON میباشد باید این مقدار را از پاسخ استخراج کنیم. برای این منظور شما میتوانید از کتابخانه‌های موجود جهت پارس کردن JSON در جاوااسکربپت استفاده کنید. همچنین می‌توانید از کدهای آماده در بخش Snippets استفاده کنید. بر روی گزینه Response body: JSON value Check کلیک کرده و آن را با توجه به نیازمندی خود تغییر دهید.

```javascript
pm.test("Check price", function () {
    var price = pm.environment.get("price_key");
    var jsonData = pm.response.json();
    pm.expect(jsonData.webengage.price).to.eql(price);
});
```

![Json Assertion](./resources/json-assertion.png?raw=true "Json Assertion")

همانطور که در تصویر زیر مشاهده میکنید در بخش Test Result نتیجه تمام تست‌های نوشته شده را نمایش می‌دهد.

![Assertion Results](./resources/assertion-results.png?raw=true "Assertion Results")

### بررسی headerهای دریافتی

با توجه به تصویر زیر هدرهایی در پاسخ دریافتی از سرور مشاهده می‌کنیم.

![Divar Response Header](./resources/divar-response-header.png?raw=true "Divar Response Header")

با استفاده از Postman می‌توانیم وجود یا عدم وجود یک هدر و یا مقدار آن‌ها را بررسی کنیم. یکی از این هدرها content-type می‌باشد که با استفاده از کد زیر مقدار دریافتی را با مقدار مورد انتظار مقایسه کرده و در صورتی که برابر باشند این تست موفقیت آمیز خواهد بود.

```javascript
pm.test("Content-Type is application/json", function () {
    pm.expect(pm.response.headers.get('Content-Type')).to.eql('application/json');
});
```
پس از اجرای تست همان طور که در تصویر زیر نمایش داده می‌شود تست مربوطه موفقیت آمیز بوده و با رنگ سبز مشخص شده است.

![Postman Header Assertion](./resources/postman-header-assertion.png?raw=true "Postman Header Assertion")

### بررسی زمان پاسخ

یکی دیگر از پارامترهای که میتوان تست کرد زمان پاسخ (زمانی که طول می‌کشد سرور به درخواست ارسالی پاسخ دهد) است. برای تست این پارامتر از کد زیر استفاده کرده و بازه زمانی که انتظار داریم سرور به درخواست ما پاسخ دهد را به عنوان زمان مورد انتظار در نظر می‌گیریم. اگر پاسخ دریافتی بیشتر از این زمان طول بکشد تست رد می‌شود. در مثال ما این زمان برابر ۱۵۰۰ میلی‌ثانیه است.
```javascript
pm.test("Response time is less than 200ms", () => {
  pm.expect(pm.response.responseTime).to.be.below(1500);
});
```

![Response Time Assertion](./resources/response-time-assertion.png?raw=true "Response Time Assertion")

شما میتوانید به هر تعدادی که نیاز دارید  assertion به تست خود اضافه کنید. اکنون که شما با بخش‌های مختلف Postman آشنا شده‌اید می‌توانید خودکارسازی تست‌های خود را آغاز کنید. اگر با زبان جاوااسکریپت آشنایی ندارید نگران نباشید زیرا Postman ابزار انعطاف‌پذیری است و شما میتوانید با استفاده از کدهای آماده‌ای که در دسترس شما قرار داده است خودکارسازی تست‌های خود را آغاز کنید.
