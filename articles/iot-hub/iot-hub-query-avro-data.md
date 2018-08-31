---
title: Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával |} A Microsoft Docs
description: Üzenet törzsének tulajdonságok használatával eszköztelemetria irányíthatja a Blob storage és az Avro formátum a Blob storage írt adatok lekérdezéséhez.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: asrastog
ms.openlocfilehash: a17df39c55b5c02c83e3f0b74a91d7109ddb4d3d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188944"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Az Avro-adatok lekérdezése az Azure Data Lake Analytics használatával

Ez a cikk ismerteti, hogyan lehet hatékonyan juthatnak az Azure-szolgáltatások az Azure IoT Hubból üzenetek az Avro-adatok lekérdezése. Az ebben a blogbejegyzésben bejelentettük, [Az Azure IoT Hub üzenet-útválasztása: mostantól az üzenettörzs a Útválasztás], az IoT Hub által támogatott tulajdonságok vagy az üzenet törzse az Útválasztás. További információkért lásd: [az üzenet törzse az Útválasztás][Routing on message bodies]. 

A kihívás, hogy, amikor az Azure IoT Hub üzeneteket irányítja az Azure Blob storage, az IoT Hub ír a tartalmat az Avro formátum, ami egy üzenet törzsének tulajdonság és a egy üzenet egyik tulajdonságát is van. Az IoT Hub adatok írása a Blob storage támogatja az Avro-adatok formátumát csak a, és bármely más végpontok nem használják ezt a formátumot. További információkért lásd: [használata az Azure Storage-tárolók esetén][When using Azure storage containers]. Bár az Avro formátum kiválóan alkalmazható az adatok és az üzenet megőrzését, egy kérdés használatával adatokat lekérdezni. Ezzel szemben a JSON vagy CSV formátumban sokkal egyszerűbb, az adatok lekérdezése.

Nem relációs big-data igények és formátumok és a nehézség, használhatja a big-data minták számos átalakítása, mind az adatok méretezés. A lépéseknek az ismertetése, ez a cikk az Azure Data Lake Analytics egy mintát, "fizessen a lekérdezést,". A lekérdezés a Hadoop és más megoldásokkal könnyen végrehajthat, bár a Data Lake Analytics van gyakran fejlesztéseink a "fizessen a lekérdezés" módszer. 

Van egy "információkinyerő" az avro-hoz a U-SQL-ben. További információkért lásd: [U-SQL az Avro-példa].

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Lekérdezés, és az Avro-adatok exportálása CSV-fájlba
Ebben a szakaszban az Avro-adatok lekérdezése és exportálása az Azure Blob storage-, CSV-fájlba, bár könnyen más tárházakban vagy adattárakon tudta elhelyezni az adatokat.

1. Állítsa be az Azure IoT Hub olyan tulajdonság megadásával az üzenet törzsében üzenetek találhatók útvonal adatokat egy Azure Blob storage-végponthoz.

    ![Az "Egyéni végpontok" szakasz][img-query-avro-data-1a]

    ![Az útvonalak parancs][img-query-avro-data-1b]

2. Győződjön meg arról, hogy az eszköz rendelkezik-e a kódolást, a tartalom típusa és a szükséges adatokat a Tulajdonságok vagy az üzenet szövegét, amint arra a termék dokumentációjában. Ezek az attribútumok megtekintése Device Explorer, az itt látható módon, ellenőrizheti, hogy helyesen vannak beállítva.

    ![Az Event Hub adat panel][img-query-avro-data-2]

3. Állítsa be az Azure Data Lake Store-példány és a egy Data Lake Analytics-példányt. Az Azure IoT Hub nem irányíthatja a Data Lake Store, de egy Data Lake Analytics-példány kéri.

    ![Data Lake Store és Data Lake Analytics-példányok][img-query-avro-data-3]

4. A Data Lake Analytics egy kiegészítő tárolóként, ugyanezen a Blobtárolón belül, amely az Azure IoT Hub irányítja az adatok Azure Blob storage konfigurálja.

    ![Az "Adatforrások" panelen][img-query-avro-data-4]
 
5. Az [U-SQL az Avro-példa], négy DLL-fájlok van szüksége. Ezek a fájlok feltöltése a Data Lake Store-példány a hely.

    ![Négy feltöltött DLL-fájlok][img-query-avro-data-5] 

6. A Visual Studióban hozzon létre egy U-SQL projekt.
 
    ![U-SQL projekt létrehozása][img-query-avro-data-6]

7. Illessze be az újonnan létrehozott fájlt a következő parancsfájl tartalmát. Módosítsa a kiemelt három szakaszra osztható: a Data Lake Analytics-fiók, a kapcsolódó DLL-fájlok elérési útja és a tárfiók a helyes elérési útra.
    
    ![A három szakaszra osztható módosítani][img-query-avro-data-7a]

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

    A Data Lake Analytics szükséges, futtassa a következő szkriptet, amely legfeljebb 10 elemzési egységek volt, és 177 fájlok feldolgozása öt perc alatt. Az eredmény a CSV-fájl megjelenített kimenetre láthat az alábbi képen látható:
    
    ![A CSV-fájlból a kimeneti eredmények][img-query-avro-data-7b]

    ![CSV-fájlba konvertálja kimenet][img-query-avro-data-7c]

    A JSON elemzéséhez, folytassa a 8. lépés.
    
8. A legtöbb IoT-üzenetekhez JSON formátumban vannak. A következő sorokat ad hozzá, az üzenet JSON-fájlba, amely lehetővé teszi a WHERE záradék hozzáadása, és csak a szükséges adatokat kimeneti elemezhetők.

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

    A kimenet megjeleníti egy oszlopban lévő összes elem a `SELECT` parancsot. 
    
    ![A kimeneti oszlop minden elem megjelenítése][img-query-avro-data-8]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan hatékonyan juthatnak az Azure-szolgáltatások az Azure IoT Hubból üzenetek az Avro-adatok lekérdezésére.

Teljes körű teljes körű megoldások, IoT Hub használó példákért lásd [Azure IoT távoli figyelési megoldásgyorsító][lnk-iot-sa-land].

Az IoT Hub megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [Az IoT Hub fejlesztői útmutató].

Az IoT Hub üzenet-útválasztással kapcsolatos további információkért lásd: [küldhet és fogadhat üzeneteket az IoT Hub][lnk-devguide-messaging].

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
[Az Azure IoT Hub üzenet-útválasztása: mostantól az üzenettörzs a Útválasztás]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL az Avro-példa]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.yml
[Az IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
