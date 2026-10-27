# Vulnerability: Python Insecure Deserialization

## Overview
The application is vulnerable to Insecure Deserialization (classified under OWASP Top 10 A08:2021 - Software and Data Integrity Failures). It processes untrusted user input using Python's inherently unsafe `pickle` module.

## Vulnerability Mechanism
The `pickle` module is designed to serialize and deserialize Python object structures. An attacker can exploit the `__reduce__` magic method, which tells the interpreter how to reconstruct the object. By crafting a serialized object that specifies a system command (e.g., using `os.system`), the payload executes automatically the moment the server calls `pickle.loads()`, resulting in Remote Code Execution (RCE).

## Exploitation Scenarios
1. **Manipulated Session Cookies:** If an application serializes user session data using `pickle` and stores it in cookies, an attacker can replace their cookie with a Base64-encoded malicious serialized object.
2. **Message Brokers / IPC:** Applications that receive serialized objects via background job queues (like Redis or RabbitMQ) from untrusted external sources can be compromised when worker nodes deserialize the malicious payloads.

---
---

# الثغرة: إلغاء التسلسل غير الآمن في بايثون (Insecure Deserialization)

## نظرة عامة
التطبيق مصاب بثغرة إلغاء التسلسل غير الآمن (والمصنفة ضمن قائمة OWASP لأهم 10 ثغرات لعام 2021 تحت بند A08 - إخفاقات سلامة البرامج والبيانات). يقوم التطبيق بمعالجة مدخلات المستخدم غير الموثوقة باستخدام مكتبة `pickle` في بايثون، وهي غير آمنة بطبيعتها.

## آلية عمل الثغرة
تم تصميم مكتبة `pickle` لعمل تسلسل (Serialization) وفك تسلسل (Deserialization) لهياكل كائنات بايثون. يمكن للمهاجم استغلال الدالة السحرية `__reduce__`، والتي تخبر المترجم بكيفية إعادة بناء الكائن. من خلال صياغة كائن متسلسل يحدد أمر نظام (على سبيل المثال، باستخدام `os.system`)، يتم تنفيذ البايلود تلقائياً في اللحظة التي يستدعي فيها الخادم دالة `pickle.loads()`، مما يؤدي إلى تنفيذ أوامر عن بُعد (RCE).

## سيناريوهات الاستغلال
1. **ملفات تعريف الارتباط (Cookies) التي تم التلاعب بها:** إذا قام التطبيق بعمل تسلسل لبيانات جلسة المستخدم باستخدام `pickle` وقام بتخزينها في ملف تعريف الارتباط، يمكن للمهاجم استبدال ملفه بكائن متسلسل خبيث ومشفر بصيغة Base64.
2. **وسطاء الرسائل (Message Brokers):** التطبيقات التي تستقبل كائنات متسلسلة عبر طوابير المهام الخلفية (مثل Redis أو RabbitMQ) من مصادر خارجية غير موثوقة، يمكن اختراقها عندما تقوم عقد العمل (Worker nodes) بفك تسلسل الحمولات الخبيثة.
