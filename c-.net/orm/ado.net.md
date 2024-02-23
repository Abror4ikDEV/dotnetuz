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

##Coding qismi
Mana ADO.NET haqida qisqacha ma'lumotga ega bo'ldingiz. Endi quyida sizga kod misolida tushuntirishga harakat qilaman. Siz bilan quyidagi kodda Talaba nomli obektni database bilan bog'lashni o'rganamiz. Uni Console Application holatda tuzamiz.

***Ishni Talaba modeli tuzishdan boshlaymiz.***
```
// Bu qismda ADO NET uchun package o'rnatishimiz shart emas
namespace ADONET
{
    public class Talaba
    {
        public int Id { get; set; }
        public string Name { get; set; } = string.Empty;
        public string Surname { get; set; } = string.Empty;
        public byte Age { get; set; }
        public string Phone { get; set; } = string.Empty;
        public string Photo { get; set; } = string.Empty;
    }
}

```
***Biz SQLite Ma'lumotlar bazasidan foydalandik. Siz istalgan Databasedan foydalanishingiz mumkin!***
```
using Microsoft.Data.Sqlite; //ADO.NET uchun package
using ADONET; //Talaba Modelidan foydalanishimiz uchun namespace
namespace ADO.NET
{
    public class TalabaService 
    {
        const string CONNECTION_STRING = "Data source = AdoNet.db"; // Bu qismda bizning Connection Stringimiz yoziladi.
        SqliteConnection _connection = new SqliteConnection(CONNECTION_STRING); // Connection qismi. Databasega ulanishni ochishda pastroqda foydalanamiz.
        SqliteDataReader ?_reader; //Databasedan qaytgan ma'lumotlarimizni saqlash uchun tip.
        SqliteCommand _command = new(); // SQL commandlarni databasega yuborish uchun.
        public void Add(Talaba talaba) // Endi databasega ma'lumot qo'shish methodini tuzamiz
        {
            try // ADO.NET bilan ishlashda doimiy ravishda istisno holatlarni hisobga olishni maslahat beraman.
            {
                _connection.Open(); // Ulanish oqimini ochamiz.
                _command.CommandText = $"""
                INSERT INTO Talabas(Name, Surname, Age, Phone) Values('{talaba.Name}','{talaba.Surname}',{talaba.Age},'{talaba.Phone}')
                """; // SQLcommandni yozib olamiz. Agar undan ikki yoki undan ko'p foydalansak yana huddi shu buyruq orqali almashtiramiz.
                _command.ExecuteNonQuery(); // Endi Yozilgan buyrug'imizni run qilish uchun databasega yuboramiz. NonQuery ishlatamiz(Bunga yana to'xtalamiz)
            }
            catch (Exception ex) // Istisno holat.
            {
                Console.WriteLine(ex);
            }
	    finally{
                _connection.Close();// Nega aynan finally qismda ekaniga keyinroq to'xtalamiz
	    }
        }


        public void Delete(int id)
        {
            try
            {
                _connection.Open();//Connection ochish
                _command.CommandText = $"""
                DELETE from Talabas WHERE id = {id}
                """; // SQL Commandni alishtiramiz.
                _command.ExecuteNonQuery(); //NonQuery yordamida uni execute(run) qilamiz.
            }
            catch (Exception ex) //Istisno holat
            {
                Console.WriteLine(ex);
            }
	    finally{
                _connection.Close();// Nega aynan finally qismda ekaniga keyinroq to'xtalamiz
	    }
        }

        public List<Talaba> GetAll() // Databasedagi ma'lumotlarni list ko'rinishida yuborish buyrug'ini tuzamiz
        {
            List<Talaba> Talabalar = new(); // Return uchun List ochamiz.
            try
            {
                _connection.Open();
                _command.CommandText = "SELECT * from Talabas";
                _reader = _command.ExecuteReader(); // Endi commandni run qilishda NonQuerydan foydalanmayman. Sabab qaytuvchi malumot None emas.
                while (_reader.Read())
                {
                    Talaba talaba = new() // yangi obekt yasaymiz.
                    {
                        Id = _reader.GetInt32(0), // Qaytgan ma'lumotimizni 0 - indexida id turibdi biz uni int tipida get qilib olamiz
                        Name = _reader.GetString(1), // Qolgan qismlarda ham get buyruqlarini bajaramiz va tipini aytamiz.
                        Surname = _reader.GetString(2),
                        Age = _reader.GetByte(3),
                        Phone = _reader.GetString(4),
                    };
                    Talabalar.Add(talaba); // Listga qo'shamiz.
                }
            }
            catch(Exception ex)
            {
                Console.WriteLine(ex);
            }
	    finally{
                _connection.Close();// Nega aynan finally qismda ekaniga keyinroq to'xtalamiz
	    }
            return Talabalar;
        }
    }
}
```
##ExcecuteReader va ExcecuteNonQuery farqi##
Istalgan dassturlash tilida(ORMda ham) biz CRUD amallar bajarishda databasega Create Update Delete buyruqlarini yuborsak undan javob qaytmaydi. Yani natija none bo'ladi. None natijalar uchun esa biz ExcecuteNonQuery Methodidan foydalanamiz va uni biror o'zgaruvchiga saqlamaymiz. Lekin bizda ikkinchi qism ya'ni Read buyruq qolib ketdi. Biz databasedan istalgan malumotni olish uchun so'rov yuborsak va biz qandaydir natijani kutishimiz kerak bo'lsa ExcecuteReader Methodidan foydalanamiz va uni SqlDataReader Tipidagi o'zgaruvchiga saqlaymiz. Yuqoridagi kod bilan qayta tanishib chiqing va tushunishga harakat qiling.

##Nima uchun finally qismda Close qilamiz##
Biz dastur yozishda istisno holatlarni hisobla olish uchun try va catch operatoridan foydalandik. Aytaylik Connection ochish muvaffaqiyatli bajarildi ammo Command qismda hatolik bo'lsachi? Shu qismda dastur to'xtaydi ammo Command qismning pastida oqimni uzuvchi buyruq qolib ketadi. Demak biz uni tryda ham catchda ham yozishimiz kerak. Endi savol try tugasa ham catch tugasa ham bir hil ish bajarishimiz kerak ekan buni ikkalasiga yozishimiz shartmi? Albatta yo'q buni ikkalasiga tegishli bo'lgan finally yani istisno operatori tugassh qismiga yozamiz... 



Endi tuzilgan TalabaService dan dasturimizning istalgan qismida obekt olib ishlatishimiz mumkin. Sizlarga topshiriq esa o'zingiz bu kodni yozib, Program.cs faylini tuzish! 
