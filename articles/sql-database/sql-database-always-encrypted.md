---
title: Mindig titkosított - Windows tanúsítványtároló
description: Ez a cikk bemutatja, hogyan biztosíthatja a bizalmas adatokat egy SQL-adatbázisban adatbázis-titkosítással az SQL Server Management Studio (SSMS) Mindig titkosított varázslójával. Azt is bemutatja, hogyan tárolhatja a titkosítási kulcsokat a Windows tanúsítványtárolóban.
keywords: adatok titkosítása, sql titkosítás, adatbázis-titkosítás, bizalmas adatok, mindig titkosított
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviwer: ''
ms.date: 03/08/2019
ms.openlocfilehash: 82c3c3274a8a9d66019ce906ee7be47cedac7470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822045"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-the-windows-certificate-store"></a>Mindig titkosítva: Bizalmas adatok védelme és titkosítási kulcsok tárolása a Windows tanúsítványtárolóban

Ez a cikk bemutatja, hogyan biztosíthatja a bizalmas adatokat az SQL-adatbázisban adatbázis-titkosítással az [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx)Mindig titkosított [varázslójával.](https://msdn.microsoft.com/library/mt459280.aspx) Azt is bemutatja, hogyan tárolhatja a titkosítási kulcsokat a Windows tanúsítványtárolóban.

A Mindig titkosított egy új adattitkosítási technológia az Azure SQL Database és az SQL Server rendszerben, amely segít megvédeni a kiszolgálón lévő bizalmas adatokat az ügyfél és a kiszolgáló közötti mozgás során, és az adatok használata közben, biztosítva, hogy a bizalmas adatok soha ne jelenjenek meg egyszerű szöveget az adatbázisrendszeren belül. Az adatok titkosítása után csak a kulcsokhoz hozzáféréssel rendelkező ügyfélalkalmazások vagy alkalmazáskiszolgálók férhetnek hozzá a egyszerű szöveges adatokhoz. További információt a [Mindig titkosított (adatbázis-kezelő motor) című témakörben talál.](https://msdn.microsoft.com/library/mt163865.aspx)

Miután konfigurálta az adatbázist a Mindig titkosítva használatára, létrehoz egy ügyfélalkalmazást c# nyelven a Visual Studióval a titkosított adatokkal való együttműködésre.

Ebben a cikkben található lépéseket, hogy megtudja, hogyan állíthatja be mindig titkosítva egy Azure SQL-adatbázis. Ebben a cikkben megtudhatja, hogyan hajthatja végre a következő feladatokat:

* Az SSMS mindig titkosított varázslójával hozzon létre [mindig titkosított kulcsokat.](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3)
  * [Oszlopfőkulcs (CMK)](https://msdn.microsoft.com/library/mt146393.aspx)létrehozása .
  * [Oszloptitkosítási kulcs (CEK)](https://msdn.microsoft.com/library/mt146372.aspx)létrehozása.
* Hozzon létre egy adatbázistáblát, és titkosítsa az oszlopokat.
* Hozzon létre egy alkalmazást, amely beszúrja, kijelöli és megjeleníti a titkosított oszlopok adatait.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következőkre lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nem rendelkezik ilyen, iratkozzon fel egy [ingyenes próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/)
* [AZ SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242-es vagy újabb verziója.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson az > **Erőforrás-adatok létrehozása + Tárolás** >  **Create a resource****SQL-adatbázis elemre.**
3. **Hozzon** létre egy **Klinika** nevű üres adatbázist egy új vagy meglévő kiszolgálón. Az Azure Portalon való adatbázis-létrehozásról az [Első Azure SQL-adatbázis](sql-database-single-database-get-started.md)című témakörben talál részletes útmutatást.

    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted/create-database.png)

Szüksége lesz a kapcsolat string később az oktatóanyagban. Az adatbázis létrehozása után nyissa meg az új klinika adatbázist, és másolja a kapcsolati karakterláncot. A kapcsolati karakterláncot bármikor lekaphatja, de az Azure Portalon való másolása egyszerű.

1. Kattintson **az SQL adatbázisok** > **Klinika** > **adatbázis-kapcsolati karakterláncainak megjelenítése parancsra.**
2. Másolja a **ADO.NET**kapcsolati karakterláncát.

    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével

Nyissa meg az SSMS-t, és csatlakozzon a kiszolgálóhoz a klinika adatbázisával.

1. Nyissa meg az SSMS-t. (Kattintson az**Adatbázis-motor** **csatlakoztatása** > gombra a **Csatlakozás kiszolgálóhoz** ablak megnyitásához, ha nincs megnyitva).
2. Adja meg a kiszolgáló nevét és hitelesítő adatait. A kiszolgálónév megtalálható az SQL adatbázis panelen és a korábban másolt kapcsolati karakterláncban. Írja be a kiszolgáló teljes nevét *a database.windows.net*együtt.

    ![A kapcsolati sztring másolása](./media/sql-database-always-encrypted/ssms-connect.png)

Ha megnyílik az **Új tűzfalszabály** ablak, jelentkezzen be az Azure-ba, és hagyja, hogy az SSMS hozzon létre egy új tűzfalszabályt.

## <a name="create-a-table"></a>Tábla létrehozása

Ebben a szakaszban létrehoz egy táblázatot a betegek adatainak tárolására. Ez kezdetben egy normál tábla lesz - a titkosítást a következő szakaszban fogja konfigurálni.

1. Bontsa ki **az Adatbázisok csomópontot.**
2. Kattintson a jobb gombbal a **Klinika adatbázisára,** és válassza az Új **lekérdezés (Új lekérdezés) parancsra.**
3. Illessze be a következő Transact-SQL (T-SQL) az új lekérdezési ablakba, és **hajtsa végre.**

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

## <a name="encrypt-columns-configure-always-encrypted"></a>Oszlopok titkosítása (a Mindig titkosított beállítás konfigurálása)

Az SSMS egy varázslót biztosít a Mindig titkosítva konfigurálásához a CMK, A CEK és a titkosított oszlopok beállításával.

1. Bontsa ki **az Adatbázisok** > **klinikatábláinak****Clinic** > csomópontot.
2. Kattintson a jobb gombbal a **Betegek** táblára, és válassza az **Oszlopok titkosítása parancsot** a Mindig titkosított varázsló megnyitásához:

    ![Oszlopok titkosítása](./media/sql-database-always-encrypted/encrypt-columns.png)

A Mindig titkosított varázsló a következő szakaszokat tartalmazza: **Oszlopválasztás**, **Főkulcs-konfiguráció** (CMK), **Érvényesítés**és **Összegzés**.

### <a name="column-selection"></a>Oszlopkijelölés

Kattintson a **Tovább** gombra a **Bevezetés** lapon az **Oszlopkijelölés** lap megnyitásához. Ezen a lapon kiválaszthatja, hogy mely oszlopokat szeretné titkosítani, [milyen típusú titkosítást és milyen oszloptitkosítási kulcsot (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) kíván használni.

Titkosítsa **az SSN** és **birthdate** információkat minden beteg számára. Az **SSN** oszlop determinisztikus titkosítást fog használni, amely támogatja az egyenlőségi keresőket, az illesztéseket és a csoportosítást. A **BirthDate** oszlop véletlenszerű titkosítást fog használni, amely nem támogatja a műveleteket.

Állítsa az **SSN-oszlop** **titkosítási típusát** **determinisztikusra,** a **BirthDate** oszlopot **pedig randomizáltra.** Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Főkulcs konfigurációja

A **Főkulcs konfigurációja** lapon állíthatja be a CMK-t, és kiválaszthatja azt a kulcstároló-szolgáltatót, amelyen a CMK tárolója tárolóba kerül. Jelenleg tárolhat egy CMK-t a Windows tanúsítványtárolóban, az Azure Key Vaultban vagy egy hardveres biztonsági modulban (HSM). Ez az oktatóanyag bemutatja, hogyan tárolhatja a kulcsokat a Windows tanúsítványtárolóban.

Ellenőrizze, hogy a **Windows tanúsítványtároló** ki van-e jelölve, és kattintson a **Tovább**gombra.

![Főkulcs konfigurációja](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés

Titkosíthatja az oszlopokat most, vagy mentse a PowerShell-parancsfájlt, hogy később fusson. Ehhez az oktatóanyaghoz válassza a **Folytatás gombra a befejezéshez,** majd kattintson a **Tovább**gombra.

### <a name="summary"></a>Összefoglalás

Ellenőrizze, hogy a beállítások helyesek-e, és kattintson a **Befejezés** gombra a Mindig titkosított beállítás befejezéséhez.

![Összefoglalás](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>A varázsló műveletének ellenőrzése

A varázsló befejezése után az adatbázis mindig titkosítva. A varázsló a következő műveleteket hajtotta végre:

* Létrehozott egy CMK-t.
* Létrehozott egy CEK-t.
* A kijelölt oszlopoktitkosításkonfigurálása. A **Betegek** táblában jelenleg nincsenek adatok, de a kijelölt oszlopokban lévő adatok titkosítva vannak.

A kulcsok létrehozását az SSMS-ben ellenőrizheti, ha a **Klinika** > **Biztonsági Mindig** > **titkosított kulcsait fogja megküldeni.** Most már láthatja a varázsló által létrehozott új kulcsokat.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>A titkosított adatokkal működő ügyfélalkalmazás létrehozása

Most, hogy a Mindig titkosított beállítás be van állítva, létrehozhat egy olyan alkalmazást, amely *beszúrásokat* és *kijelöléseket* hajt végre a titkosított oszlopokon. A mintaalkalmazás sikeres futtatásához ugyanazon a számítógépen kell futtatnia, amelyen a Mindig titkosított varázslót futtatta. Az alkalmazás másik számítógépen való futtatásához telepítenie kell a mindig titkosított tanúsítványokat az ügyfélalkalmazást futtató számítógépre.  

> [!IMPORTANT]
> Az alkalmazásnak [SqlParameter objektumokat kell használnia,](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) amikor egyszerű szöveges adatokat ad át a kiszolgálónak mindig titkosított oszlopokkal. A literális értékek sqlparameter objektumok használata nélkül történő átadása kivételt eredményez.

1. Nyissa meg a Visual Studio alkalmazást, és hozzon létre egy új C# konzolalkalmazást. Győződjön meg arról, hogy a projekt **.NET Framework 4.6** vagy újabb.
2. Nevezze el a projektet **AlwaysEncryptedConsoleApp** és kattintson **az OK**gombra.

![Új konzolalkalmazás](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>A kapcsolati karakterlánc módosítása a Mindig titkosított engedélyezéshez

Ez a szakasz azt ismerteti, hogy miként engedélyezheti a Mindig titkosítva az adatbázis-kapcsolati karakterláncban. A következő szakaszban létrehozott konzolalkalmazást a következő szakaszban , "Mindig titkosított mintakonzolalkalmazás" szakaszban fogja módosítani.

A Mindig titkosított engedélyezése érdekében hozzá kell adnia az **Oszloptitkosítási beállítás** kulcsszót a kapcsolati karakterlánchoz, és engedélyeznie kell **az Engedélyezve beállítást.**

Ezt közvetlenül a kapcsolati karakterláncban állíthatja be, vagy az [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx)használatával is beállíthatja. A mintaalkalmazás a következő szakaszban bemutatja, hogyan kell használni **az SqlConnectionStringBuilder**.

> [!NOTE]
> Ez az egyetlen olyan módosítás, amely a Mindig titkosított ügyfélalkalmazásra vonatkozik. Ha van egy meglévő alkalmazás, amely tárolja a kapcsolati karakterlánc külsőleg (azaz egy konfigurációs fájl), akkor lehet, hogy engedélyezze mindig titkosított módosítása nélkül kódot.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Mindig titkosítva engedélyezése a kapcsolati karakterláncban

Adja hozzá a következő kulcsszót a kapcsolati karakterlánchoz:

    Column Encryption Setting=Enabled

### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Mindig titkosítva engedélyezése SqlConnectionStringBuilder segítségével

A következő kód bemutatja, hogyan engedélyezhető a Always Encrypted az [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) [beállításával engedélyezve.](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx)

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;

## <a name="always-encrypted-sample-console-application"></a>Mindig titkosított mintakonzolalkalmazás

Ez a minta bemutatja, hogyan:

* Módosítsa a kapcsolati karakterláncot a Mindig titkosított engedélyezéséhez.
* Adatok beszúrása a titkosított oszlopokba.
* Jelöljön ki egy rekordot egy titkosított oszlop adott értékének szűrésével.

Cserélje le **Program.cs** tartalmát a következő kódra. Cserélje le a kapcsolati karakterláncot a közvetlenül a fő metódus feletti sorban lévő globális connectionString változóhoz az Azure Portal érvényes kapcsolati karakterláncára. Ez az egyetlen változás, amit meg kell tennie, hogy ezt a kódot.

Futtassa az alkalmazást a Mindig titkosítva működés közben i.Run the app to see Always Encrypted in action.

```cs
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.SqlClient;
using System.Globalization;

namespace AlwaysEncryptedConsoleApp
{
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Data Source = SPE-T640-01.sys-sqlsvr.local; Initial Catalog = Clinic; Integrated Security = true";

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

            CultureInfo culture = CultureInfo.CreateSpecificCulture("en-US");
            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985", culture)
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993", culture)
            });


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
```

## <a name="verify-that-the-data-is-encrypted"></a>Az adatok titkosításának ellenőrzése

Gyorsan ellenőrizheti, hogy a kiszolgálón lévő tényleges adatok titkosítva vannak-e a **Betegek** adatainak SSMS-sel történő lekérdezésével. (Használja az aktuális kapcsolatot, ahol az oszloptitkosítási beállítás még nincs engedélyezve.)

Futtassa a következő lekérdezést a klinika adatbázisában.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Láthatja, hogy a titkosított oszlopok nem tartalmaznak egyszerű szöveges adatokat.

   ![Új konzolalkalmazás](./media/sql-database-always-encrypted/ssms-encrypted.png)

Ha az SSMS segítségével szeretné elérni az egyszerű szöveges adatokat, hozzáadhatja az **Oszloptitkosítási beállítás=engedélyezve paramétert** a kapcsolathoz.

1. Az SSMS-ben kattintson a jobb gombbal a kiszolgálóra az **Objektumkezelőben,** majd kattintson a **Kapcsolat bontása**parancsra.
2. Kattintson az**Adatbázis-motor** **csatlakoztatása** > gombra a **Csatlakozás kiszolgálóhoz** ablak megnyitásához, majd kattintson a **Beállítások gombra.**
3. Kattintson **a További kapcsolatparaméterek gombra,** és írja **be az Oszloptitkosítási beállítás=engedélyezve.**

    ![Új konzolalkalmazás](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. Futtassa a következő lekérdezést a **klinika adatbázisában.**

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     Most már láthatja az egyszerű szöveges adatokat a titkosított oszlopokban.

    ![Új konzolalkalmazás](./media/sql-database-always-encrypted/ssms-plaintext.png)

> [!NOTE]
> Ha egy másik számítógépről csatlakozik az SSMS-hez (vagy bármely ügyfélhez), akkor nem lesz hozzáférése a titkosítási kulcsokhoz, és nem tudja visszafejteni az adatokat.

## <a name="next-steps"></a>További lépések

A Mindig titkosított adatbázis létrehozása után a következőket teheti:

* Futtassa ezt a mintát egy másik számítógépről. Nem fér hozzá a titkosítási kulcsokhoz, így nem lesz hozzáférése az egyszerű szöveges adatokhoz, és nem fog sikeresen futni.
* [Forgassa el és tisztítsa meg a billentyűket](https://msdn.microsoft.com/library/mt607048.aspx).
* [A Mindig titkosított a már titkosított adatok áttelepítése.](https://msdn.microsoft.com/library/mt621539.aspx)
* [Mindig titkosított tanúsítványok telepítése más ügyfélgépekre](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (lásd a "Tanúsítványok elérhetővé tétele az alkalmazások és a felhasználók számára" című részt).

## <a name="related-information"></a>Kapcsolódó információk

* [Mindig titkosított (ügyfélfejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server titkosítás](https://msdn.microsoft.com/library/bb510663.aspx)
* [Mindig titkosított varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Mindig titkosított blog](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)