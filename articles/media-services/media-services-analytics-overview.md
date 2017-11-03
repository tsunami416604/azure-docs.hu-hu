---
title: "A Media Services platform Médiaelemzés |} Microsoft Docs"
description: "A Médiaelemzés beszéd- és számítógép stratégiai szolgáltatásának vállalati méretezés, a megfelelőségi, a biztonsági és a globális reach gyűjteménye public Preview – áttekintés"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: c0bbe6f80370515fa783b12757434897fe2221b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="media-analytics-on-the-media-services-platform"></a>A Media Services platform Médiaelemzés használatával
## <a name="overview"></a>Áttekintés
További szervezetek segítségével végzi a videó adathordozóként előnyben részesített az alkalmazottak számára, az ügyfelek végezhetnek, de a dokumentum üzleti funkciók. A felhőalapú informatika tárolhatja, adatfolyamként, és el nagy médiafájlokat lehetőséget kínál. De videotartalom könyvtárának a vállalat növekedésével egyaránt hatékony elemzések a tartalom kibontása eszközt kell. 

Kell, hogy az egyre növekvő, az Azure Media Services kínál Azure Médiaelemzés használatával. A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. A Media Services platform alapösszetevőket használatával készültek, Media Analytics napon az egyik léptékű feldolgozás media képes kezelni.

Media Analytics fejlesztők is gyorsan vonja fejlett videó funkciói alkalmazások. A teljes skála, a megfelelőségi, a biztonsági és a szükséges nagy méretű szervezeteknek globális reach biztosít vállalati környezetben.

A következő ábrán az látható, Médiaelemzés és egyéb a Media Services platform fontosabb részei. 

