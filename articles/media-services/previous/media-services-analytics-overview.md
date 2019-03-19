---
title: A Media Services platformon Médiaelemzés |} A Microsoft Docs
description: A Médiaelemzés beszéd- és vizuális szolgáltatások nagyvállalati szintű, megfelelőségi, biztonsági és globális elérhetőséget gyűjteménye a nyilvános előzetes verzió – áttekintés
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
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: 0c090499ee6ae2aaaa14af18c3c38db28ecb2f3e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884727"
---
# <a name="media-analytics-on-the-media-services-platform"></a>A Media Services platformon Médiaelemzés 

## <a name="overview"></a>Áttekintés
Több szervezet használ a videó a az előnyben részesített közepes betanításához alkalmazottak, az ügyfelek megszólítása és üzleti funkciók dokumentálja. A felhő-számítástechnika segítségével tárolhatja, adatfolyam és érheti el nagy médiafájlokat. De videótartalmak könyvtára a vállalat növekedésével a insights kinyerését a tartalom egyformán hatékony eszközt kell. 

Kell, hogy folyamatosan bővülő, az Azure Media Services az Azure Médiaelemzés biztosít. A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. A Media Services platform alapvető összetevői használatával létrehozott, a Media Analytics médiafeldolgozási ipari méretekben ismereteik alapján képes kezelni.

A Media Analytics-szel a fejlesztők is gyorsan beemelése összetettebb video funkciók alkalmazások. A teljes méretezési, megfelelőségi, biztonsági és nagyméretű szervezetek által megkövetelt globális jelenléttel rendelkező vállalati környezetek biztosít.

Az alábbi ábrán látható, a Media Analytics és a Media Services platform más fontosabb részei. 

