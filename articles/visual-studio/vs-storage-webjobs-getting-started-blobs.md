---
title: A blobstorage használatának első lépései a Visual Studio használatával (WebJob-projektek)
description: A Blob storage használatának első lépései egy WebJob-projektben, miután a Visual Studio csatlakoztatott szolgáltatásaival csatlakozott egy Azure-tárhoz.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72299979"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-webjob-projects"></a>Ismerkedés az Azure Blob storage-szal és a Visual Studio csatlakoztatott szolgáltatásaival (WebJob-projektek)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk C# kódmintákat tartalmaz, amelyek bemutatják, hogyan indíthat el egy folyamatot, amikor egy Azure blobot hoz létre vagy frissít. A kódminták a [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 1.x verzióját használják. Ha a Visual Studio **Csatlakoztatott szolgáltatások hozzáadása** párbeszédpanelen hozzáad egy tárfiókot egy WebJob-projekthez, a megfelelő Azure Storage NuGet csomag telepítve lesz, a megfelelő .NET-hivatkozások hozzáadódnak a projekthez, és a tárfiók kapcsolati karakterláncai frissülnek az App.config fájlban.

## <a name="how-to-trigger-a-function-when-a-blob-is-created-or-updated"></a>Függvény aktiválása blob létrehozásakor vagy frissítésekor
Ez a szakasz a **BlobTrigger** attribútum használatát mutatja be.

 **Megjegyzés:** A WebJobs SDK ellenőrzi a naplófájlokat, hogy figyelje az új vagy módosított blobokat. Ez a folyamat eredendően lassú; előfordulhat, hogy egy függvény csak néhány perccel vagy a blob létrehozása után aktiválódik.  Ha az alkalmazásnak azonnal fel kell dolgoznia a blobokat, az ajánlott módszer egy várólista-üzenet létrehozása a blob létrehozásakor, és a **BlobTrigger** attribútum helyett a **QueueTrigger** attribútumot kell használnia a blobot feldolgozó függvényen.

