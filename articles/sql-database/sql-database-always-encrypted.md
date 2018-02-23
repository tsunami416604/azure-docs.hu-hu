---
title: "Mindig titkosítja: Azure SQL Database - Windows-tanúsítványtároló |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan bizalmas adatok védelmének az adatbázis-titkosítás az SQL-adatbázisban az SQL Server Management Studio (SSMS) mindig titkosítja varázsló használatával. Azt is bemutatja, hogyan tárolja a titkosítási kulcsok a Windows tanúsítványtárolójában."
keywords: "Mindig titkosítja adatok, sql-titkosítás, az adatbázis-titkosítás, bizalmas adatok titkosításához"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: cgronlun
ms.assetid: ce7e052e-8bf6-4d7c-9204-4c6f4afeba4b
ms.service: sql-database
ms.custom: security
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: sstein
ms.openlocfilehash: 8e86648195811a666a197b6ee06ad610a1c8d568
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Mindig titkosítja: Az SQL-adatbázis bizalmas adatok védelmét, és a titkosítási kulcsok tárolása a Windows tanúsítványtároló

A cikkből megtudhatja, mennyire biztonságos a bizalmas adatokat az SQL-adatbázisok adatbázis-titkosítás segítségével a [varázsló mindig titkosítja](https://msdn.microsoft.com/library/mt459280.aspx) a [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). Azt is bemutatja, hogyan tárolja a titkosítási kulcsok a Windows tanúsítványtárolójában.

Mindig titkosított technológiája új adatok titkosítása az Azure SQL-adatbázis, amely segít az SQL Server a kiszolgálón, inaktív bizalmas adat védelme adatátviteli ügyfél és kiszolgáló közötti és az adatok használata közben, bizalmas adatok soha nem biztosítására adatbázis rendszerében szövegként jelenik meg. Adatok titkosítása csak az ügyfélalkalmazások vagy a kulcsoknak access app kiszolgálók is férnek hozzá a titkosítatlan szöveges adatokat. Részletes információkért lásd: [(adatbázismotor) mindig titkosítja](https://msdn.microsoft.com/library/mt163865.aspx).

Miután mindig titkosítja az adatbázist, a C# a Visual Studio, a titkosított adatok hoz létre egy ügyfélalkalmazást.

Kövesse a cikkben megtudhatja, hogyan állíthat be mindig titkosítja az Azure SQL-adatbázis. Ebből a cikkből megtudhatja, hogyan a következő feladatok végezhetők el:

* A varázslóval mindig titkosítja az SSMS létrehozásához [mindig a titkosított kulcsok](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Hozzon létre egy [oszlop főkulcs (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Hozzon létre egy [oszlop titkosítási kulcsának (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Hozzon létre egy adatbázis tábláinak és oszlopok titkosításához.
* Hozzon létre egy alkalmazást, amely beszúrja, kiválasztása és a titkosított oszlopokban adatait jeleníti meg.

## <a name="prerequisites"></a>Előfeltételek
Ebben az oktatóanyagban lesz szüksége:

* Azure-fiók és -előfizetés. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 13.0.700.242 verzió vagy újabb.
* [.NET-keretrendszer 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) vagy újabb (az ügyfélszámítógépen).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **hozzon létre egy erőforrást** > **adatok + tárolás** > **SQL-adatbázis**.
3. Hozzon létre egy **üres** nevű adatbázis **klinikán** egy új vagy meglévő kiszolgálóra. Adatbázis létrehozása az Azure portálon részletes utasításokért lásd: [az első Azure SQL-adatbázis](sql-database-get-started-portal.md).
   
    ![Hozzon létre egy üres adatbázist](./media/sql-database-always-encrypted/create-database.png)

Az oktatóanyag későbbi részében szüksége lesz a kapcsolati karakterláncot. Az adatbázis létrehozását követően nyissa meg az új klinikán adatbázishoz, és másolja a kapcsolati karakterláncot. A kapcsolati karakterlánc kaphat bármikor, de könnyen másolásához, amikor az Azure portálon.

1. Kattintson a **SQL-adatbázisok** > **klinikán** > **adatbázis-kapcsolati karakterláncok megjelenítése**.
2. Másolja a kapcsolati karakterláncot a **ADO.NET**.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével
Nyissa meg a szolgáltatáshoz az SSMS, és csatlakozzon a kiszolgálóhoz, az egészségügyi ellátó intézmény adatbázissal.

1. Nyissa meg a szolgáltatáshoz az SSMS. (Kattintson **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, ha még nincs nyitva).
2. Adja meg a kiszolgáló nevét és hitelesítő adatait. Az SQL-adatbázis paneljén található a kiszolgáló nevét, és a kapcsolati karakterláncban korábban kimásolt. Írja be a teljes kiszolgáló neve például *database.windows.net*.
   
    ![Másolja a kapcsolati karakterláncot](./media/sql-database-always-encrypted/ssms-connect.png)

Ha a **Új tűzfalszabály** ablakban nyitja meg, jelentkezzen be Azure, és lehetővé teszik az SSMS, hozzon létre egy új tűzfalszabályt.

## <a name="create-a-table"></a>Tábla létrehozása
Ebben a szakaszban egy beteg adatokat tároló tábla hoz létre. Ez kezdetben--lesz normál tábla titkosítási konfigurál a következő szakaszban.

1. Bontsa ki a **adatbázisok**.
2. Kattintson a jobb gombbal a **klinikán** adatbázis, és kattintson a **új lekérdezés**.
3. Illessze be a következő Transact-SQL (T-SQL) az új lekérdezési ablakba és **Execute** azt.

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


## <a name="encrypt-columns-configure-always-encrypted"></a>Titkosítani az oszlopok (mindig titkosítja konfigurálása)
SSMS egy varázslón keresztül történő egyszerű beállítását mindig titkosítja a CMK CEK és titkosított oszlopokat beállítása az Ön által lehetővé.

1. Bontsa ki a **adatbázisok** > **klinikán** > **táblák**.
2. Kattintson a jobb gombbal a **betegek** tábla, és válassza ki **titkosítása oszlopok** mindig titkosítja varázsló megnyitásához:
   
    ![Oszlopok titkosítása](./media/sql-database-always-encrypted/encrypt-columns.png)

A mindig titkosítja varázsló az alábbi szakaszokat tartalmazza: **Oszlopválasztás**, **főkulcs konfigurációs** (CMK) **érvényesítési**, és **összegzés**.

### <a name="column-selection"></a>Oszlop kiválasztása
Kattintson a **tovább** a a **bemutatása** lapon nyissa meg a **Oszlopválasztás** lap. Ezen a lapon kiválaszthatja titkosítására, oszlopok [a típusú titkosítást, és milyen oszlop titkosítási kulcsának (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) használatára.

Titkosítani **SSN** és **születési dátumot** minden türelmet adatait. A **SSN** oszlop determinisztikus titkosítás, mely támogatja a egyenlőség keresések, társítások és csoportosítás fogja használni. A **születési dátumot** oszlop véletlenszerű titkosítás, mely nem támogatja a műveleteket fogja használni.

Állítsa be a **titkosítási típus** a a **SSN** oszlop **Deterministic** és a **születési dátumot** oszlop **Randomized**. Kattintson a **Tovább** gombra.

![Oszlopok titkosítása](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>A főkulcs konfiguráció
A **főkulcs konfigurációs** lap ahol állítsa be a CMK, és válassza ki a kulcstároló-szolgáltatóval a CMK tárolásához. Jelenleg egy CMK tárolhatja a Windows tanúsítványtárolóban, az Azure Key Vault vagy egy hardveres biztonsági modul (HSM). Ez az oktatóanyag bemutatja a kulcsok tárolása Windows tanúsítványtárolójába.

Ellenőrizze, hogy **Windows tanúsítványtároló** van kiválasztva, és kattintson a **következő**.

![A főkulcs konfiguráció](./media/sql-database-always-encrypted/master-key-configuration.png)

### <a name="validation"></a>Ellenőrzés
Most titkosítani az oszlopokat, vagy mentse később futtatni egy PowerShell-parancsfájlt. A jelen oktatóanyag esetében válassza ki a **Befejezés most már továbbléphet** kattintson **következő**.

### <a name="summary"></a>Összegzés
Ellenőrizze, hogy a beállítások helyességét, és kattintson a **Befejezés** mindig titkosítja az a telepítés befejezéséhez.

![Összegzés](./media/sql-database-always-encrypted/summary.png)

### <a name="verify-the-wizards-actions"></a>Ellenőrizze a varázsló műveletek
A varázsló befejezése után az adatbázis be van állítva mindig titkosítja. A varázsló a következő műveletek végre:

* Létrehozott egy CMK.
* Létrehozott egy CEK.
* A kijelölt oszlopokat a titkosításhoz konfigurálva. A **betegek** tábla jelenleg nem tartalmaz adatokat, de a meglévő adatokat a kijelölt oszlopokban szereplő most már titkosítva van.

A szolgáltatáshoz az ssms kulcsok létrehozása megnyitásával ellenőrizheti **klinikán** > **biztonsági** > **mindig a titkosított kulcsok**. Most láthatja a varázsló által létrehozott, új kulcsokkal.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Hozzon létre egy ügyfélalkalmazást, amely kompatibilis a titkosított adatok
Most, hogy mindig titkosítja be van állítva, egy alkalmazás, amely elvégzi hozhat létre *beszúrása* és *kiválasztja* a titkosított oszlopok. Sikeresen futtassa a mintaalkalmazást, futtatnia kell az ugyanazon a számítógépen, ahol futtatta a mindig titkosítja varázslót. Futtassa az alkalmazást egy másik számítógépen, mindig titkosítja tanúsítványait az ügyfél alkalmazást futtató számítógépre kell telepítenie.  

> [!IMPORTANT]
> Az alkalmazást kell használnia [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) objektumok számára történő átadásakor adatokat egyszerű szöveges oszlopok mindig titkosítja a kiszolgálót. Értékek szövegkonstansban SqlParameter objektumok használata nélkül átadja azt eredményezi, hogy kivételt.
> 
> 

1. Nyissa meg a Visual Studio, és hozzon létre egy új C# konzolalkalmazást. Győződjön meg arról, hogy a projekt értéke **.NET-keretrendszer 4.6** vagy újabb.
2. Nevet a projektnek **AlwaysEncryptedConsoleApp** kattintson **OK**.

![Új Konzolalkalmazás](./media/sql-database-always-encrypted/console-app.png)

## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Ahhoz, hogy mindig titkosítja a kapcsolati karakterlánc módosítása
Ez a szakasz azt ismerteti, hogyan is engedélyezhető az mindig titkosítja az adatbázis-kapcsolati karakterlánc. A "Mindig titkosítja minta-Konzolalkalmazás." a következő szakaszban létrehozott Konzolalkalmazás fog módosítani

Mindig titkosítja engedélyezéséhez kell hozzáadnia a **Oszloptitkosítási beállítás** kulcsszót a kapcsolati karakterláncot, és állítsa az értékét **engedélyezve**.

Ez közvetlenül a kapcsolódási karakterláncban állíthatja be, vagy használatával állíthatja be a [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). A mintaalkalmazást a következő szakasz bemutatja, hogyan használható **SqlConnectionStringBuilder**.

> [!NOTE]
> Ez az ügyfélalkalmazás adott mindig titkosítja a szükséges mindössze annyi a változás. Ha egy meglévő alkalmazást, amely tárolja a kapcsolati karakterlánc külsőleg (Ez azt jelenti, hogy a konfigurációs fájlban), valószínűleg létre tudja ahhoz, hogy mindig titkosítja a programkód módosítása nélkül.
> 
> 

### <a name="enable-always-encrypted-in-the-connection-string"></a>Engedélyezze a mindig titkosítja a kapcsolódási karakterláncban
A kapcsolati karakterláncot adja hozzá a következő kulcsszó:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Egy SqlConnectionStringBuilder mindig titkosítva engedélyezése
A következő kód bemutatja, hogyan lehet engedélyezni a úgy, hogy mindig titkosítja a [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) való [engedélyezve](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Mindig titkosított minta-Konzolalkalmazás
Ez a példa bemutatja, hogyan:

* Módosítsa a kapcsolati karakterlánc ahhoz, hogy mindig titkosítja.
* Adatok beszúrása a titkosított oszlopokat.
* Válasszon ki egy olyan rekordot szűrést használ a megadott titkosított oszlop az értéket.

Cserélje le a tartalmát **Program.cs** a következő kóddal. Cserélje le a kapcsolati karakterláncot a globális connectionString változó a sorban fölött a fő metódus a érvényes kapcsolati karakterláncot az Azure portálról. Ez akkor tegye ezt a kódot kell mindössze annyi a változás.

Futtassa az alkalmazást, hogy mindig titkosítja a művelet.

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
Gyorsan ellenőrizheti, hogy a tényleges adatokat a kiszolgáló titkosított lekérdezésével a **betegek** ssms alkalmazásával adatokat. (Az aktuális kapcsolat használatát ahol a oszloptitkosítási beállítás még nincs engedélyezve.)

Futtassa a következő lekérdezést a klinikán adatbázison.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

Láthatja, hogy a titkosított oszlop nem tartalmaz az egyszerű szöveges adatokat.

   ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-encrypted.png)

Az egyszerű szöveges adatok eléréséhez használja az SSMS, adja hozzá a **Oszloptitkosítási beállítás = engedélyezve** paraméter a kapcsolatra.

1. Az SSMS, kattintson a jobb gombbal a kiszolgáló **Object Explorer**, és kattintson a **Disconnect**.
2. Kattintson a **Connect** > **adatbázismotor** megnyitásához a **kapcsolódás a kiszolgálóhoz** ablakot, és kattintson **beállítások**.
3. Kattintson a **további kapcsolódási paraméterek** és típus **Oszloptitkosítási beállítás = engedélyezve**.
   
    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-connection-parameter.png)
4. A következő lekérdezés futtatása a **klinikán** adatbázis.
   
        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   
     Most már megtekintheti a titkosított oszlopokban az egyszerű szöveges adatokat.

    ![Új Konzolalkalmazás](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [!NOTE]
> Ha a szolgáltatáshoz az SSMS (vagy bármely olyan ügyfél) egy másik számítógépről csatlakozik, nem kell férnie a titkosítási kulcsokhoz, és nem tudják fejteni az adatokat.
> 
> 

## <a name="next-steps"></a>További lépések
Miután létrehozott egy adatbázist, amely mindig titkosítja használ, érdemes lehet tegye a következőket:

* A minta futtatásához egy másik számítógépen. A titkosítási kulcsoknak access azt nem rendelkezik, ezért nem lesz a titkosítatlan szöveges adataihoz való hozzáférés, és nem futtathatók sikeresen.
* [Forgassa el, és a kulcsok tisztítása](https://msdn.microsoft.com/library/mt607048.aspx).
* [Már mindig titkosítja a titkosított adatok áttelepítése](https://msdn.microsoft.com/library/mt621539.aspx).
* [Mindig titkosítja tanúsítványok telepítése a más ügyfélgépek](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (lásd az "Így tanúsítványok érhető el az alkalmazások és felhasználók").

## <a name="related-information"></a>Kapcsolódó információk
* [Mindig titkosítja (ügyféloldali fejlesztés)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Átlátható adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx)
* [SQL Server Encryption](https://msdn.microsoft.com/library/bb510663.aspx)
* [Mindig titkosított varázsló](https://msdn.microsoft.com/library/mt459280.aspx)
* [Mindig titkosított Blog](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

