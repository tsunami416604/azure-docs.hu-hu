---
title: Avro-adatok lekérdezése az Azure Data Lake Analytics használatával | Microsoft dokumentumok
description: Az üzenettörzs tulajdonságaival irányítsa az eszköz telemetriai adatait a Blob storage-ba, és kérdezze le a Blob storage-ba írt Avro formátumadatokat.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 92fc5bb88ff5efd8fe1a8cd61be833b3984b673a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605619"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Avro-adatok lekérdezése az Azure Data Lake Analytics használatával

Ez a cikk bemutatja, hogyan lehet lekérdezni az Avro-adatokat az Azure IoT Hubból az Azure-szolgáltatásokba való üzenetek hatékony továbbításához. [Az Üzenetátirányítás](iot-hub-devguide-messages-d2c.md) lehetővé teszi az adatok szűrését az üzenet tulajdonságai, az üzenettörzs, az ikereszköz-címkék és az ikereszköz-tulajdonságok alapján. Az Üzenetútrendszer lekérdezési lehetőségeiről az [üzenettovábbítási lekérdezés szintaxisáról](iot-hub-devguide-routing-query-syntax.md)szóló cikkben olvashat bővebben.

A kihívás az volt, hogy amikor az Azure IoT Hub útvonalak üzeneteket Az Azure Blob storage, alapértelmezés szerint az IoT Hub írja a tartalmat Avro formátumban, amely rendelkezik egy üzenet törzs tulajdonság és egy üzenet tulajdonság. Az Avro formátum nem használható más végpontokhoz. Bár az Avro formátum kiválóan alkalmas az adatok és az üzenetek megőrzésére, kihívást jelent az adatok lekérdezésére. Összehasonlításképpen, JSON vagy CSV formátumsokkal könnyebb az adatok lekérdezése. Az IoT Hub mostantól támogatja az adatok írását a Blob storage-ba a JSON-ban és az AVRO-ban.

További információ: [Az Azure Storage használata útválasztási végpontként](iot-hub-devguide-messages-d2c.md#azure-storage)című témakörben talál.

A nem relációs big data-igények és -formátumok kezeléséhez és a kihívás megoldásához számos big data-mintát használhat az adatok átalakításához és méretezéséhez. Az egyik minták, "pay per query", az Azure Data Lake Analytics, amely a cikk középpontjában. Bár a lekérdezés ta- ha a Hadoopban vagy más megoldásokban egyszerűen végrehajthatja, a Data Lake Analytics gyakran jobban megfelel ehhez a "lekérdezésenkénti fizetés" megközelítéshez.

Van egy "elszívó" az Avro az U-SQL. További információ: [U-SQL Avro example.](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro-adatok lekérdezése és exportálása CSV-fájlba

Ebben a szakaszban lekérdezi az Avro-adatokat, és exportálja őket egy CSV-fájlba az Azure Blob storage-ban, bár könnyedén elhelyezheti az adatokat más tárolókban vagy adattárakban.

1. Állítsa be az Azure IoT Hub ot, hogy az adatokat egy Azure Blob-tárolási végpontra irányítsa az üzenettörzs egyik tulajdonságának használatával az üzenetek kijelöléséhez.

   ![Az "Egyéni végpontok" szakasz](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Az útválasztási szabályok](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Az útvonalakat és az egyéni végpontokat a Message [Routing for an IoT hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)című témakörben talál.

2. Győződjön meg arról, hogy az eszköz rendelkezik a kódolással, a tartalomtípussal és a szükséges adatokkal a tulajdonságokban vagy az üzenettörzsben, ahogy arra a termék dokumentációja hivatkozik. Amikor ezeket az attribútumokat az Eszközkezelőben tekinti meg, az itt látható módon ellenőrizheti, hogy megfelelően vannak-e beállítva.

   ![Az Eseményközpont adatai ablaktábla](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Hozzon létre egy Azure Data Lake Store-példányt és egy Data Lake Analytics-példányt. Az Azure IoT Hub nem irányítja a Data Lake Store-példány, de a Data Lake Analytics-példány igényel egy.

   ![Data Lake Store és Data Lake Analytics-példányok](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. A Data Lake Analytics konfigurálása Azure Blob storage, mint egy további áruház, ugyanazt a Blob storage, amely az Azure IoT Hub útvonalak adatokat.

   ![Az "Adatforrások" ablaktábla](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Ahogy azt az [U-SQL Avro példában tárgyalta,](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)négy DLL-fájlra van szükség. Töltse fel ezeket a fájlokat a Data Lake Store-példány egy helyére.

   ![Négy feltöltött DLL-fájl](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. A Visual Studióban hozzon létre egy U-SQL-projektet.

   ! U-SQL-projekt létrehozása](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Illessze be a következő parancsfájl tartalmát az újonnan létrehozott fájlba. Módosítsa a három kiemelt szakaszt: a Data Lake Analytics-fiókot, a társított DLL-fájlelérési utakat és a tárfiók helyes elérési útját.

   ![A módosítandó három szakasz](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   A tényleges U-SQL script egyszerű kimenet egy CSV fájl:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    A Data Lake Analytics-nek öt percbe telt a következő parancsfájl futtatása, amely 10 analitikus egységre korlátozódott, és 177 fájlt feldolgozott fel. Az eredmény a CSV-fájl kimenetében jelenik meg, amely az alábbi képen jelenik meg:

    ![A CSV-fájlkimenet eredményei](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![CsV-fájllá konvertált kimenet](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    A JSON elemzéséhez folytassa a 8.

8. A legtöbb IoT-üzenet JSON fájlformátumban van. A következő sorok hozzáadásával elemezheti az üzenetet egy JSON-fájlba, amely lehetővé teszi a WHERE záradékok hozzáadását és csak a szükséges adatok kimenetét.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    A kimenet a parancs minden `SELECT` egyes eleméhez egy oszlopot jelenít meg.

    ![Minden elemoszlopot megjelenítő kimenet](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kérdezheti le az Avro-adatokat az Azure IoT Hubból az Azure-szolgáltatásokba való üzenetek hatékony továbbításához.

Az IoT Hubot használó teljes körű megoldások példáit az [Azure IoT-megoldásgyorsítók dokumentációjában találja.](/azure/iot-accelerators)

Ha többet szeretne megtudni a megoldások IoT Hubkal való fejlesztéséről, tekintse meg az [IoT Hub fejlesztői útmutatóját.](iot-hub-devguide.md)

Ha többet szeretne tudni az üzenetek útválasztásáról az IoT Hubban, olvassa el az [Üzenetek küldése és fogadása az IoT Hubbal.](iot-hub-devguide-messaging.md)
