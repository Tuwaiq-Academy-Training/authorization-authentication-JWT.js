# authorization-authentication-Hashing

محتوى الملف :

- ماهي authentication  (المصادقة)
- ماهي Authorization (التفويض  او التخويل )
- انشاء  authentication في  middleware 
- ماهي  hashing؟



## 

يتم استخدام المصادقة والتفويض في الأمان ، لا سيما عندما يتعلق الأمر بالوصول إلى النظام. ومع ذلك ، هناك فرق كبير بين الحصول على الدخول إلى المنزل (المصادقة) وما يمكنك القيام به في الداخل (الترخيص).


## ماهي authentication  (المصادقة)

#### المصادقة هي عملية التحقق من هوية المستخدم من خلال الحصول على بيانات الاعتماد واستخدام بيانات الاعتماد هذه لتأكيد هوية المستخدم. تبدأ عملية التفويض إذا كانت بيانات الاعتماد شرعية. تتبع عملية التفويض دائمًا إجراء المصادقة.

#### لقد كنت بالفعل على علم بعملية المصادقة لأننا نقوم بها جميعًا يوميًا ، سواء في العمل (تسجيل الدخول إلى جهاز الكمبيوتر الخاص بك) أو في المنزل (تسجيل الدخول إلى موقع ويب). ومع ذلك ، فإن الحقيقة هي أن معظم "الأشياء" المتصلة بالإنترنت تتطلب منك إثبات هويتك من خلال تقديم بيانات الاعتماد.


##  ماهي Authorization (التفويض  او التخويل )


التخويل هو عملية السماح للمستخدمين المصادق عليهم بالوصول إلى الموارد عن طريق تحديد ما إذا كان لديهم أذونات الوصول إلى النظام. من خلال منح أو رفض تراخيص معينة لمستخدم مصادق عليه ، يمكّنك التفويض من التحكم في امتيازات الوصول.

لذلك ، يحدث التفويض بعد مصادقة النظام على هويتك ، مما يمنحك وصولاً كاملاً إلى الموارد مثل المعلومات والملفات وقواعد البيانات والأموال والأماكن وأي شيء آخر. ومع ذلك ، يؤثر التفويض على قدرتك على الوصول إلى النظام ومدى إمكانية القيام بذلك.

## انشاء authentication في middleware

يمكننا إنشاء مستخدم وتسجيل الدخول بنجاح. ومع ذلك ، سننشئ مسارًا يتطلب رمزًا مميزًا للمستخدم في الرأس ، وهو رمز JWT الذي أنشأناه سابقًا.

#### أضف الملف التالي داخل auth.js.

ال middleware/auth.js

        const jwt = require("jsonwebtoken");

        const config = process.env;

        const verifyToken = (req, res, next) => {
          const token =
            req.body.token || req.query.token || req.headers["x-access-token"];

          if (!token) {
            return res.status(403).send("A token is required for authentication");
          }
          try {
            const decoded = jwt.verify(token, config.TOKEN_KEY);
            req.user = decoded;
          } catch (err) {
            return res.status(401).send("Invalid Token");
          }
          return next();
        };

        module.exports = verifyToken;


#### فلنقم الآن بإنشاء المسار /welcome وتحديث app.js بالملف التالي لاختبار البرنامج middleware.

         const auth = require("./middleware/auth");

         app.post("/welcome", auth, (req, res) => {
           res.status(200).send("Welcome 🙌 ");
         });
         
         
شاهد النتيجة أدناه عندما نحاول الوصول إلى المسار /welcome الذي أنشأناه للتو دون تمرير رمز مميز في الرأس باستخدام مفتاح x-access-token.



![image](https://www.section.io/engineering-education/how-to-build-authentication-api-with-jwt-token-in-nodejs/error-response.png)


#### يمكننا الآن إضافة رمز مميز في header  باستخدام المفتاح x-access-token وإعادة الاختبار.

![image](https://www.section.io/engineering-education/how-to-build-authentication-api-with-jwt-token-in-nodejs/success-response.png)




##  ماهي  hashing؟

ال hashing هي عملية تحويل مفتاح معين إلى قيمة أخرى. تُستخدم دالة التجزئة لتوليد القيمة الجديدة وفقًا لخوارزمية رياضية. تُعرف نتيجة دالة التجزئة باسم قيمة التجزئة أو ببساطة التجزئة.

![hash](https://user-images.githubusercontent.com/92247967/201050028-dd6db11c-cb37-4aad-9d78-c28d448654a3.png)


تستخدم دالة hash الجيدة خوارزمية hash أحادية الاتجاه ، أو بمعنى آخر ، لا يمكن تحويل hash مرة أخرى إلى المفتاح الأصلي.

![one-way](https://user-images.githubusercontent.com/92247967/201051294-7f8cb869-40b4-4ff0-98b7-c9055f636b41.png)


## الاصطدامات

ضع في اعتبارك أن مفتاحين يمكن أن يولدا نفس hash. تُعرف هذه الظاهرة بالاصطدام. هناك عدة طرق للتعامل مع الاصطدامات ، ولكن هذا موضوع لوقت آخر.

![Collisions](https://user-images.githubusercontent.com/92247967/201052348-8a6872f5-d167-481a-801f-e554d013e6d2.png)

