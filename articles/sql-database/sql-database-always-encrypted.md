---
title: 'Always Encrypted: Az Azure SQL Database - Windows-tanúsítványtároló |} A Microsoft Docs'
description: Ez a cikk bemutatja, hogyan az adatbázis-titkosítást az SQL-adatbázisban tárolt bizalmas adatok védelme az Always Encrypted varázsló az SQL Server Management Studio (SSMS) használatával. Bemutatja azt is, hogyan lehet a Windows-tanúsítványtároló tárolja a titkosítási kulcsokat.
keywords: Always Encrypted adatok, sql-titkosítás, az adatbázis-titkosítás, bizalmas adatok titkosítása
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
manager: craigg
ms.date: 11/07/2018
ms.openlocfilehash: de8bb0adf45a7cdaf9cc0d9a451af203e01e87da
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564874"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Always Encrypted: Bizalmas adatok védelme és a titkosítási kulcsok tárolása a Windows-tanúsítványtároló

Ez a cikk bemutatja, hogyan teheti biztonságossá a bizalmas adatokat az SQL-adatbázis, az adatbázis-titkosítás segítségével a [Always Encrypted varázsló](https://msdn.microsoft.com/library/mt459280.aspx) a [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Bemutatja azt is, hogyan lehet a Windows-tanúsítványtároló tárolja a titkosítási kulcsokat.

Always Encrypted az Azure SQL Database-ben új adatok titkosítási technológia, amely segít az SQL Server a kiszolgálón, az inaktív bizalmas adatok védelme, ügyfél és kiszolgáló közötti mozgás során és az adatok használata közben, annak biztosítása, hogy a bizalmas adatok soha nem jelenik meg belül az adatbázis-rendszer egyszerű szövegként. Miután adatokat titkosít, csak az ügyfélalkalmazások vagy alkalmazáskiszolgálók, a kulcsokhoz hozzáférő érhető el egyszerű szöveges adatokat. Részletes információkért lásd: [Always Encrypted (adatbázismotor)](https://msdn.microsoft.com/library/mt163865.aspx).

Miután Always Encrypted használni kívánt adatbázis, a titkosított adatok használatát a Visual studióval C#-ban egy ügyfélalkalmazás fog létrehozni.

Ebből a cikkből megtudhatja, hogyan állíthatja be az Always Encrypted az Azure SQL database kövesse. Ebből a cikkből megtudhatja, hogyan hajthat végre a következő feladatokat:

* Az Always Encrypted varázsló segítségével az ssms-ben hozzon létre [mindig a titkosított kulcsok](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Hozzon létre egy [oszlop Master Key (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Hozzon létre egy [Oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Hozzon létre egy adatbázistábla soraihoz, és titkosítani az oszlopok.
* Hozzon létre egy alkalmazást, amely beszúrja, kiválasztása, és a titkosított oszlopokat adatait jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek
A jelen oktatóanyag esetében lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nincs fiókja, regisztráljon egy [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 verzió vagy újabb.
* [.NET-keretrendszer 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (az ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **erőforrás létrehozása** > **adatok + tárolás** > **SQL Database**.
3. Hozzon létre egy **üres** nevű adatbázis **Clinic** egy új vagy meglévő kiszolgálóra. Adatbázis létrehozása az Azure Portalon részletes utasításokért lásd: [az első Azure SQL database](sql-database-get-started-portal.md).
   
    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted/create-database.png)

Az oktatóanyag későbbi részében szüksége lesz a kapcsolati karakterláncot. Az adatbázis létrehozása után nyissa meg az új Clinic adatbázist, és másolja a kapcsolati karakterláncot. A kapcsolati karakterláncot a bármikor megtekintheti, de egyszerűen másolja azt, ha Ön az Azure Portalon.

1. Kattintson a **SQL-adatbázisok** > **Clinic** > **adatbázis kapcsolati karakterláncainak megjelenítése**.
2. Másolja a kapcsolati karakterláncot a **ADO.NET**.
   
    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével
Nyissa meg az ssms-ben, és csatlakozzon a Clinic adatbázist tartalmazó kiszolgálón.

1. Nyissa meg az SSMS-t. (Kattintson **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, ha nem látható).
2. Adja meg a kiszolgáló nevét és hitelesítő adatokat. Az SQL-adatbázis panelen található a kiszolgáló nevét, és a kapcsolati karakterláncban korábban vágólapra másolt. Írja be a teljes kiszolgáló neve például *database.windows.net*.
   
    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted/ssms-connect.png)

Ha a **Új tűzfalszabály** ablak megnyílik, jelentkezzen be Azure-ban, és lehetővé teszik az ssms-ben hozzon létre egy új tűzfalszabályt az Ön számára.

## <a name="create-a-table"></a>Tábla létrehozása
Ebben a szakaszban létrehozhat egy táblát, amely a betegek adatokat tárolja. Ez kezdetben--lesz egy normál tábla titkosítási fogja beállítani a következő szakaszban.

1. Bontsa ki a **adatbázisok**.
2. Kattintson a jobb gombbal a **Clinic** adatbázis, és kattintson a **új lekérdezés**.
3. Az új lekérdezési ablakban illessze be a következő Transact-SQL (T-SQL) és **Execute** azt.

        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>(Az Always Encrypted konfigurálása) oszlop titkosítása
SSMS egy varázslón könnyen konfigurálhatja az Always Encrypted által az Ön számára a CMK CEK és titkosított oszlopokat beállítását.

1. Bontsa ki a **adatbázisok** > **Clinic** > **táblák**.
2. Kattintson a jobb gombbal a **betegek** táblázatot, majd **titkosítása oszlopok** az Always Encrypted varázsló megnyitásához:
   
    ![Oszlopok titkosítása](./media/sql-database-always-encrypted/encrypt-columns.png)

Az Always Encrypted varázsló az alábbi szakaszokat tartalmazza: **Oszlopválasztás**, **főkulcs konfigurációs** (CMK), **érvényesítési**, és **összefoglalás**.

### <a name="column-selection"></a>Oszlop kiválasztása
Kattintson a **tovább** a a **bemutatása** megnyitásához a **Oszlopválasztás** lapot. Ezen a lapon kiválaszthatja titkosítására, mely oszlopok [titkosítás, típusa és milyen oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) használatára.

Titkosítása **SSN** és **születési** egyes betegek adatait. A **SSN** oszlop determinisztikus titkosítás, mely támogatja a egyenlőség kereséseket, illesztés és csoportosítás fogja használni. A **születési** oszlop véletlenszerű titkosítás, amely nem támogatja a műveletek fogja használni.

Állítsa be a **titkosítási típus** számára a **SSN** oszlop **Deterministic** és a **születési** oszlop **Randomized** . Kattintson a **tovább**.

![Oszlopok titkosítása](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>A főkulcs konfiguráció
A **főkulcs konfigurációs** lap, amelyen a CMK beállítása és a kulcstároló-szolgáltató kiválasztása a CMK tárolásához. Jelenleg egy CMK tárolhatja a Windows-tárolóba, az Azure Key Vault vagy egy hardveres biztonsági modul (HSM). Az oktatóanyag bemutatja, hogy a kulcsokat a Windows-tárolóban található.

Ellenőrizze, hogy **Windows-tanúsítványtároló** van kijelölve, kattintson **tovább**.

![A főkulcs konfiguráció](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés
Most titkosítani az oszlopokat, vagy később futtatni egy PowerShell-parancsfájl mentése. A jelen oktatóanyag esetében válassza ki a **most gombra a folytatáshoz** kattintson **tovább**.

### <a name="summary"></a>Összegzés
Győződjön meg arról, hogy a beállítások helyességét, és kattintson a **Befejezés** az Always Encrypted a telepítés befejezéséhez.

![Összegzés](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Ellenőrizze a varázsló műveletek
A varázsló befejezése után az adatbázis beállítása az Always Encrypted. A varázsló a következő műveletek végre:

* Létrehozott egy CMK.
* Egy CEK létrehozása.
* A kijelölt oszlopokat a titkosításhoz konfigurálva. A **betegek** tábla jelenleg nem tartalmaz adatokat, de a kijelölt oszlopokban szereplő összes meglévő adat már titkosítva van.

Az ssms-ben a kulcsok létrehozásának ellenőrzéséhez a **Clinic** > **biztonsági** > **mindig a titkosított kulcsok**. Most már megtekintheti az új kulcsokat, a varázsló az Ön számára létrehozott.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Egy ügyfélalkalmazás, amely együttműködik a titkosított adatok létrehozása
Most, hogy az Always Encrypted be van állítva, hozhat létre olyan alkalmazás, amely végrehajtja *szúr be* és *kiválasztja* meg a titkosított oszlopokat. A mintaalkalmazás futtatása sikeres, futtatnia kell, ugyanazon a számítógépen, ahol futtatta az Always Encrypted varázsló. Futtassa az alkalmazást egy másik számítógépen, az ügyfél-alkalmazást futtató számítógépre kell telepíteni az Always Encrypted tanúsítványokat.  

> [!IMPORTANT]
> Az alkalmazás kell használnia [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objektumok egyszerű szöveges adatokat a Always Encrypted oszlopokkal rendelkező kiszolgáló történő átadásakor. Szöveges értékek passing SqlParameter objektumok használata nélkül kivételt eredményez.
> 
> 

1. Nyissa meg a Visual Studiót, és hozzon létre egy új C# konzolalkalmazást. Ellenőrizze, hogy a projekt értéke **.NET Framework 4.6** vagy újabb.
2. Adja a projektnek **AlwaysEncryptedConsoleApp** kattintson **OK**.

![Új Konzolalkalmazás](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Always Encrypted engedélyezéséhez a kapcsolati karakterlánc módosítása
Ez a szakasz ismerteti, hogyan Always Encrypted engedélyezéséhez az adatbázis-kapcsolati karakterláncot. A "Always Encrypted minta-Konzolalkalmazás.", a következő szakaszban létrehozott konzolalkalmazást fog módosítani

Always Encrypted engedélyezéséhez hozzá kell a **oszlop titkosítási beállítás** kulcsszó használatával a kapcsolati karakterláncot, és állítsa be **engedélyezve**.

Közvetlenül a kapcsolati karakterláncban állíthat, vagy beállíthatja a egy [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A következő szakaszban szereplő mintaalkalmazás bemutatja, hogyan **SqlConnectionStringBuilder**.

> [!NOTE]
> Ez az az egyetlen változás az Always Encrypted az adott ügyfélalkalmazás megadása kötelező. Ha egy meglévő alkalmazást, amely tárolja a kapcsolati karakterlánc külsőleg (azt jelenti, egy konfigurációs fájlban), valószínűleg létre tudja engedélyezéséhez az Always Encrypted bármilyen kód átírása nélkül.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Engedélyezze az Always Encrypted a kapcsolati karakterlánc
Adja hozzá a következő kulcsszó a kapcsolati karakterlánc:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Mindig titkosítva egy SqlConnectionStringBuilder engedélyezése
A következő kód bemutatja, hogyan lehet engedélyezni az Always Encrypted beállításával a [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) való [engedélyezve](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Always Encrypted minta-Konzolalkalmazás
Ez a minta azt ismerteti, hogyan lehet:

* Always Encrypted engedélyezéséhez a kapcsolati karakterlánc módosítása.
* Adatok beszúrása a titkosított oszlopokat.
* Válassza ki egy rekordot egy megadott értéket egy titkosított oszlop szűrésével.

Cserélje le a tartalmát **Program.cs** az alábbi kódra. A sor fölé a Main metódushoz a connectionString globális változó a kapcsolati karakterláncot cserélje le az Azure Portalon érvényes kapcsolati karakterláncra. Ez az az egyetlen változás, győződjön meg arról, hogy ez a kód kell.

Always Encrypted megtekintéséhez működés közben az alkalmazás futtatásához.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## <a name="verify-that-the-data-is-encrypted"></a>Győződjön meg arról, hogy az adatok titkosítása
Gyorsan ellenőrizheti, hogy a tényleges adatokat a kiszolgáló titkosított lekérdezésével a **betegek** adatokon az ssms használatával. (Használja az aktuális kapcsolatot, az oszlop titkosítási beállítás még nincs engedélyezve.)

Futtassa a következő lekérdezést a Clinic adatbázison.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Láthatja, hogy a titkosított oszlopokban nem tartalmaz egyszerű szöveges adatokat.

   ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-encrypted.png)

Az SSMS használatához az egyszerű szöveges adatok is hozzáadhat a **oszlop titkosítási beállítás = engedélyezve** paramétert annak a kapcsolatnak.

1. Az ssms-ben, kattintson a jobb gombbal a kiszolgáló **Object Explorer**, és kattintson a **Disconnect**.
2. Kattintson a **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, és kattintson **beállítások**.
3. Kattintson a **további kapcsolódási paraméterek** , és írja be **oszlop titkosítási beállítás = engedélyezve**.
   
    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Futtassa a következő lekérdezést a a **Clinic** adatbázis.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Most már megtekintheti a titkosított oszlopokban az egyszerű szöveges adatokat.

    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Ha az ssms-ben (vagy bármely ügyfél) egy másik számítógépről csatlakozik, nem fog tudni hozzáférni a titkosítási kulcsokhoz, és nem tudják fejteni az adatokat.
> 
> 

## <a name="next-steps"></a>További lépések
Miután létrehozott egy adatbázist, amely használja az Always Encrypted, érdemes a következőket:

* A minta futtatásához egy másik számítógépről. Azt nem kell a titkosítási kulcsokat a hozzáférést, így nem fog tudni hozzáférni az egyszerű szöveges adatokat, és nem futtathatók sikeresen.
* [Elforgatás és karbantartása a kulcsok](https://msdn.microsoft.com/library/mt607048.aspx).
* [Az Always Encrypted használatával titkosított adatok migrálása](https://msdn.microsoft.com/library/mt621539.aspx).
* [Always Encrypted tanúsítványok telepítése a többi ügyfél gépek](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (lásd a "Tétele tanúsítványok érhető el az alkalmazások és-felhasználók" szakaszt).

## <a name="related-information"></a>Kapcsolódó információk
* [Always Encrypted (ügyféloldali fejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server Encryption](https://msdn.microsoft.com/library/bb510663.aspx)
* [Always Encrypted varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Mindig titkosított Blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

