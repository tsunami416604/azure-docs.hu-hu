---
title: Ismerkedés a blob storage és a Visual Studio kapcsolódó szolgáltatások (webjobs-feladat projektek) |} Microsoft Docs
description: Első lépések a Blob storage használata a webjobs-feladat projektben csatlakoztassa a Visual Studio használatával Azure-tárolóra kapcsolódó szolgáltatások.
services: storage
author: ghogen
manager: douge
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 4800d6ad0482462b9eccb72de34de56e4f7c370c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796992"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure Blob storage és a Visual Studio csatlakoztatva (webjobs-feladat projektek) szolgáltatások
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a C# mintakódok bemutatják, hogyan indul el egy folyamatot, ha az Azure blob létrehozásakor vagy frissítésekor. A kód minták használja a [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) verzió 1.x. Hozzáadásakor a tárfiók egy webjobs-feladat projekthez a Visual Studio használatával **kapcsolódó szolgáltatások hozzáadása** párbeszédpanelen, a megfelelő Azure Storage NuGet-csomag telepítve van, a megfelelő .NET hivatkozások hozzáadódnak a projekt és a tárfiók kapcsolati karakterláncok frissítése az App.config fájlban.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Hogyan indul el egy function, ha egy blob létrehozásakor vagy frissítésekor
Ez a szakasz bemutatja, hogyan használja a **BlobTrigger** attribútum.

 **Megjegyzés:** a WebJobs SDK megvizsgálja az új vagy módosított blobok bemutató naplófájljait. A folyamat be nem eredendően lassú; egy függvény előfordulhat, hogy nem get indulnak el, néhány percig, vagy már a blob létrehozása után.  Ha az alkalmazás blobok feldolgozása azonnal, az ajánlott módszer a blob létrehozásakor, és használja egy üzenetsor létrehozásához-e a **QueueTrigger** attribútum helyett a **BlobTrigger** a függvény, amely feldolgozza a blob attribútum.

### <a name="single-placeholder-for-blob-name-with-extension"></a>A blob kiterjesztésű egyetlen helyőrzője
A következő példakód másolja át a megjelenő szöveg blobok a *bemeneti* tárolót, hogy a *kimeneti* tároló:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Az attribútum konstruktora a, amelyben a tároló neve és a blob nevének helyőrzője karakterlánc-paramétert fogad. Ebben a példában, ha egy blob neve *Blob1.txt* jön létre a *bemeneti* tároló, a függvény létrehoz egy blob nevű *Blob1.txt* a a *kimeneti* tároló.

Megadhatja egy mintát a blob nevének helyőrzője, a következő kód mintában látható módon:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ez a kód csak blobok kezdve "eredeti-" nevű másolja. Például *eredeti-Blob1.txt* a a *bemeneti* tároló másolódik *másolási-Blob1.txt* a a *kimeneti* tároló.

Ha meg kell adnia egy mintát a blob neve kapcsos zárójelek a neve, dupla a kapcsos zárójelek. Például, ha a keresett blobot, amely a *képek* ilyen nevű tárolóban:

        {20140101}-soundfile.mp3

Ezt a mintát használja:

        images/{{20140101}}-{name}

A példában a *neve* helyőrző értéke lenne *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Külön blob-névnek és kiterjesztésnek helyőrzők
A következő példakód módosítja a fájlnévkiterjesztés, a megjelenő blobot másol a *bemeneti* tárolót, hogy a *kimeneti* tároló. A kód kiterjesztését naplózza a *bemeneti* blob-, és beállítja a kiterjesztését a *kimeneti* a blob *.txt*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Blobok köthető típusai
Használhatja a **BlobTrigger** attribútum a következő esetében:

* **string**
* **TextReader**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Egyéb típusú általi [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder)

Ha közvetlenül az Azure storage-fiók dolgozni szeretne, azt is megteheti egy **CloudStorageAccount** a metódus-aláírás paramétert.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Szöveges blob tartalma karakterláncra kötés beolvasása
Ha a szöveges BLOB várható, **BlobTrigger** alkalmazható egy **karakterlánc** paraméter. A következő példakód köti a szöveges blob egy **karakterlánc** nevű paraméter **logMessage**. A funkció, hogy a paraméter a blob tartalmát írni a WebJobs SDK irányítópult.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Első szerializált blob tartalma ICloudBlobStreamBinder használatával
A következő példakód használ, amely megvalósítja az osztály **ICloudBlobStreamBinder** engedélyezése a **BlobTrigger** attribútum egy blob kötni a **WebImage** típusa.

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

A **WebImage** kötés kód megtalálható egy **WebImageBinder** abból származó osztály **ICloudBlobStreamBinder**.

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

## <a name="how-to-handle-poison-blobs"></a>Az elhalt blobok kezelése
Ha egy **BlobTrigger** függvény sikertelen, az SDK meghívja az ebben az esetben, ha a hibát egy átmeneti hiba okozta. Ha a hiba oka a blob tartalmát, a függvény minden alkalommal, amikor megpróbálja feldolgozni a blob sikertelen lesz. Alapértelmezés szerint az SDK meghívja a függvény legfeljebb 5-ször egy adott BLOB. Az ötödik próbálja meghiúsul, ha az SDK-t ad hozzá egy üzenet nevű várólista *webjobs-blobtrigger-poison*.

