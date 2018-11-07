---
title: Ismerkedés a blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (a Webjobs projekteket) |} A Microsoft Docs
description: Első lépések a Blob storage használatával a WebJob-projektben a Visual Studio használatával egy Azure Storage csatlakoztatása után a szolgáltatások csatlakoztatva.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 5a7c16e6ac565d1660fee02cb7df178344b195e7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254400"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure Blob storage és a Visual Studio csatlakoztatott szolgáltatásainak (webjobs-feladat projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk a C#-mintakódot biztosít, amelyek bemutatják, hogyan indítható el egy folyamatot, ha egy Azure-blobból létrehozásakor vagy frissítésekor. A Kódminták használja a [WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki) verzió 1.x. Hozzáadásakor egy tárfiókot, a webjobs-feladat projekt a Visual Studio használatával **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanelen a megfelelő Azure Storage NuGet-csomag telepítve van, a megfelelő .NET hivatkozások hozzáadódnak a projektet, és a storage-fiókhoz tartozó kapcsolati karakterláncok frissülnek az App.config fájlban.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Hogyan indítható el egy függvényt, ha egy blob létrehozásakor vagy frissítésekor
Ez a szakasz bemutatja, hogyan használhatja a **BlobTrigger** attribútum.

 **Megjegyzés:** a WebJobs SDK megvizsgálja a fájlokat az új vagy módosított blobok megtekintése. Ez a folyamat rendszer természetüknél fogva lassabbak; függvény előfordulhat, hogy nem első indul el, amíg több percet vagy hosszabb a blob létrehozása után.  Az alkalmazása azonnal blobok feldolgozásához szükséges, ha az ajánlott módszer az üzenetsori üzenet létrehozására, amikor a blob létrehozásához, és használja a **QueueTrigger** attribútum helyett a **BlobTrigger** a függvény, amely feldolgozza a blob attribútum.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Egyetlen helyőrző kiterjesztésű blob neve
Az alábbi kódmintában megjelenő szöveg blobok másolja át a *bemeneti* tárolót, hogy a *kimeneti* tároló:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Az attribútum konstruktorának paramétereként egy karakterlánc-paramétert, amely meghatározza a tároló nevének és a blob nevét. Ebben a példában a blob neve *Blob1.txt* jön létre a *bemeneti* tárolót, a függvény létrehoz egy blobot, nevű *Blob1.txt* a a *kimeneti* tároló.

A blob neve helyőrzővel egy mintát adhat meg az alábbi kódmintában látható módon:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ez a kód csak blobok kezdve "eredeti-" nevű másolja. Például *eredeti-Blob1.txt* a a *bemeneti* tároló másolódik *másolási-Blob1.txt* a a *kimeneti* tároló.

Adja meg egy olyan blobneveket, amelyek a kapcsos zárójelek között van a nevében a mintát kell, ha duplán kapcsos zárójeleket kell használni. Például, ha meg szeretne keresni a blobok a *lemezképek* tároló, amely hasonló névvel rendelkeznek:

        {20140101}-soundfile.mp3

Ez a minta használata:

        images/{{20140101}}-{name}

A példában a *neve* helyőrző érték *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Külön blob-névnek és kiterjesztésnek helyőrzői
Az alábbi kódmintában módosul a fájl kiterjesztése a blobok megjelenő másolja a *bemeneti* tárolót, hogy a *kimeneti* tároló. A kód kiterjesztését naplózza a *bemeneti* blob-, és beállítja a kiterjesztését a *kimeneti* blobból *.txt*.

        public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
            [Blob("output/{name}.txt")] out string output,
            string name,
            string ext,
            TextWriter logger)
        {
            logger.WriteLine("Blob name:" + name);
            logger.WriteLine("Blob extension:" + ext);
            output = input.ReadToEnd();
        }

