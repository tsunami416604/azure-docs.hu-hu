---
title: Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával |} Microsoft Docs
description: Üzenet törzsének tulajdonságok használatával telemetriát irányíthatja a Blob storage és az Avro formátum a Blob storage írt adatok lekérdezése.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: c56b567498047ee996018675134c252ec1de7e0c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081368"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával

A cikkből megtudhatja, hatékonyan továbbítani az Azure-szolgáltatásokhoz való Azure IoT Hub üzeneteit Avro adatok lekérdezése. Szerint azt a blogbejegyzés [Az Azure IoT Hub üzenet útválasztás: most útválasztással a üzenet szövege], az Útválasztás a Tulajdonságok vagy az üzenettörzs IoT-Központ támogatja. További információkért lásd: [üzenettörzs útválasztás][Routing on message bodies]. 

A kérdés már, amikor Azure IoT Hub üzenetirányítást végez az Azure Blob storage, az IoT-központ ír a tartalmat az Avro formátum, ami az üzenet törzse tulajdonság, és egy üzenet tulajdonság. Az IoT-Központ támogatja a Blob Storage az adatok írásakor csak Avro adatformátum, és ezt a formátumot nem lesznek felhasználva az egyéb végpontok. További információkért lásd: [Azure Storage tárolók használata esetén][When using Azure storage containers]. Bár az Avro formátum adatok és az üzenet megőrzése nagyszerű, továbbra is használhatja adatait kihívást. Összehasonlításképpen JSON vagy a fürt megosztott kötetei szolgáltatás formátuma sokkal egyszerűbb, mivel az adatok lekérdezése.

Kezelje a nem relációs big data igényeinek és formázza az adathordozót, és ez a probléma megoldásához, használhatja a big data-minták számos átalakítása és skálázás adatokat. Ez a cikk célja azoknak az Azure Data Lake Analytics egy mintát, "kell fizetnie, lekérdezésenként". Bár a lekérdezés Hadoop vagy egyéb megoldások egyszerűen végrehajtható, Data Lake Analytics gyakran jobban van megfelel a "fizetési lekérdezésenként" megközelítést. 

Nincs olyan "készülék" az avro-hoz a U-SQL. További információkért lásd: [U-SQL Avro – példa].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Lekérdezési és az Avro-adatok exportálása CSV-fájlba
Ebben a szakaszban az Avro-adatok lekérdezése és exportálni onnan egy CSV-fájl az Azure Blob Storage tárolóban, bár könnyen más tárházak találhatók, sem a adatokat tároló nem elhelyezni az adatokat.

1. Azure IoT Hub útvonal az adatokat az Azure Blob storage endpoint által létrehozott üzeneteket az üzenet törzsében tulajdonsággal.

    ![A "Custom végpontok" szakasz][img-query-avro-data-1a]

    ![Az útvonalak parancs][img-query-avro-data-1b]

2. Győződjön meg arról, hogy az eszköz rendelkezik-e a kódolás tartalomtípus és a Tulajdonságok vagy az üzenettörzs, amint a termék dokumentációjában a szükséges adatokat. Ezek az attribútumok megtekintéséhez eszköz Explorer, az itt látható módon, ellenőrizheti, hogy helyesen vannak beállítva.

    ![A központ eseményadatok ablaktábla][img-query-avro-data-2]

3. Állítsa be az Azure Data Lake Store-példány és egy Data Lake Analytics-példányt. Azure IoT-központ nem lehet közvetlenül a Data Lake Store-példányra, de egy Data Lake Analytics-példányt az egyik szükséges.

    ![Data Lake Store és a Data Lake Analytics-példányok][img-query-avro-data-3]

4. A Data Lake Analytics konfigurálása az Azure Blob Storage tárolóban tárolóként történő további, az azonos Azure IoT Hub továbbítja az adatokat a Blob-tároló.

    ![A "Adatforrások" ablak][img-query-avro-data-4]
 
5. A bemutatott [U-SQL Avro – példa], négy DLL-fájlok van szüksége. Ezek a fájlok feltöltése a Data Lake Store-példány a hely.

    ![Négy feltöltött DLL-fájlok][img-query-avro-data-5] 

6. A Visual Studio a U-SQL projekt létrehozása.
 
    ![U-SQL projekt létrehozása][img-query-avro-data-6]

7. Az újonnan létrehozott fájlba illessze be a következő parancsfájl tartalmát. Módosítsa a három kiemelt szakasz: Data Lake Analytics-fiókja, a kapcsolódó dll-fájl elérési útvonalat és a helyes elérési utat a storage-fiók.
    
    ![A módosítani kívánt három szakasz][img-query-avro-data-7a]

    A tényleges U-SQL parancsfájl egyszerű kimeneti CSV-fájlba:
    
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
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    A Data Lake Analytics tartott öt percet futtassa az alábbi parancsfájlt, amely 10 elemzési egység korlátozódott, és 177 fájlok feldolgozása. Az eredmény a CSV-fájl megjelenített kimenetre láthat az alábbi ábrán látható:
    
    ![A CSV-fájl a kimeneti eredmények][img-query-avro-data-7b]

    ![A kimeneti alakítja át a CSV-fájl][img-query-avro-data-7c]

    A JSON elemzése, folytassa a 8.
    
8. A legtöbb IoT-üzenetek JSON formátumban vannak. Adja hozzá az alábbi sorokat, az üzenet, amely lehetővé teszi a WHERE záradék hozzáadása, és csak a szükséges adatokat a kimeneti JSON fájlba tudja értelmezni.

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
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

    A kimenet megjeleníti az egyes elemekhez tartozó oszlop a `SELECT` parancsot. 
    
    ![A kimeneti oszlop az egyes elemek megjelenítése][img-query-avro-data-8]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudta, hogyan hatékonyan továbbítani az Azure-szolgáltatásokhoz való Azure IoT Hub üzeneteit Avro adatokat lekérdezni.

Teljes végpontok közötti megoldások, amelyek használják az IoT-központot, tekintse meg a [megoldásgyorsító Azure IoT távoli megfigyelési][lnk-iot-sa-land].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

Az üzenetet az IoT hubon útválasztási kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging].

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Az Azure IoT Hub üzenet útválasztás: most útválasztással a üzenet szövege]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro – példa]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