![VoD-munkafolyamat](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. Egy media processzor MP4-fájlokat hoz létre, ha fokozatosan lehet letölteni a fájlt. Egy media processzor JSON-fájlt hoz létre, ha az Azure Blob storage letöltheti a fájlt. 

## <a name="media-analytics-services"></a>Médiaelemzés-szolgáltatások

### <a name="indexer"></a>Indexelő
Az Azure Media Indexer hogy tartalom kereshető és készítése kódolt-feliratok követi nyomon. Az előző verzió képest, Azure Media Indexer 2 Preview rendelkezik gyorsabban indexelési és szélesebb körű nyelv támogatja. Támogatott nyelvek angol, német, francia, német, olasz, kínai, portugál és arab. Részletes útmutatást és példákat, lásd: [videók feldolgozása az Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse videó exportstabilizációt és gyors, a hosszú tartalomról fogyasztható videók létrehozásához Gyorsított felvétel funkció egyesíti. Gyorsított felvétel videó létrehozása, mellett a Hyperlapse hozhat létre stabil videók a mobiltelefonokat és kamerák rögzített shaky videók. Részletes útmutatást és példákat, lásd: [Hyperlapse médiafájlok az Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Mozgásérzékelő
Mozgásérzékelő segítségével mozgásérzékelési észleli a helyhez kötött hátterek és videó. Ez lehetővé teszi téves térfigyelő kamerák használatát által észlelt mozgásérzékelési események kereséséhez. Részletes útmutatást és példákat, lásd: [Mozgásérzékelés az Azure Médiaelemzés használatával](media-services-motion-detection.md).
### <a name="face-detector"></a>Arcérzékelő
Arcfelismerési érzékelő használatával Népi lapokat és a érzelmek, beleértve a Boldogsága, sadness és jelzés nélküli észlelését. Számos hasznos iparági alkalmazások, későbbi, beleértve összesítésére és elemzésére esemény részt vevő személyek reakciók azt. Részletes útmutatást és példákat, lásd: [Arcfelismerés és érzelemfelismerési észlelési az Azure Médiaelemzés használatával](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Videóösszegzés
Videóösszegzés segítségével létre videók kijelölésével automatikusan érdekes kódtöredékek a forrás-videót. Ez a lehetőség akkor hasznos, ha lehetővé szeretné tenni, mi történik, a hosszú videó gyors áttekintést. Részletes útmutatást és példákat, lásd: [használata Azure Media videó miniatűrök videó összegzésének létrehozása](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optikai karakterfelismerés
Az Azure Media OCR (optikai karakter használata) átalakíthatja videofájlok a szöveges tartalom szerkeszthető, kereshető digitális szöveg. A médiafájlt a videó jel jelentéssel bíró metaadatok kibontásával majd automatizálható.
### <a name="scalable-face-redaction"></a>Méretezhető arcfelismerési kivonási
Az Azure Media Redactor kínál a felhőben méretezhető arcfelismerési kivonási Médiaelemzés media processzor. Arcfelismerési kivonási használatával módosíthatja az lehetőséget felületei kijelölt személyek a videó. Előfordulhat, hogy használni kívánt a tapasztalt kivonási szolgáltatás hírek media, vagy ha nyilvános biztonsági van szó. Több lapokat tartalmazó felvételei, néhány perc múlva a kivonás a manuálisan órát is igénybe vehet, de ezzel a szolgáltatással arcfelismerési kivonási tart néhány egyszerű lépésben. További információkért lásd: a [az Azure Media Analytics lapok kivonás](media-services-face-redaction.md) cikk.

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
Media Analytics segítségével a szervezetek és vállalatok glean videó új információkat kaphat, és több hogyan kezelhet hatékonyan videotartalom nagy mennyiségű. Az alábbiakban néhány forgatókönyv:

* **Erőforrások hívás**. Még a közösségi média megjelenésével ügyfél telefonos ügyfélszolgálatok továbbra is megkönnyíteni az ügyfélszolgálati tranzakciók nagy része. Nagy mennyiségű ügyfél adatait, amely újabb ügyfelek elégedettségének eléréséhez elemzése a hangadatok kódolású van. Media Indexer használatával a szervezetek szöveg és keresési indexek és irányítópultokat hozhat létre. Majd kinyerhessék eszközintelligencia gyakran panaszkodnak, panaszokat források és egyéb kapcsolódó adatokat.
* **Felhasználó által létrehozott tartalom moderálás**. Rendőrségi szervezeti egységek hírek media a számos szervezet rendelkezik nyilvánosan elérhető portálok fogadja el a felhasználók által létrehozott adathordozók, például videók és a képeket. A tartalom mennyisége miatt nem várt események is lefoglalását. Ezekben az esetekben is nehézkes elvégzésével hatékony manuális értékelést, melyekkel tartalom. Az ügyfelek a tartalom-moderálás szolgáltatás megfelelő tartalmat összpontosíthat támaszkodhat.
* **Felügyeleti**. Használja a növekedési IP kamerák rendelkező előre videó egy egyre bővülő leltár a felügyelet. Manuálisan megtekintésével felügyeleti videó az idő, intenzív és emberi tévedések hibalehetőségeket rejt magában. Media Analytics például mozgásérzékelés, arc észlelési és annak ellenőrzése, kezelése és származékaik egyszerűbb létrehozását érdekében Hyperlapse szolgáltatásokat biztosít.

## <a name="media-analytics-media-processors"></a>Media Analytics media processzor
Ebben a szakaszban a Médiaelemzés media processzorok sorolja fel, és a media (MP) processzor objektum .NET és REST használatát ismerteti.

### <a name="mp-names"></a>Felügyeleti csomag neve
* Az Azure Media Indexer 2 előzetes verzió
* Azure Media Indexer
* Azure Media Hyperlapse
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR

### <a name="net"></a>.NET
A következő függvény egyikét a megadott felügyeleti csomag nevét, és a felügyeleti csomag objektum beállítása/beolvasása.

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
A kérelem:

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
Lásd: [Azure Médiaelemzés használatával bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
Lásd: [Médiaelemzés-szolgáltatások közlemény](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