## <a name="types-that-you-can-bind-to-blobs"></a>Típusokat, amelyeket a blobokhoz hozhasson létre
Használhatja a **BlobTrigger** attribútuma a következők:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Más típusú által [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Ha az ügyfelek közvetlenül az Azure storage-fiókot szeretne, hozzáadhatja egy **CloudStorageAccount** paramétert a metódus aláírásához.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Bevezetés a blob-tartalmak szöveges karakterláncra kötés által
Ha várhatóan szöveg blobok, **BlobTrigger** is alkalmazható egy **karakterlánc** paraméter. Az alábbi kódmintában köti a szöveges blob egy **karakterlánc** nevű paraméter **logMessage**. A funkció arra a paraméterre a blob tartalmának írása a WebJobs SDK-irányítópultra.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Blobtartalom beolvasása szerializált ICloudBlobStreamBinder használatával
A következő kódminta implementáló osztályt használ **ICloudBlobStreamBinder** engedélyezéséhez a **BlobTrigger** attribútum kötést létrehozni egy blobot a **WebImage** típusa.

        public static void WaterMark(
            [BlobTrigger("images3/{name}")] WebImage input,
            [Blob("images3-watermarked/{name}")] out WebImage output)
        {
            output = input.AddTextWatermark("WebJobs SDK",
                horizontalAlign: "Center", verticalAlign: "Middle",
                fontSize: 48, opacity: 50);
        }
        public static void Resize(
            [BlobTrigger("images3-watermarked/{name}")] WebImage input,
            [Blob("images3-resized/{name}")] out WebImage output)
        {
            var width = 180;
            var height = Convert.ToInt32(input.Height * 180 / input.Width);
            output = input.Resize(width, height);
        }

A **WebImage** kötés kód megtalálható egy **WebImageBinder** származó osztálynak **ICloudBlobStreamBinder**.

        public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
        {
            public Task<WebImage> ReadFromStreamAsync(Stream input,
                System.Threading.CancellationToken cancellationToken)
            {
                return Task.FromResult<WebImage>(new WebImage(input));
            }
            public Task WriteToStreamAsync(WebImage value, Stream output,
                System.Threading.CancellationToken cancellationToken)
            {
                var bytes = value.GetBytes();
                return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
            }
        }

## <a name="how-to-handle-poison-blobs"></a>Ártalmas blobok kezelése
Ha egy **BlobTrigger** függvény sikertelen, az SDK meghívja az újra, abban az esetben, ha a hiba átmeneti hiba okozta. Ha a hiba okozza a blob tartalmát, a függvény sikertelen lesz, minden alkalommal, amikor megpróbálja feldolgozni a blob. Alapértelmezés szerint az SDK meghívja egy függvény legfeljebb 5 alkalommal egy adott BLOB. Az ötödik próbálja meghiúsul, ha az SDK-t ad hozzá egy üzenet nevű üzenetsor *webjobs-blobtrigger-poison*.

Az újrapróbálkozások maximális száma nem konfigurálható. Azonos **MaxDequeueCount** ártalmas blob kezelésére és ártalmas várólista üzenetkezelés beállítást kell használni.

Az üzenetsorban található üzenet ártalmas blobok esetében a következő JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (a következő formátumban *{webjobs-feladat neve}*. A műveletek. *{Függvény neve}*, például: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName
* Az ETag (például egy blob verzió azonosítója: "0x8D1DC6E70A277EF")

Az alábbi kódmintában a **CopyBlob** függvénynek kódot, amely miatt a sikertelen lesz, minden alkalommal, amikor azt nevezzük. Amikor az SDK meghívja az újrapróbálkozások maximális számát, az ártalmas blob üzenetsor létrehozása egy üzenet és üzenetet dolgoz fel a **LogPoisonBlob** függvény.

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("textblobs/output-{name}")] out string output)
        {
            throw new Exception("Exception for testing poison blob handling");
            output = input.ReadToEnd();
        }

        public static void LogPoisonBlob(
        [QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
            TextWriter logger)
        {
            logger.WriteLine("FunctionId: {0}", message.FunctionId);
            logger.WriteLine("BlobType: {0}", message.BlobType);
            logger.WriteLine("ContainerName: {0}", message.ContainerName);
            logger.WriteLine("BlobName: {0}", message.BlobName);
            logger.WriteLine("ETag: {0}", message.ETag);
        }

Az SDK automatikusan deserializes a JSON-üzenetet. Íme a **PoisonBlobMessage** osztály:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>BLOB lekérdezési algoritmus
A WebJobs SDK megvizsgálja az összes tároló által megadott **BlobTrigger** attribútumok alkalmazás indításakor. Nagy storage-fiókban található ez a vizsgálat hosszabb ideig is eltarthat, így érdemes lehet egy ideig, mielőtt új blobok találhatók és **BlobTrigger** függvények végrehajtása történik.

Alkalmazás indítása után az új vagy módosított blobok észleléséhez az SDK rendszeres időközönként beolvassa a blob storage-naplók. A blobnaplók pufferelve van, és csak fizikai bejegyzése 10 percenként vagy tehát itt is van jelentős késleltetés után blob létrehozása vagy frissítése előtt a megfelelő **BlobTrigger** függvény végrehajt.

A blobokat, amelyekre használatával hoz létre a kivétel a **Blob** attribútum. Amikor a WebJobs SDK-val létrehoz egy új blob, átadja az új blob azonnal bármilyen megfelelő **BlobTrigger** funkciók. Ezért ha egy blob bemeneti és kimeneti láncában, az SDK azokat fel tudná dolgozni hatékony. Ha azt szeretné, hogy a blob futó feldolgozási funkciók létrehozott vagy más módon frissítve blobok alacsony késleltetésű, javasoljuk, de **QueueTrigger** helyett **BlobTrigger**.

### <a name="blob-receipts"></a>BLOB visszaigazolások
A WebJobs SDK gondoskodik róla, hogy nincs **BlobTrigger** függvény egynél többször meghívása megtörténik a azonos új vagy frissített BLOB. Ezt nem megőrzése *visszaigazolások blob* annak érdekében, hogy határozza meg, ha egy adott blob verzió feldolgozása megtörtént.

BLOB visszaigazolások nevű tárolóban tárolt *azure-webjobs-gazdagépek* AzureWebJobsStorage kapcsolati sztring által meghatározott Azure storage-fiókban. Egy blob beérkezési rendelkezik a következő információkat:

* A függvényt, amely a BLOB byla volána ("*{webjobs-feladat neve}*. A műveletek. *{Függvény neve}*", például:"WebJob1.Functions.CopyBlob")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag címke (például egy blob verzió azonosítója: "0x8D1DC6E70A277EF")

Ha azt szeretné, a blob újrafeldolgozása kényszerítheti, hogy a BLOB blob fogadását manuálisan törölheti a *azure-webjobs-gazdagépek* tároló.

## <a name="related-topics-covered-by-the-queues-article"></a>Az üzenetsorok cikkben foglalt Kapcsolódó témakörök
Egy üzenetsor által aktivált blob feldolgozási kezelése kapcsolatos információkat, vagy a WebJobs SDK-val forgatókönyveket nem blob-feldolgozás, lásd [Azure queue storage használata a WebJobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

Ebben a cikkben szereplő kapcsolódó témakörök a következők:

* Az aszinkron funkciók
* Több példány
* Biztonságos leállításának
* A WebJobs SDK attribútumok függvény törzsében.
* Az SDK-kapcsolati karakterláncok beállítása a kódban.
* Értékek a WebJobs SDK-val konstruktor paraméterek beállítása a code-ban
* Konfigurálása **MaxDequeueCount** ártalmas blob kezelésére.
* Egy függvény manuális aktiválása
* Naplók írása

## <a name="next-steps"></a>További lépések
Ez a cikk kódmintákkal, amelyek bemutatják, hogyan kezelje a gyakori forgatókönyvek az Azure-beli blobokhoz munkához biztosított. Azure WebJobs és WebJobs SDK használatával kapcsolatos további információkért lásd: [Azure WebJobs-dokumentáció erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

