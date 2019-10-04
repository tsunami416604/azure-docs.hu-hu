---
title: Media Analytics a Media Services platformon | Microsoft Docs
description: A Media Analytics nyilvános előzetesének áttekintése, a nagyvállalati szinten, a megfelelőséggel, a biztonsággal és a globális elérhetőséggel foglalkozó beszéd-és számítógépes szemléletű szolgáltatások gyűjteménye
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: d8470ad759d983f25f8f68b93d58f89f23eeb578
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "69015400"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics a Media Services platformon 

## <a name="overview"></a>Áttekintés
Több szervezet használja a videó használatát előnyben részesített médiumként alkalmazottaik betanításához, az ügyfelek bevonásához és az üzleti funkciók dokumentálására. A felhő-számítástechnika lehetővé teszi a nagyméretű médiafájlok tárolását, továbbítását és elérését. Ha azonban a videó tartalmának a vállalati könyvtára is növekszik, akkor a tartalomból való kinyeréséhez hasonlóan hatékony eszközre van szükség. 

A növekvő igény kielégítéséhez Azure Media Services Azure Media Analytics kínál. A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. Az alapszintű Media Services platform-összetevők használatával a Media Analytics a média feldolgozását a nap folyamán is képes kezelni.

A Media Analytics segítségével a fejlesztők gyorsan készíthetnek speciális videó-funkciókat az alkalmazásokban. Vállalati környezeteket biztosít a nagyméretű szervezetek számára szükséges teljes körű, megfelelőséggel, biztonsággal és globális elérhetőséggel.

Az alábbi ábrán a Media Services platform Media Analytics és egyéb főbb részei láthatók. 

