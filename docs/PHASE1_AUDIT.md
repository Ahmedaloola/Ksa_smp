# KSA SMP — تدقيق المرحلة الأولى

## النطاق

تمت مراجعة نسخة GitHub فقط. لم يتم تعديل السيرفر الحي، أو JAR، أو عالم، أو قاعدة بيانات، أو Secret. هذه الوثيقة لا تغيّر سلوك السيرفر.

## خريطة الملفات الجديدة

| الملف الحالي | الإضافة | المسار النهائي المقترح |
|---|---|---|
| `config.yml` | ChunkManager | `plugins/ChunkManager/config.yml` |
| `config (1).yml` | Chunky | `plugins/Chunky/config.yml` |
| `config (2).yml` | ClearLag | `plugins/ClearLag/config.yml` |
| `config (3).yml` | CommandWhitelist | `plugins/CommandWhitelist/config.yml` |
| `config (4).yml` | GrimAC | `plugins/GrimAC/config.yml` |
| `config (5).yml` | InventoryRollbackPlus | `plugins/InventoryRollbackPlus/config.yml` |
| `config (6).yml` | PlaceholderAPI | `plugins/PlaceholderAPI/config.yml` |
| `config (7).yml` | ProtocolLib | `plugins/ProtocolLib/config.yml` |
| `rwd.yml` | RoseGarden/RWD | `plugins/RoseGarden/config.yml` |
| `config.json` | spark | `plugins/spark/config.json` |
| `config (8).yml` | Vault | `plugins/Vault/config.yml` |
| `config (9).yml` | ViaVersion | `plugins/ViaVersion/config.yml` |
| `config (10).yml` | ViaBackwards | `plugins/ViaBackwards/config.yml` |
| `configuration.json` | VoidGen | `plugins/VoidGen/configuration.json` |
| `config (11).yml` | WildStacker | `plugins/WildStacker/config.yml` |

## مشاكل حرجة قبل Public launch

### مصادقة السيرفر

`server.properties` يستخدم `online-mode=false` و`white-list=false` و`rate-limit=0`. هذا يتطلب مراجعة LoginSecurity وGeyser/Floodgate وطبقة الاستضافة قبل فتح السيرفر للعامة. لا نغيّر هذه القيم ضمن هذه المرحلة لأن تغييرها قد يمنع دخول Java أو Bedrock.

### ClearLag وWildStacker

ClearLag يحتوي `remove-entities: true`. وWildStacker يتعامل مع إزالة الكيانات والعناصر المتكدسة عند التنظيف. تشغيل نظامي إزالة دون سياسة مشتركة قد يحذف عناصر اللاعبين أو كيانات المزارع أو يسبب عدم اتساق مع الاقتصاد. لا نفعل ClearLag أو نرفع حدوده قبل اختبار staging.

### ChunkManager وChunky

يوجد ChunkManager مع prefetch وsmart unloading وpre-generation، ويوجد Chunky للتوليد المسبق. يجب ألا يعمل التوليد المسبق من النظامين في الوقت نفسه. نحتاج اختيار Chunky للتوليد المسبق، وترك ChunkManager لإدارة التحميل فقط بعد قياس TPS/MSPT.

### GrimAC وGeyser

GrimAC يحتوي فحوص Elytra، وGeyser/Floodgate يضيف طبقة Bedrock. أي تغيير في فحوص Elytra أو حزم ProtocolLib يحتاج اختبار Java وBedrock منفصل، ولا يجوز تعطيل الحماية اعتمادًا على شكوى lag فقط.

### الاقتصاد والتكامل

Vault هو طبقة API وليس اقتصادًا بحد ذاته. إعداد KSASMP يستخدم placeholders خاصة مثل `%economy_nicestMoney%` و`%economy_shards%`. يجب تحديد مصدر الاقتصاد الوحيد قبل ربط Essentials أو Vault؛ لا نفعّل اقتصادين متوازيين.

### Discord

`plugins/UltimateDonutSmp2/discord.yml` يحتوي Placeholder Webhook، وليس اعتمادًا صالحًا. لا يتم تفعيل Webhook أو Bot في هذه المرحلة، ولا توضع Tokens داخل GitHub.

## التكامل المستهدف

```text
Paper
├── LoginSecurity + Geyser + Floodgate   (الدخول)
├── LuckPerms                            (الصلاحيات)
├── CommandWhitelist                     (الأوامر المسموحة)
├── KSASMP ── Vault                      (اقتصاد/API بعد تحديد المصدر)
│    ├── PlaceholderAPI                  (عرض القيم)
│    ├── Essentials                       (أوامر عامة فقط، دون اقتصاد مكرر)
│    ├── FancyHolograms                  (عرض معلومات)
│    └── FancyNpcs                       (واجهات/نقاط دخول)
├── Multiverse-Core ── WorldGuard        (العوالم والحماية)
│    └── VoidGen                          (توليد العوالم الفارغة فقط)
├── Chunky                              (التوليد المسبق)
├── ChunkManager                         (التحميل/التفريغ بعد التحقق)
└── ViaVersion + ViaBackwards            (توافق الإصدارات)
```

`ProtocolLib` و`RoseGarden/RWD` و`InventoryRollbackPlus` و`spark` تبقى تكاملات مشروطة باختبار النسخة الفعلية من JAR وسجل التشغيل.

## ترتيب الإصلاح المعتمد

1. تثبيت مسارات الملفات وإزالة التكرار التنظيمي فقط.
2. تأمين الدخول والصلاحيات والأوامر.
3. اختيار اقتصاد واحد وربط Vault وPlaceholderAPI.
4. تثبيت العوالم وWorldGuard وVoidGen.
5. اختيار Chunky للتوليد المسبق ومنع التوليد المزدوج.
6. ضبط WildStacker/ClearLag ضد فقد العناصر.
7. إصلاح GUI وScoreboard وNPCs/Holograms.
8. تصميم Discord رسمي آمن بعد توفر Bot/Webhook مخزن بأمان.

## ما لن يتم فعله الآن

- لا تغيير لـ`online-mode`.
- لا تفعيل Discord أو Webhook.
- لا حذف Plugin.
- لا تعديل JAR.
- لا تعديل LuckPerms data أو قواعد البيانات يدويًا.
- لا تشغيل `/reload` على السيرفر العام.
- لا إعلان أن التوافق مع Java/Paper مؤكد بدون JAR و`latest.log`.
