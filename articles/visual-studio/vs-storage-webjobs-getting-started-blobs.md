---
title: A blob Storage használatának első lépései a Visual Studióval (Webjobs-projektek)
description: A blob Storage használatának első lépései egy Webjobs-projektben, miután kapcsolódott egy Azure Storage-hoz a Visual Studio Connected Services használatával.
services: storage
author: ghogen
manager: jillfra
ms.assetid: 324c9376-0225-4092-9825-5d1bd5550058
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 90aa824b7df575eb2783ece5bd88322f0b55f0a2
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299979"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure Blob Storage és a Visual Studio csatlakoztatott szolgáltatásaival (Webjobs-projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk C# olyan programkód-mintákat tartalmaz, amelyek bemutatják, hogyan indíthat el egy folyamatot egy Azure-Blob létrehozásakor vagy frissítésekor. A kód minták a [Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 1. x verzióját használják. Amikor egy Webjobs-projekthez hozzáadja a Storage-fiókot a Visual Studio **csatlakoztatott szolgáltatások hozzáadása** párbeszédpanel használatával, a megfelelő Azure Storage NuGet-csomag telepítve lesz, a megfelelő .net-hivatkozások hozzáadódnak a projekthez, és a kapcsolati karakterláncok a következőhöz: a Storage-fiók az app. config fájlban frissül.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Függvény elindítása blob létrehozásakor vagy frissítésekor
Ez a szakasz bemutatja, hogyan használhatja a **BlobTrigger** attribútumot.

 **Megjegyzés:** A webjobs SDK megvizsgálja a naplófájlokat az új vagy módosított Blobok figyeléséhez. Ez a folyamat természeténél fogva lassú; Előfordulhat, hogy a rendszer nem indít el egy függvényt, amíg a blob létrehozása után több percig vagy tovább nem ér.  Ha az alkalmazásnak azonnal fel kell dolgoznia a blobokat, az ajánlott módszer egy üzenetsor létrehozása a blob létrehozásakor, valamint a **QueueTrigger** attribútum használata a blobot feldolgozó függvény **BlobTrigger** attribútuma helyett. .

### <a name="single-placeholder-for-blob-name-with-extension"></a>A blob nevének egyetlen helyőrzője a kiterjesztéssel
A következő mintakód a *bemeneti* tárolóban megjelenő szöveges blobokat másolja a *kimeneti* tárolóba:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Az attribútum konstruktora egy olyan karakterlánc-paramétert hoz létre, amely megadja a tároló nevét és a blob nevének helyőrzőjét. Ebben a példában, ha egy *Blob1. txt* nevű blob jön létre a *bemeneti* tárolóban, a függvény létrehoz egy *Blob1. txt* nevű blobot a *kimeneti* tárolóban.

Megadhat egy nevet a blob neve helyőrzővel, ahogyan az a következő mintakód-mintában látható:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ez a kód csak a "Original-" kezdetű nevű blobokat másolja. A *bemeneti* tárolóban található *Original-Blob1. txt* például a *kimeneti* tárolóban lévő *copy-Blob1. txt fájlba* másolódik.

Ha olyan blob-nevekhez kell megadnia a nevet, amelyek kapcsos zárójelekkel rendelkeznek a névben, akkor a kapcsos zárójelek közé kell állítani. Ha például a *képek* tárolóban lévő blobokat szeretne megtalálni, amelyek a következőhöz hasonló névvel rendelkeznek:

        {20140101}-soundfile.mp3

használja ezt a mintát:

        images/{{20140101}}-{name}

A példában a *név* helyőrző értéke *hangállomány. mp3*lesz.

### <a name="separate-blob-name-and-extension-placeholders"></a>A blob neve és a kiterjesztés helyőrzői
A következő mintakód megváltoztatja a fájlkiterjesztést, mert a *bemeneti* tárolóban megjelenő blobokat másolja a *kimeneti* tárolóba. A kód naplózza a *bemeneti* blob bővítményét, és beállítja a *kimeneti* blob kiterjesztését a *. txt fájlba*.

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

## <a name="types-that-you-can-bind-to-blobs"></a>Blobokhoz köthető típusok
A **BlobTrigger** attribútum a következő típusokban használható:

* **karakterlánc**
* **TextReader**
* **Patak**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* A [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder) által deszerializált egyéb típusok

Ha közvetlenül az Azure Storage-fiókkal szeretne dolgozni, **CloudStorageAccount** paramétert is hozzáadhat a metódus aláírásához.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Szöveges blob tartalmának beolvasása karakterlánchoz kötéssel
Ha a rendszer szöveges blobokat vár, a **BlobTrigger** alkalmazható egy **karakterlánc** -paraméterre. A következő mintakód egy **logMessage**nevű **karakterlánc** -paraméterhez köti a szöveges blobot. A függvény ezt a paramétert használja a blob tartalmának írásához a webjobs SDK-irányítópulton.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Szerializált blob-tartalom beolvasása a ICloudBlobStreamBinder használatával
A következő mintakód egy olyan osztályt használ, amely **ICloudBlobStreamBinder** valósít meg, hogy a **BlobTrigger** attribútum a blobot a **webképek** típusához köti.

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

A **webrendszerkép** -kötési kód egy olyan **WebImageBinder** osztályba tartozik, amely a **ICloudBlobStreamBinder**származik.

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