![VoD-munkafolyamat](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. Ha egy adathordozó-feldolgozó MP4-fájlt hoz létre, fokozatosan letöltheti a fájlt. Ha egy adathordozó-feldolgozó JSON-fájlt hoz létre, letöltheti a fájlt az Azure Blob Storage-ból. 

## <a name="media-analytics-services"></a>Media Analytics szolgáltatások

### <a name="indexer"></a>Indexelő
A Azure Media Indexer segítségével kereshetővé teheti a tartalmat, és létrehozhat lezárt feliratozási számokat. Az előző verzióhoz képest a Azure Media Indexer 2 előzetes verziója gyorsabb indexelést és szélesebb nyelvi támogatást nyújt. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, kínai, portugál és arab. Részletes információkért és példákért tekintse meg a [videók feldolgozása a Azure Media Indexer 2](media-services-process-content-with-indexer2.md)-mel című témakört.
### <a name="motion-detector"></a>Mozgásérzékelő
A mozgásérzékelő használatával érzékelheti a mozgást egy álló hátterű videóban. Ez lehetővé teszi, hogy a megfigyelő kamerák által észlelt mozgási eseményeken hamis pozitív eredményt keressen. A részletes információkat és példákat lásd: [a Azure Media Analytics mozgásészlelésének észlelése](media-services-motion-detection.md).
### <a name="face-detector"></a>Arcérzékelő
A Face detektor használatával azonosíthatja az emberek arcát és érzelmeit, például a boldogságot, a szomorúságot és a meglepetést. Ez számos hasznos iparági alkalmazást tartalmaz, amelyeket később ismertetünk, beleértve az eseményekben részt vevő személyek reakcióinak összesítését és elemzését. Részletes információkat és példákat a [Azure Media Analytics arc-és érzelem-észlelése](media-services-face-and-emotion-detection.md)című témakörben talál.
### <a name="video-summarization"></a>Videók összegzése
A videó-összefoglalás segítségével összefoglalókat hozhat létre a hosszú videókról a forrás videóból származó érdekes kódrészletek automatikus kiválasztásával. Ez a funkció akkor hasznos, ha gyors áttekintést szeretne nyújtani arról, hogy mire számíthat egy hosszú videóban. Részletes információkat és példákat a következő témakörben talál: a [Azure Media Video thumbnails használata a videók összegzésének létrehozásához](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optikai karakterfelismerés
Az Azure Media OCR (optikai karakterfelismerés) használatával szerkeszthető, kereshető digitális szövegként konvertálhatja a videofájlok szövegét. Ezt követően automatizálhatja az értelmes metaadatok kinyerését az adathordozó Videójának jelének használatával.
### <a name="scalable-face-redaction"></a>Skálázható arc kivonása
A Azure Media Redactor egy Media Analytics adathordozó-feldolgozó, amely méretezhető Arcfelismerés a felhőben. Az Arcfelismerés használatával módosíthatja a videót a kijelölt személyek életlenítési lapjaira. Előfordulhat, hogy az Arcfelismerés szolgáltatást szeretné használni a hírek adathordozóján, vagy ha a közbiztonság is érintett. Néhány perces felvétel, amely több arcot tartalmaz, akár órákat is igénybe vehet manuálisan, de ezzel a szolgáltatással az arc kivonása csupán néhány egyszerű lépést vesz igénybe. További információ: kivonási [arcok Azure Media Analytics](media-services-face-redaction.md) cikkből.
### <a name="content-moderation"></a>Tartalom-jóváhagyás
Az Azure Content Moderator lehetővé teszi, hogy a videókhoz géppel támogatott moderálást használjon. Például előfordulhat, hogy szeretné észlelni az esetleges felnőtt vagy kényes tartalmakat a videókban, majd egy emberi moderátorcsapattal kívánja felülvizsgáltatni a megjelölt tartalmakat. A videók a nemkívánatos tartalomhoz történő manuális moderálása időigényes és költséges feladat. Ezzel a szolgáltatással és a kapcsolódó felülvizsgálati eszközökkel kombinálhatja a gépi támogatással rendelkező moderálásokat a legjobb eredmények hatékony és költséghatékony megoldásával. További információkért tekintse meg a [videók Azure](media-services-content-moderation.md) -Content Moderatorval való feldolgozását ismertető cikket.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
Media Analytics segítségével a szervezetek és a vállalatok új bepillantást nyerhetnek a videóból, és hatékonyabban kezelhetik a nagy mennyiségű videó tartalmát. Íme néhány forgatókönyv:

* Központilag hívható. A közösségi média beköszöntével még az ügyfél-szolgáltatási tranzakciók nagy hányadát is megkönnyíti az ügyfelek hívási központjai. Az ebben a hangadatokban kódolva nagy mennyiségű ügyfél-információ elemezhető, így magasabb szintű vásárlói elégedettség érhető el. Media Indexer használatával a szervezetek szövegeket gyűjthetnek, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik az intelligenciát a gyakori panaszok, a panaszok forrásai és egyéb releváns adatok köré.
* A **felhasználó által létrehozott tartalom moderálása**. A hírek adathordozóról a rendőrségi részlegeknek számos szervezet rendelkezik olyan nyilvános portálokkal, amelyek a felhasználó által létrehozott adathordozókat, például videókat és képeket fogadják el. A tartalom mennyisége nem várt események miatt megszeghető. Ezekben a forgatókönyvekben nehéz a tartalom hatékony manuális felülvizsgálatának elvégzése a megfelelőség érdekében. Az ügyfelek a Content-moderációs szolgáltatásra támaszkodva a megfelelő tartalomra összpontosítanak.

## <a name="media-analytics-media-processors"></a>Media Analytics adathordozó-processzorok
Ez a szakasz felsorolja a Media Analytics adathordozó-processzorokat, és bemutatja, hogyan használható a .NET vagy a REST egy Media Processor (MP) objektum beszerzéséhez.

### <a name="mp-names"></a>MP-nevek
* Azure Media Indexer 2 előzetes verzió
* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
A következő függvény a megadott MP-nevek egyikét veszi át, és egy MP-objektumot ad vissza.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Kérés:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Válasz:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Bemutatók
Lásd: [Azure Media Analytics demók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
Lásd: [Media Services Analytics-hirdetmény](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
