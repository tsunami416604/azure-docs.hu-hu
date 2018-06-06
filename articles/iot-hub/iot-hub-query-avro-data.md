---
title: Azure Data Lake Analytics használatával az Avro-adatok lekérdezése |} Microsoft Docs
description: Használatával üzenet törzse számára telemetriát útvonal blob-tároló és a lekérdezést az Avro formátum írni a blob-tároló.
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34727740"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>Az Avro adatait kérdezi le az Azure Data Lake Analytics használatával

Ez a cikk az Avro adatait hatékonyan irányításához az Azure IoT Hub üzeneteit Azure-szolgáltatásokhoz való használatáról. A következő blogbejegyzésben található a következő –[Az Azure IoT Hub üzenet útválasztás: most útválasztással a üzenet szövege], az Útválasztás a Tulajdonságok vagy az üzenettörzs IoT-Központ támogatja. Lásd még: [üzenettörzs útválasztás][Routing on message bodies]. 

A kérdés már, amikor Azure IoT Hub üzenetirányítást blob-tároló, IoT-központ ír a tartalmat az Avro formátum, ami az üzenet szövege és az üzenet tulajdonságai. Vegye figyelembe, hogy az IoT-központ csak támogatja az Avro adatformátum a tárolási blob adatainak írása, és ez a formátum nem használják-e más végpontok. Lásd: [Azure Storage tárolók használata esetén][When using Azure storage containers]. Az Avro formátum adatok/üzenet megőrzése nagyszerű, bár ez a beállítás az adatok lekérdezése. Összehasonlításképpen JSON vagy a fürt megosztott kötetei szolgáltatás formátuma sokkal egyszerűbb, mivel az adatok lekérdezése.

Oldja meg ezt, használhatja a big Data típusú adatok minták számos átalakítása és adatok skálázás nem relációs big Data típusú adatok igényeinek és formátumok. A minták, a "kell fizetnie, amennyit lekérdezésenként" minta egyike az Azure Data Lake Analytics (ADLA). Ez a cikk a fókusz. Abban az esetben, ha a lekérdezés a Hadoopba vagy az egyéb megoldások könnyen támadó, ADLA gyakran jobban van megfelel a "fizetési lekérdezésenként" megközelítést. Nincs olyan "készülék" az avro-hoz a U-SQL. Lásd: [U-SQL Avro – példa].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Lekérdezési és az Avro-adatok exportálása CSV-fájlba
A szakasz végigvezeti az Avro-adatok lekérdezése és az Azure Blob Storage CSV-fájlba való exportálásához abban az esetben, ha más tárházak találhatók, sem a adatokat tároló egyszerűen az adatok nem elhelyezni.

1. Azure IoT Hub beállítása az Azure Blob Storage endpoint üzeneteket az üzenet törzsében tulajdonsággal útvonal az adatokat.

    ![1/a. lépés képernyőfelvétel-készítés][img-query-avro-data-1a]

    ![1/b. lépés képernyőfelvétel-készítés][img-query-avro-data-1b]

2. Győződjön meg arról, az eszköznek a kódolást, a tartalom típusa és a Tulajdonságok vagy az üzenettörzs, amint a termék dokumentációjában a szükséges adatokat. Megtekintve eszköz Explorer (lásd alább), ellenőrizheti, hogy ezek az attribútumok helyesen van-e beállítva.

    ![2. lépés a képernyőfelvétel-készítés][img-query-avro-data-2]

3. Egy Azure Data Lake Store-(ADLS-) és az Azure Data Lake Analytics-példány beállítása. Azure IoT Hub nem irányítja az Azure Data Lake Store, amíg ADLA szükséges.

    ![3. lépés a képernyőfelvétel-készítés][img-query-avro-data-3]

4. ADLA konfigurálja az Azure Blob Storage tárolóként történő további, az azonos Azure IoT Hub továbbítja az adatokat a Blob-tároló.

    ![4. lépés a képernyőfelvétel-készítés][img-query-avro-data-4]
 
5. A bemutatott [U-SQL Avro – példa], nincsenek 4 dll-fájl szükséges.  Ezek a fájlok feltöltése a ADLS a hely.

    ![5. lépés a képernyőfelvétel-készítés][img-query-avro-data-5] 

6. A Visual Studio a U-SQL projekt létrehozása
 
    ![6. lépés a képernyőfelvétel-készítés][img-query-avro-data-6]

7. A következő parancsfájl tartalmát másolja és illessze be az újonnan létrehozott fájl. Módosítsa a 3 kiemelt szakaszok: ADLA fiókját, a DLL elérési utak és a helyes elérési utat a Storage-fiók.
    
    ![A lépés 7a képernyőfelvétel-készítés][img-query-avro-data-7a]

    A tényleges U-SQL parancsfájlt a fürt megosztott kötetei szolgáltatás egyszerű kimeneti:
    
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

    A lent látható módon parancsfájl futtatása ADLA 5 percig tartott 10 elemzési egység korlátozódik, és 177 fájlok feldolgozása, összefoglalójához a kimeneti CSV-fájlba.
    
    ![A lépés 7b képernyőfelvétel-készítés][img-query-avro-data-7b]

    A kimeneti megtekintése, megtekintheti az Avro tartalom konvertálta CSV-fájlba. Ha azt szeretné, hogy a JSON elemzése, folytassa a 8.
    
    ![7/c. lépés képernyőfelvétel-készítés][img-query-avro-data-7c]

    
8. A legtöbb IoT-üzenetek JSON formátumban vannak.  Ad hozzá az alábbi sorokat, akkor tudja értelmezni az üzenet JSON, az, hogy vegye fel a WHERE záradék csak kimeneti a szükséges adatokat.

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

9. A kimenet megtekintéséhez, ekkor megjelenik a kijelölési parancs minden eleméhez tartozó oszlopokat. 
    
    ![8. lépés a képernyőfelvétel-készítés][img-query-avro-data-8]

## <a name="next-steps"></a>További lépések
Ebben az oktatóprogramban megismerte hatékonyan irányításához az Azure-szolgáltatásokhoz való Azure IoT Hub érkező üzenetek az Avro-adatok lekérdezése.

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [megoldásgyorsító Azure IoT távoli megfigyelési][lnk-iot-sa-land].

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

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
