---
title: Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával |} A Microsoft Docs
description: Üzenet törzsének tulajdonságok használatával eszköztelemetria irányíthatja a Blob storage és az Avro formátum a Blob storage írt adatok lekérdezéséhez.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: a5b8ce8cd753ee294a8d61ba8a3dfed872f0f31a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956325"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával

Ez a cikk ismerteti, hogyan lehet hatékonyan juthatnak az Azure-szolgáltatások az Azure IoT Hubból üzenetek az Avro-adatok lekérdezése. [Útválasztás üzenet](iot-hub-devguide-messages-d2c.md) lehetővé teszi, hogy részletes lekérdezéseket alapján üzenet tulajdonságait, üzenet szövegét, device twin címkék és eszköz-ikertulajdonságok használata az adatok szűréséhez. Az üzenet útválasztási lekérdezését képességeivel kapcsolatos további tudnivalókért tekintse meg a cikket, üzenet útválasztási lekérdezések szintaxisáról. 
<!--[Message Routing Query Syntax](iot-hub-devguide-routing-query-syntax.md). I don't have this article yet. -->

A kihívás, hogy, amikor az Azure IoT Hub üzeneteket irányítja az Azure Blob storage, az IoT Hub ír a tartalmat az Avro formátum, ami egy üzenet törzsének tulajdonság és a egy üzenet egyik tulajdonságát is van. Az IoT Hub adatok írása a Blob storage támogatja az Avro-adatok formátumát csak a, és bármely más végpontok nem használják ezt a formátumot. További információkért lásd: egy Azure Storage-tárolók használatával kapcsolatos cikket. Bár az Avro formátum kiválóan alkalmazható az adatok és az üzenet megőrzését, egy kérdés használatával adatokat lekérdezni. Ezzel szemben a JSON vagy CSV formátumban sokkal egyszerűbb, az adatok lekérdezése.

<!-- https://review.docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-messages-d2c?branch=pr-en-us-51566#azure-blob-storage  NEW LINK FOR 'WHEN USING STORAGE CONTAINERS' -->

Nem relációs big-data igények és formátumok és a nehézség, használhatja a big-data minták számos átalakítása, mind az adatok méretezés. A minták egyikét, "fizessen a lekérdezés" a lépéseknek az ismertetése, ez a cikk az Azure Data Lake Analytics. A lekérdezés a Hadoop és más megoldásokkal könnyen végrehajthat, bár a Data Lake Analytics van gyakran fejlesztéseink a "fizessen a lekérdezés" módszer. 

Van egy "információkinyerő" az avro-hoz a U-SQL-ben. További információkért lásd: [U-SQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples).

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Lekérdezés, és az Avro-adatok exportálása CSV-fájlba
Ebben a szakaszban az Avro-adatok lekérdezése és exportálása az Azure Blob storage-, CSV-fájlba, bár könnyen más tárházakban vagy adattárakon tudta elhelyezni az adatokat.

1. Állítsa be az Azure IoT Hub olyan tulajdonság megadásával az üzenet törzsében üzenetek találhatók útvonal adatokat egy Azure Blob storage-végponthoz.

   ![Az "Egyéni végpontok" szakasz](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![Az útválasztási szabályokat](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Útvonalak és az egyedi végpontok beállítása a további információkért lásd: [IoT hub üzenet-útválasztása](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub).

2. Győződjön meg arról, hogy az eszköz rendelkezik-e a kódolást, a tartalom típusa és a szükséges adatokat a Tulajdonságok vagy az üzenet szövegét, amint arra a termék dokumentációjában. Ezek az attribútumok megtekintése Device Explorer, az itt látható módon, ellenőrizheti, hogy helyesen vannak beállítva.

   ![Az Event Hub adat panel](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Állítsa be az Azure Data Lake Store-példány és a egy Data Lake Analytics-példányt. Az Azure IoT Hub nem irányíthatja a Data Lake Store, de egy Data Lake Analytics-példány kéri.

   ![Data Lake Store és Data Lake Analytics-példányok](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. A Data Lake Analytics egy kiegészítő tárolóként, ugyanezen a Blobtárolón belül, amely az Azure IoT Hub irányítja az adatok Azure Blob storage konfigurálja.

   ![Az "Adatforrások" panelen](./media/iot-hub-query-avro-data/query-avro-data-4.png)
 
5. Az a [U-SQL Avro példa](https://github.com/Azure/usql/tree/master/Examples/AvroExamples), négy DLL-fájlok van szüksége. Ezek a fájlok feltöltése a Data Lake Store-példány a hely.

   ![Négy feltöltött DLL-fájlok](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. A Visual Studióban hozzon létre egy U-SQL projekt.
 
   ! Hozzon létre egy U-SQL project](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Illessze be az újonnan létrehozott fájlt a következő parancsfájl tartalmát. Módosítsa a kiemelt három szakaszra osztható: a Data Lake Analytics-fiók, a kapcsolódó DLL-fájlok elérési útja és a tárfiók a helyes elérési útra.
    
   ![A három szakaszra osztható módosítani](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   A tényleges U-SQL parancsfájl egyszerű CSV-fájlból a kimeneti:
    
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

    A Data Lake Analytics szükséges, futtassa a következő szkriptet, amely legfeljebb 10 elemzési egységek volt, és 177 fájlok feldolgozása öt perc alatt. Az eredmény a CSV-fájl megjelenített kimenetre láthat az alábbi képen látható:
    
    ![A CSV-fájlból a kimeneti eredmények](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![CSV-fájlba konvertálja kimenet](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    A JSON elemzéséhez, folytassa a 8. lépés.
    
8. A legtöbb IoT-üzenetekhez JSON formátumban vannak. A következő sorokat ad hozzá, az üzenet JSON-fájlba, amely lehetővé teszi a WHERE záradék hozzáadása, és csak a szükséges adatokat kimeneti elemezhetők.

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

    A kimenet megjeleníti egy oszlopban lévő összes elem a `SELECT` parancsot. 
    
    ![A kimeneti oszlop minden elem megjelenítése](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan hatékonyan juthatnak az Azure-szolgáltatások az Azure IoT Hubból üzenetek az Avro-adatok lekérdezésére.

Teljes körű teljes körű megoldások, IoT Hub használó példákért lásd a [Azure IoT-Megoldásgyorsítók dokumentációja](../iot-accelerators/index.yml).

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutatójának](iot-hub-devguide.md).

Az IoT Hub üzenet-útválasztással kapcsolatos további információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub](iot-hub-devguide-messaging.md).