### <a name="single-placeholder-for-blob-name-with-extension"></a>Egyetlen helyőrző a bővítményes blobnévhez
A következő kódminta a *bemeneti* tárolóban megjelenő szövegblobokat másolja a *kimeneti* tárolóba:

        public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
            [Blob("output/{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Az attribútumkonstruktor vesz egy karakterlánc-paraméter, amely megadja a tároló nevét és a blob neve helyőrző. Ebben a példában, ha egy *Blob1.txt* nevű blob jön létre a *bemeneti* tárolóban, a függvény létrehoz egy *Blob1.txt* nevű blobot a *kimeneti* tárolóban.

A blob névhelyőrzőjével megadhat egy névmintát, ahogy az a következő kódmintában látható:

        public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
            [Blob("output/copy-{name}")] out string output)
        {
            output = input.ReadToEnd();
        }

Ez a kód csak azokat a blobokat másolja, amelyek neve "eredeti" kezdetű. Például *az eredeti Blob1.txt* a *bemeneti* tárolóban másolja *a copy-Blob1.txt* a *kimeneti* tárolóban.

Ha meg kell adnia egy névmintát a blobnevekhez, amelyek nek kapcsos zárójelek vannak a nevében, duplázza meg a kapcsos zárójeleket. Ha például olyan blobokat szeretne keresni a *lemezképek* tárolójában, amelyek neve ily módon van:

        {20140101}-soundfile.mp3

használja ezt a mintát:

        images/{{20140101}}-{name}

A példában a *név* helyőrző értéke *soundfile.mp3*.

### <a name="separate-blob-name-and-extension-placeholders"></a>Külön blobnév és bővítmény helyőrzői
A következő kódminta módosítja a fájlkiterjesztést, miközben a *bemeneti* tárolóban megjelenő blobokat másolja a *kimeneti* tárolóba. A kód naplózza a *bemeneti* blob kiterjesztését, és a *kimeneti* blob kiterjesztését *.txt.*

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
A **BlobTrigger** attribútumot a következő típusokon használhatja:

* **sztring**
* **TextReader (Szövegolvasó)**
* **Stream**
* **ICloudBlob**
* **CloudBlockBlob**
* **CloudPageBlob**
* Az [ICloudBlobStreamBinder](#getting-serialized-blob-content-by-using-icloudblobstreambinder) által deszerializált egyéb típusok

Ha közvetlenül az Azure storage-fiókkal szeretne dolgozni, hozzáadhat egy **CloudStorageAccount** paramétert a metódus aláírásához.

## <a name="getting-text-blob-content-by-binding-to-string"></a>Szöveges blobtartalom beszerzése karakterlánchoz való kötéssel
Ha szöveges blobok várhatók, **BlobTrigger** egy **karakterlánc-paraméterre** alkalmazható. A következő kódminta egy szöveges blobot köt egy **logMessage**nevű **karakterlánc-paraméterhez.** A függvény ezt a paramétert használja a blob tartalmának a WebJobs SDK irányítópultra való írásához.

        public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
            string name,
            TextWriter logger)
        {
             logger.WriteLine("Blob name: {0}", name);
             logger.WriteLine("Content:");
             logger.WriteLine(logMessage);
        }

## <a name="getting-serialized-blob-content-by-using-icloudblobstreambinder"></a>Szerializált blobtartalom beszerzése az ICloudBlobStreamBinder használatával
A következő kódminta egy osztályt használ, amely megvalósítja **az ICloudBlobStreamBinder-t,** hogy a **BlobTrigger** attribútum megkösse a blobot a **WebImage** típushoz.

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

A **WebImage** kötési kód az **ICloudBlobStreamBinder**programból származó **WebImageBinder** osztályban található.

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

## <a name="how-to-handle-poison-blobs"></a>Hogyan kezeljük a méregfoltokat?
Ha egy **BlobTrigger** függvény meghibásodik, az SDK újra meghívja, ha a hibát átmeneti hiba okozta. Ha a hibát a blob tartalma okozza, a függvény minden alkalommal sikertelen lesz, amikor megpróbálja feldolgozni a blobot. Alapértelmezés szerint az SDK egy adott blob hoz legfeljebb 5-ször hív meg egy függvényt. Ha az ötödik próbálkozás sikertelen, az SDK hozzáad egy üzenetet egy *webjobs-blobtrigger-poison*nevű várólistához.

Az újrapróbálkozások maximális száma konfigurálható. Ugyanaz **tasza tokravári a** rendszer a poison blob és a poison queue üzenetkezeléséhez.

A méregblobok várólista-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId *(a(z) {WebJob name}* formátumban. Funkciók. *{Függvénynév}*, például: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName (BlobName)
* ETag (blob verzióazonosítója, például: "0x8D1DC6E70A277EF")

A következő kódmintában a **CopyBlob** függvény olyan kódot használ, amely minden alkalommal sikertelen, amikor meghívják. Miután az SDK meghívja a maximális számú újrapróbálkozások, egy üzenet jön létre a méreg blob várólistában, és az üzenetet a **LogPoisonBlob** függvény dolgozza fel.

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

Az SDK automatikusan deszerializálja a JSON-üzenetet. Itt van a **PoisonBlobMessage** osztály:

        public class PoisonBlobMessage
        {
            public string FunctionId { get; set; }
            public string BlobType { get; set; }
            public string ContainerName { get; set; }
            public string BlobName { get; set; }
            public string ETag { get; set; }
        }

### <a name="blob-polling-algorithm"></a>Blob lekérdezési algoritmusa
A WebJobs SDK az alkalmazás indításakor a **BlobTrigger** attribútumok által megadott összes tárolót megvizsgálja. Egy nagy tárfiókban ez a vizsgálat eltarthat egy ideig, ezért előfordulhat, hogy egy ideig, mielőtt új blobok találhatók, és **BlobTrigger-függvények** végrehajtása.

Az új vagy módosított blobok észlelése az alkalmazás indítása után, az SDK rendszeresen beolvassa a blob storage naplók. A blob naplók pufferelt, és csak akkor kap fizikailag írt 10 percenként, vagy úgy, így előfordulhat, hogy jelentős késés után egy blob jön létre vagy frissíthető, mielőtt a megfelelő **BlobTrigger** függvény végrehajtása.

Van egy kivétel a blobok, amelyek a **Blob** attribútum használatával létrehozott. Amikor a WebJobs SDK létrehoz egy új blobot, azonnal **BlobTrigger** átadja az új blobot bármely megfelelő BlobTrigger-függvénynek. Ezért ha blob bemenetek és kimenetek láncolata van, az SDK hatékonyan feltudja dolgozni őket. De ha azt szeretné, hogy alacsony késésű a blob feldolgozási függvények blobok, amelyek más módon létrehozott vagy frissített, javasoljuk, hogy **használja QueueTrigger** helyett **BlobTrigger.**

### <a name="blob-receipts"></a>Blob-visszaigazolások
A WebJobs SDK gondoskodik arról, hogy egyetlen **BlobTrigger-függvény** sem kap egynél többször ugyanahhoz az új vagy frissített blobhoz. Ezt úgy éri *el, hogy pacabevételezéseket* tart fenn annak megállapítása érdekében, hogy egy adott blobverzió feldolgozása folyamatban van-e.

A blobnyugták az AzureWebJobsStorage kapcsolati karakterlánc által meghatározott Azure storage-fiókban található *azure-webjobs-hosts* nevű tárolóban tárolótalálhatók. A blobnyugta a következő adatokkal rendelkezik:

* A blobhoz hívott függvény ("*{WebJob name}*. Funkciók. *{Function name}*", például: "WebJob1.Functions.CopyBlob")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag (blob verzióazonosítója, például: "0x8D1DC6E70A277EF")

Ha egy blob újrafeldolgozását szeretné kényszeríteni, manuálisan törölheti az adott blob blobátvételét az *azure-webjobs-hosts* tárolóból.

## <a name="related-topics-covered-by-the-queues-article"></a>A várólistákról szóló cikk ben tárgyalt kapcsolódó témakörök
A várólista-üzenet által kiváltott blob-feldolgozás kezeléséről, illetve a blob-feldolgozásra nem jellemző WebJobs SDK-forgatókönyvekről az [Azure queue storage használata a WebJobs SDK-val című](https://github.com/Azure/azure-webjobs-sdk/wiki)témakörben talál további információt.

Az e cikkben tárgyalt kapcsolódó témák a következők:

* Aszinkron függvények
* Több példány
* Kecses leállítás
* WebJobs SDK-attribútumok használata egy függvény törzsében
* Állítsa be az SDK-kapcsolati karakterláncokat a kódban.
* A WebJobs SDK konstruktorparamétereinek értékei a kódban
* Állítsa be a **MaxDequeueCount-ot** a méregblobok kezeléséhez.
* Függvény manuális aktiválása
* Naplók írása

## <a name="next-steps"></a>További lépések
Ez a cikk olyan kódmintákat tartalmaz, amelyek bemutatják, hogyan kezelhetők az Azure-blobok gyakori forgatókönyvei. Az Azure WebJobs és a WebJobs SDK használatáról az [Azure WebJobs dokumentációs erőforrásaiban](https://go.microsoft.com/fwlink/?linkid=390226)olvashat bővebben.