## <a name="how-to-handle-poison-blobs"></a>A méreg Blobok kezelése
Ha egy **BlobTrigger** függvény meghibásodik, az SDK újra meghívja azt az esetet, ha a hibát egy átmeneti hiba okozta. Ha a hibát a blob tartalma okozta, a függvény minden alkalommal meghiúsul, amikor megkísérli feldolgozni a blobot. Az SDK alapértelmezés szerint legfeljebb 5 alkalommal hívja meg a függvényt egy adott blob esetében. Ha az ötödik próbálkozás sikertelen, az SDK felvesz egy üzenetet egy *webjobs-blobtrigger-méreg*nevű várólistába.

Az újrapróbálkozások maximális száma konfigurálható. Ugyanez a **MaxDequeueCount** -beállítás a blob-kezelő és a méreg üzenetsor-üzenetek kezelésére szolgál.

A méreg-Blobok üzenetsor-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId ( *{webjobs Name}* formátumban. Funkciók. *{Function Name}* , például: WebJob1. functions. CopyBlob)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName
* ETag (blob-verzió azonosítója, például: "0x8D1DC6E70A277EF")

A következő kódrészletben a **CopyBlob** függvénynek van olyan kódja, amelynek hatására a rendszer minden alkalommal meghívja a hibát. Miután az SDK meghívja az újrapróbálkozások maximális számát, létrejön egy üzenet a megmérgezett blob-várólistán, és ezt az üzenetet a **LogPoisonBlob** függvény dolgozza fel.

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

Az SDK automatikusan deszerializálja a JSON-üzenetet. Itt látható a **PoisonBlobMessage** osztály:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>BLOB lekérdezési algoritmusa
A webjobs SDK a **BlobTrigger** attribútumai által megadott összes tárolót megvizsgálja az alkalmazás indításakor. Nagyméretű Storage-fiókban ez a vizsgálat hosszabb időt is igénybe vehet, így előfordulhat, hogy az új Blobok megtalálása és a **BlobTrigger** függvények végrehajtása előtt.

Ha az alkalmazás elindítása után új vagy módosított blobokat szeretne felderíteni, az SDK rendszeres időközönként beolvassa a blob Storage-naplókat. A blob-naplók puffereltek, és csak 10 percenként jelentkeznek be fizikailag, így előfordulhat, hogy a blob létrehozása vagy frissítése után jelentős késés lehet a megfelelő **BlobTrigger** -függvény végrehajtása előtt.

Kivételt képeznek a **blob** attribútum használatával létrehozott Blobok. Ha a webjobs SDK új blobot hoz létre, azonnal átadja az új blobot a megfelelő **BlobTrigger** -függvényeknek. Ezért ha a blob bemenetek és kimenetek láncával rendelkezik, az SDK hatékonyan feldolgozhatja őket. Ha azonban kis késleltetést szeretne végezni a blob-feldolgozási függvények más módon létrehozott vagy frissített Blobok általi futtatásához, javasoljuk, hogy a **QueueTrigger** -et **BlobTrigger**helyett használja.

### <a name="blob-receipts"></a>BLOB-visszaigazolások
A webjobs SDK gondoskodik arról, hogy egyetlen **BlobTrigger** függvény se legyen többször ugyanarra az új vagy frissített blobra. Ezt a *blob-visszaigazolások* karbantartásával végezheti el annak megállapításához, hogy egy adott blob-verzió feldolgozása megtörtént-e.

A blob-visszaigazolásokat egy *Azure-webjobs* nevű tároló tárolja, amely az Azure Storage-fiókban található, a AzureWebJobsStorage-kapcsolatok karakterlánca által meghatározott. A blob-visszaigazolás a következő információkat tartalmazhatja:

* A blobhoz hívott függvény ( *{webjobs Name}* ). Funkciók. *{Function Name}* ", például:" WebJob1. functions. CopyBlob ")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* A ETag (a blob verziószáma, például: "0x8D1DC6E70A277EF")

Ha kényszeríteni szeretné a Blobok újrafeldolgozását, manuálisan törölheti az adott blobhoz tartozó blob-elismervényt az *Azure-webjobs-hosts* tárolóból.

## <a name="related-topics-covered-by-the-queues-article"></a>A várólistákról szóló cikkben szereplő kapcsolódó témakörök
További információ a várólista-üzenet által aktivált blob-feldolgozás kezeléséről, illetve a blob-feldolgozásra nem jellemző webjobs SDK-forgatókönyvekről: [Az Azure üzenetsor-tároló használata a Webjobs SDK-val](https://github.com/Azure/azure-webjobs-sdk/wiki).

Az ebben a cikkben szereplő kapcsolódó témakörök a következők:

* Aszinkron függvények
* Több példány
* Biztonságos leállítás
* Webjobs SDK-attribútumok használata függvények törzsében
* Állítsa be az SDK-kapcsolatok karakterláncait a kódban.
* A webjobs SDK-konstruktor paramétereinek értékeinek beállítása a kódban
* A **MaxDequeueCount** konfigurálása a méreg Blobok kezelésére.
* Függvény manuális elindítása
* Írási naplók

## <a name="next-steps"></a>Következő lépések
Ez a cikk az Azure-Blobok használatának gyakori forgatókönyveit bemutató kódrészleteket tartalmaz. További információ a Azure WebJobs és a webjobs SDK használatáról: [Azure WebJobs dokumentációs erőforrások](https://go.microsoft.com/fwlink/?linkid=390226).

