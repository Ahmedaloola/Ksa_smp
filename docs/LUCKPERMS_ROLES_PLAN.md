# KSA SMP — خطة LuckPerms والرتب

هذه الأوامر **مجهزة للنسخ إلى Console**، لكنها لم تُنفذ على السيرفر. لا تحذف الرتب القديمة قبل مراجعة الأعضاء والصلاحيات.

## الرتب النهائية

```text
default
member
ksa_plus
ksa_plus_plus
ksa_plus_plus_plus
media
admin
dev
owner
```

لا توجد رتبة لاعب باسم `ksa` في التصميم الجديد. الاسم الظاهر للاعبين يكون `KSA+` و`KSA++` و`KSA+++`، بينما الأسماء الداخلية تستخدم underscore لتجنب مشاكل الأوامر.

## إنشاء الرتب الجديدة

نفذ كل أمر مرة واحدة في Console:

```text
lp creategroup member
lp creategroup ksa_plus
lp creategroup ksa_plus_plus
lp creategroup ksa_plus_plus_plus
```

إذا ظهرت رسالة أن المجموعة موجودة، لا تحذفها ولا تعيد إنشاءها.

## الأوزان

```text
lp group default setweight 0
lp group member setweight 10
lp group ksa_plus setweight 30
lp group ksa_plus_plus setweight 40
lp group ksa_plus_plus_plus setweight 50
lp group media setweight 70
lp group admin setweight 85
lp group dev setweight 90
lp group owner setweight 100
```

## الوراثة الآمنة

رتب اللاعبين المدفوعة ترث Member فقط:

```text
lp group member parent set default
lp group ksa_plus parent set member
lp group ksa_plus_plus parent set ksa_plus
lp group ksa_plus_plus_plus parent set ksa_plus_plus
```

Media مستقلة ولا ترث رتبة KSA مدفوعة أو رتبة إدارية:

```text
lp group media parent set default
lp group admin parent set default
lp group dev parent set default
lp group owner parent set default
```

## Prefix

```text
lp group member meta setprefix 10 "&7Member &8» "
lp group ksa_plus meta setprefix 30 "&bKSA+ &8» "
lp group ksa_plus_plus meta setprefix 40 "&dKSA++ &8» "
lp group ksa_plus_plus_plus meta setprefix 50 "&6KSA+++ &8» "
lp group media meta setprefix 70 "&9Media &8» "
lp group admin meta setprefix 85 "&cAdmin &8» "
lp group dev meta setprefix 90 "&5Dev &8» "
lp group owner meta setprefix 100 "&4Owner &8» "
```

## صلاحيات المرحلة الأولى

لا نضيف صلاحيات إدارية الآن. الرتب الجديدة لا تحصل تلقائيًا على:

```text
*
op
luckperms.*
worldguard.*
essentials.*
ksasmp.admin
wildstacker.admin
minecraft.give
```

بعد إنشاء الرتب، سيتم ربط صلاحيات اللاعب الأساسية من خلال قائمة دقيقة بعد فحص `KSASMP`, `Essentials`, `CommandWhitelist`, و`WorldGuard`.

## سياسة Media

Media ليست رتبة مدفوعة ولا رتبة Staff. تكون مستقلة وتُمنح بعد مراجعة الإدارة لأحد الشروط:

- بث مباشر عن KSA SMP لمدة 30 دقيقة مع 5 مشاهدين حقيقيين على الأقل؛ أو
- فيديو عن KSA SMP يصل إلى 300 مشاهدة حقيقية مع ذكر السيرفر ووضع رابط Discord.

تكون المدة الأولى `Media Trial` لمدة 7 أيام، ثم يمكن تحويلها إلى Media دائمة بعد 3 منشورات مقبولة خلال 30 يومًا. هذه السياسة يدوية في المرحلة الأولى، ولا يوجد Bot أو API أو Webhook مفعّل.

## الرتب القديمة

لا تحذف حاليًا:

```text
ksa
ksa-plus
ksa-plus-plus
donutplus
donutplusplus
donutplusplusplus
```

يجب أولًا فحص الأعضاء والصلاحيات، ثم نقل الحسابات إلى الرتب الجديدة، ثم تعطيل القديمة أو حذفها في Commit منفصل.

## تحقق بعد التنفيذ

```text
lp listgroups
lp group member info
lp group ksa_plus info
lp group ksa_plus_plus info
lp group ksa_plus_plus_plus info
lp group media info
lp group admin info
lp group dev info
lp group owner info
```

## التراجع

لا تستخدم `clear`, `deletegroup`, أو `bulkupdate`. قبل أي نقل للأعضاء نحتاج نسخة من:

```text
lp export
```

ولا تنفذ هذه الأوامر أثناء وجود لاعبين أو أثناء ضغط على السيرفر.
