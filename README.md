اخد نسخة من كل العمليات اللي حصلت في الداتا بيز بعد آخر Full Backup.
الهدف: تفضي ملف الـ LDF + ترجع الداتا لنقطة زمنية بالدقيقة.
لازم Full Backup الأول 
مينفعش تاخد Log وانت معندكش Full Backup. لازم الترتيب: Full > وبعدين Log

هو نسخة احتياطية بتاخدها من "دفتر العمليات" بتاع SQL Server.تخيل الداتا بيز كأنها كتاب. كل ما تعمل Insert, Update, Delete... SQL بيكتبها الأول في دفتر اسمه Transaction Log وبعدين ينقلها للكتاب الأساسي.
1-- Transaction Log Backup - 
ALTER DATABASE [YourDB]
SET RECOVERY FULL;
GO

BACKUP DATABASE [YourDB]
TO DISK = 'D:\Backup\YourDB_Full.bak'
WITH INIT, COMPRESSION;
GO

BACKUP LOG [YourDB]
TO DISK = 'D:\Backup\YourDB_Log.trn'
WITH INIT, COMPRESSION, STATS = 10;
GO

2--jop
عشان ا   DF ميملاش الهارد 
ملف الـ Transaction Log بيفضل يكبر مع كل عملية Insert/Update/Delete. لو سبته هيكبر لحد ما الهارد يتملي والسيرفر يقع. 
الـ Job بياخد منه نسخة كل شوية ويفضيه تلقائي.عشان السيرفر ميقفش 
لو انت اللي هتاخد الـ Backup بايدك كل 15 دقيقة، مش هتعرف تشتغل. 
الـ Job بيشتغل Background يعني: انت شغال عادي، اليوزر شغال عادي، وهو بينزل ياخد النسخة ويرجع من سكات.عشان الـ Recovery Point-in-Time 
باختصار: 
Background Job = تسيبه يشتغل لوحده كل X دقايق عشان يحميك من 2 كارثة: الهارد يتملي، والداتا تضيع.ت