![VoD-munkafolyamat](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. Egy médiaprocesszorral MP4-fájlokat hoz létre, ha fokozatosan letöltheti a fájlt. Ha egy médiaprocesszorral hoz létre egy JSON-fájlt, letöltheti a fájlt az Azure Blob storage-ból. 

## <a name="media-analytics-services"></a>Médiaelemzés

### <a name="indexer"></a>Indexelő
Az Azure Media Indexerrel is felvehető tartalom kereshető, és létrehozni feliratozáshoz nyomon követi. Az előző verzióhoz képest, az Azure Media Indexer 2 előzetes verzió támogatja a gyorsabb indexelés és a szélesebb körű nyelvi rendelkezik. Támogatott nyelvek angol, spanyol, francia, német, olasz, kínai, portugál és arab. Részletes információkért és példákért lásd: [videók feldolgozása az Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
A Microsoft Hyperlapse-ben videóstabilizálási és finomíthat funkció gyors, a hosszú tartalomból értelmezhető videók létrehozásához. Amellett, hogy a videó finomíthat hoz létre, a Hyperlapse segítségével hozzon létre stabil videók mobiltelefonok vagy kamerák keresztül rögzített remegő videók. Részletes információkért és példákért lásd: [Hyperlapse médiafájlokat az Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Mozgásérzékelő
Mozgásérzékelő segítségével egy videóban hátterű mozgásfelismerés. Ez lehetővé teszi a térfigyelő kamerák által észlelt mozgásban lévő események által kiváltott hamis pozitív kereséséhez. Részletes információkért és példákért lásd: [Mozgásérzékelés az Azure Médiaelemzés használatával](media-services-motion-detection.md).
### <a name="face-detector"></a>Arcérzékelő
A Face detector használatával, people's arcokat és az érzelmeket azonosíthat, például boldogság, szomorúság és meglepetés észlelését. Több hasznos iparági alkalmazást, a később ismertetett és elemzésére reakciók személyek részt vesz egy eseményt, beleértve azt. Részletes információkért és példákért lásd: [Arcfelismerés és az érzelmeket észlelése az Azure Médiaelemzés használatával](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Videó-összefoglalás
Videó-összefoglalás segíthet a hosszú videókról összefoglalókat készíthet az a forrás videó érdekes kódrészletek automatikusan kiválasztásával. Ez a lehetőség akkor hasznos, ha lehetővé szeretné tenni, mire számítson hosszú videó röviden. Részletes információkért és példákért lásd: [használata az Azure Media Videóminiatűrök hozhat létre a videó-összefoglalás](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optikai karakterfelismerés
Az Azure Media OCR (optikai karakterfelismerés) szerkeszthetők, kereshető digitális szöveggé alakíthatja videofájlok szöveges tartalommal. Majd segítségével automatizálhatja az értelmezhető metaadatokat kibontásával kinyert videó médiafájlt.
### <a name="scalable-face-redaction"></a>Méretezhető arcszerkesztés
Az Azure Media Redactor kínál a felhőben méretezhető arcszerkesztés Media Analytics médiafeldolgozó. Arcszerkesztés használatával módosíthatja a arcának elmossa videó. Előfordulhat, hogy használni kívánt a face kivonási szolgáltatás sajtóbeli híradásoknál, vagy ha közbiztonság van szó. Néhány perc alatt több arcokat tartalmazó képanyag manuálisan szereplők órát is igénybe vehet, de ennek a szolgáltatásnak arcszerkesztés néhány egyszerű lépéssel vesz igénybe. További információkért lásd: a [szereplők az Azure Media Analytics arcok](media-services-face-redaction.md) cikk.
### <a name="content-moderation"></a>Tartalom-jóváhagyás
Az Azure Content Moderator lehetővé teszi a gépi támogatású képmoderálás használhatja a videókhoz. Például érdemes észlelheti a videók lehetséges felnőtt és szexuális tartalom, és tekintse át a megjelölt tartalom az emberi moderálás csoportok. Manuálisan moderálása nem kívánatos tartalmakat a videók a időigényes és drága feladat. A szolgáltatás és a kapcsolódó vizsgálóeszközt kombinálja a legjobb eredmények elérése érdekében emberi hurok képességekkel rendelkező gépi támogatású képmoderálás hatékonyan és költséghatékonyan. További tudnivalókért tekintse meg a [feldolgozhatja a videóit Azure Content Moderator](media-services-content-moderation.md) cikk.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
Media Analytics megkönnyíti a szervezetek számára, és vállalatok glean új elemzéseket az videót, és kezelheti hatékonyan videótartalmak nagy mennyiségű. Az alábbiakban néhány forgatókönyvre:

* **Hívás központok**. Közösségi média bevezetése mellett is ügyfél telefonos ügyfélszolgálatok továbbra is az ügyfélszolgálati tranzakciók álló megkönnyítése. Nagy mennyiségű ügyfél-információkat, amelyek magasabb szintű ügyfél-elégedettség érdekében elemezheti a hívásaiból kódolva van. A Media Indexer segítségével szervezetek szöveg kinyerése és keresési indexek és irányítópultokat hozhat létre. Bontsa ki a azok is intelligencia közös panaszok, panaszok forrásai és más vonatkozó adatokat.
* **Felhasználó által létrehozott tartalom moderálása**. A news médiaorgánumok rendőrségi részlegekhez számos vállalat rendelkezik olyan nyilvános portálok, amely fogadja el a felhasználók által létrehozott adathordózóról, például a képek és videók. A tartalom mennyisége miatt nem várt események is lefoglalását. Ezekben a forgatókönyvekben meglehetősen nehéz indították tartalmának manuális felülvizsgálatai hatékony elvégzésére. Ügyfelek a tartalom-jóváhagyás szolgáltatás összpontosíthat a megfelelő tartalom is támaszkodik.
* **Felügyeleti**. Használatban lévő IP-kamerák növekedést is tartalmaz egy egyre bővülő felügyeleti készlet videó. Manuális megtekintésével felügyeleti videó az idő nagy számításigényű és az emberi hibák hibalehetőség. Media Analytics például mozgásérzékelés, arcfelismerés és annak áttekintése, kezelése és származékaik egyszerűbb létrehozása Hyperlapse szolgáltatásokat biztosít.

## <a name="media-analytics-media-processors"></a>A médiaelemzés médiafeldolgozói
Ez a szakasz felsorolja a médiaelemzés médiafeldolgozói, és bemutatja a .NET vagy a REST használata a media (MP) processzor-objektum.

### <a name="mp-names"></a>Felügyeleti csomag neve
* Az Azure Media Indexer 2 előzetes verzió
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
A következő függvényt a megadott felügyeleti csomag nevek egyikét veheti fel és a egy felügyeleti csomag objektumot ad vissza.

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
Lásd: [Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
Lásd: [Médiaelemzés-szolgáltatások közlemény](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