Az újrapróbálkozások maximális számát lehet konfigurálni. Azonos **MaxDequeueCount** poison blob kezelésére és a várólista elhalt üzenetek kezelésének beállítást kell használni.

Az elhalt blobok várólista üzenet egy JSON-objektum, amely tartalmazza a következő tulajdonságokkal:

* FunctionId (formátumú *{webjobs-feladat neve}*. Működik. *{Függvény neve}*, például: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* Blobnév
* ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

Az alábbi példakódban az **CopyBlob** függvénynek kódot, amely azt eredményezi, hogy minden alkalommal, amikor a hívott sikertelen. Miután az SDK meghívja az újrapróbálkozások maximális számát, az elhalt blob várólista jön létre, egy üzenet, és üzenetet dolgoz fel a **LogPoisonBlob** függvény.

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

Az SDK automatikusan deserializes a JSON-üzenet. Itt a **PoisonBlobMessage** osztály:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>A BLOB lekérdezési algoritmus
A WebJobs SDK megvizsgálja az összes tároló által megadott **BlobTrigger** alkalmazás indításkor attribútumok. Nagy tárfiókokban Ez a vizsgálat hosszabb ideig is tarthat, így előfordulhat, hogy egy kis ideig, mielőtt új blobok találhatók és **BlobTrigger** funkciókat hajtja végre a rendszer.

Új vagy módosított blobok észleléséhez az alkalmazás indítása után, az SDK rendszeres időközönként olvassa be a blob storage-naplók. A blob naplók pufferelve van-e, és csak fizikailag írása 10 percenként vagy Igen, ezért előfordulhat, hogy jelentős késleltetés után blob létrehozásakor vagy frissítésekor a megfelelő előtt **BlobTrigger** függvény végrehajtása.

A blobok, amelyek használatával hoz létre kivétel történik a **Blob** attribútum. Ha a WebJobs SDK hoz létre egy új blob, adja át az új blob azonnal bármely megfelelő **BlobTrigger** funkciók. Ezért ha fenntartása blob bemenetekhez és kimenetekhez, az SDK-t tud feldolgozni azokat hatékony. Ha azt szeretné, hogy fut a blob feldolgozási funkciók a létrehozott vagy más módon frissítve blobok kis késés, azt javasoljuk, de **QueueTrigger** helyett **BlobTrigger**.

### <a name="blob-receipts"></a>A BLOB visszaigazolások
A WebJobs SDK beállításnál ellenőrizze, hogy nincs **BlobTrigger** függvény egynél többször a azonos új vagy frissített BLOB meghívása megtörténik. Ennek érdekében karbantartása *blob-visszaigazolások* annak meghatározására, ha egy adott blobverzió feldolgozása megtörtént.

A BLOB visszaigazolások nevű tárolóban vannak tárolva *azure-webjobs-állomások* AzureWebJobsStorage kapcsolati karakterlánc által meghatározott az Azure storage-fiókban. Egy blob fogadását rendelkezik a következő információkat:

* A BLOB hívott függvény ("*{webjobs-feladat neve}*. Működik. *{Függvény neve}*", például:"WebJob1.Functions.CopyBlob")
* A tároló neve
* A blob típusú ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

Ha egy blobot újrafeldolgozása kényszeríteni kívánja, a blob fogadását, hogy a BLOB manuálisan törölheti a *azure-webjobs-állomások* tároló.

## <a name="related-topics-covered-by-the-queues-article"></a>A várólisták a cikkben említett kapcsolódó témakörök
Hogyan kezelje a blob feldolgozási sor üzenetet által indított információt, vagy a WebJobs SDK forgatókönyvek nem kizárólag a blobra feldolgozása, lásd [Azure a queue storage használata a WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Kapcsolódó témakörök hivatkozásra, ha a cikkben ismertetett közé tartoznak a következők:

* Aszinkron funkciók
* Több példánya
* Biztonságos leállításának
* Egy függvény törzséhez a WebJobs SDK attribútumok használata
* Az SDK-kapcsolati karakterláncok beállítása a kódban.
* Értékek beállítása a WebJobs SDK konstruktorparaméterek kódot
* Konfigurálása **MaxDequeueCount** poison blob kezelésére.
* Manuálisan kezdeményezi egy függvény
* Naplók írása

## <a name="next-steps"></a>További lépések
Ez a cikk nyújtott mintakódok, amelyek bemutatják, hogyan kezeli az Azure-blobokkal dolgozik gyakori forgatókönyvei. Azure webjobs-feladatok és a WebJobs SDK használatával kapcsolatos további információkért lásd: [Azure WebJobs-dokumentáció erőforrások](http://go.microsoft.com/fwlink/?linkid=390226).

