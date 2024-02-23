---
description: Abror Qurbonov
---
# ADO.NET

ADO.NET - C# tilida Database bilan bog'lanish uchun foydalanish mumkin bo'lgan framework. Ya'ni SQL Server, SQLite, MySQL, OracleDB va boshqa ma'lumotlar bazalariga bog'liq ma'lumotlar bilan ishlash uchun yordamchi funksiyalar va obyektlar qo'shish bilan ishlaydi. ORM ADO.NET ustiga qurilgan.

ADO.NET - .Net Framework versiyasidan qo'shilgan va ADO.NETning asosiy methodlari hozirda ***Microsoft.Data*** nomli package ichidan yozilgan.
##Foydalanish bo'yicha yo'riqnoma
Demak ADO.NET bilan ishlashda Microsoft.Data sinfidan foydalanishimiz kerakligini bildik. Endi savol tug'ulishi mumkin. Hamma Database uchun shu sinf ishlatiladimi...? Albatta yo'q!
Masalan men loyihamda SQLite Database tizimidan foydalanishni istayman. Lekin uni qanday using qilaman...? Endi biz Package ichidagi namespacelarga murojaat qilamiz. Masalan SQLite uchun quyidagi kodni kodimizga qo'shishimiz kerak.
```C#
using Microsoft.Data.Sqlite
```
Ushbu sinf bizga ma'lumotlar bazasi bilan ishlashda kerakli method va obektlardan foydalanishga yordam beradi. Yani usingdan so'ng Microsoft.Data. + (Database nomi) holatida yozamiz.


##Quyida ADO.NET uchun asosiy qismlari bilan tanishishingiz mumkin!
1. SqlConnection - Ma'lumotlar bazasiga (Databasega) ulanish uchun ishlatiladi. ADO.NETning bu qismidan Databasega ulanishni ochish va yopish uchun foydalanamiz.
	```c#
	SqlConnection connection = new SqlConnection(connectionString);
	connection.Open();
	//Your code
	connection.Close();
	```
2. SqlCommand - SQL so'rovlarini databasega yuborish uchun ishlatiladi. Ushbu obekt orqali orqali Sql so'rovlarni bajarish, saqlash yoki ma'lumot olib kelish uchun foydalanishingiz mumkin.
	Uning hususiy buyruqlari quyidagilar
	1. ExecuteNonQuery - Ma'lumotlarni o'zgartirmaydigan (masalan, INSERT, UPDATE, DELETE) SQL so'rovlarini bajarish uchun ishlatiladi.
	```
	connection.Open();
	SqlCommand command = new SqlCommand("INSERT INTO MyTable (Column1, Column2) VALUES (@Value1, @Value2)", connection);
	command.ExecuteNonQuery();
	connection.Close();
	```
	2. ExecuteReader - Ma'lumotlar bazasidan Ma'lumotni olib kelish uchun ishlatiladi. Ya'ni nomidan ma'lum bo'lganidek O'qish uchun ishlatiladi.
	```
	SqlDataReader reader = command.ExecuteReader();
	while (reader.Read())
    {
        Console.WriteLine(reader[0].ToString());
    }
	```
	3. ExecuteXmlReader: XML shaklida ma'lumotlarni olish uchun ishlatiladi. Bu, ExecuteReader metodining o'zgartirilgan versiyasidir va XML shaklida ma'lumotlar qaytaradi.
	4. ExecuteScalar: So'rov natijasida faqatgina bir maydon qiymatini qaytaradi. Masalan, SELECT COUNT(*) FROM MyTable so'rovi ExecuteScalar yordamida bajarganda, natija raqamning int ko'rinishida qaytariladi.
	```
	SqlCommand command = new SqlCommand("SELECT COUNT(*) FROM MyTable", connection);
	int count = (int)command.ExecuteScalar();
	```
3. SqlDataReader: Ma'lumotlar bazasidan ma'lumotlarni qatorlar halida olish uchun ishlatiladi. Ushbu obyekt orqali barcha qatorlarni olish, ularni qayta ishlash va o'chirish imkoniyatiga egasiz. Bu obektdan yuqorida ExcecuteReader methodini ishlatishda foydalandik.

4. DataSet: Ma'lumotlarni saqlash va ularga o'zgaruvchilardan kirish imkoniyatiga egasiz. Ushbu obyekt orqali ma'lumotlar bazasidan olingan ma'lumotlarni saqlash va ularga kerakli o'zgaruvchilardan kirish imkoniyatiga egasiz.

5. DataTable: Ma'lumotlarni ustunlar va qatorlar jadvalidagi ma'lumotlar sifatida saqlash uchun ishlatiladi. Ushbu obyekt orqali ma'lumotlar bazasidan olingan ma'lumotlarni saqlash va ularga qat'iyroq kirish imkoniyatiga egasiz.

6. SqlDataAdapter: Ma'lumotlar bazasidan ma'lumotlar olish va ularni saqlash uchun ishlatiladi. Ushbu obyekt orqali ma'lumotlar bazasidan ma'lumotlarni olish, ularga o'zgartirishlar qilish va yangi ma'lumotlarni qo'shish imkoniyatiga egasiz.
