---
title: Media Analytics a Media Services platformon | Microsoft dokumentumok
description: A Media Analytics nyilvános előzetes verzióinak áttekintése, a nagyvállalati szintű beszéd- és számítógépes látásszolgáltatások gyűjteménye, a megfelelőség, a biztonság és a globális elérés
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
ms.date: 11/13/2019
ms.author: juliako
ms.reviewer: milanga; johndeu
ms.openlocfilehash: ab1eba3de474d9ff985e62f491c24fa63be0fa63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069625"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics a Media Services platformon 

## <a name="retirement-plans"></a>Nyugdíjazási tervek

> [!IMPORTANT]
> Egyes médiaprocesszorok kivonásalatt állnak. A nyugdíjazási dátumokat és további információkat lásd az [örökölt összetevők](legacy-components.md) témakörben. 

## <a name="overview"></a>Áttekintés

Egyre több szervezet használja a videót előnyben részesített médiumként az alkalmazottak betanításához, az ügyfelek bevonásához és az üzleti funkciók dokumentálásához. A felhőalapú számítástechnika lehetővé teszi ezeknek a nagy médiafájloknak a tárolását, streamelését és elérését. De ahogy a vállalat videotartalom-könyvtára növekszik, ugyanolyan hatékony eszközre van szüksége a tartalomból származó információk kinyeréséhez. 

Ennek a növekvő igénynek a kezelésére az Azure Media Services kínálja az Azure Media Analytics szolgáltatást. A Médiaelemzés beszéd- és vizuális összetevők gyűjteménye, amely egyszerűbbé teszi a szervezetek és vállalatok számára, hogy a gyakorlatban is használható elemzéseket készítsenek videófájljaikból. A Media Services platform alapvető összetevőinek használatával készült Media Analytics az első napon nagy méretekben képes kezelni a médiafeldolgozást.

A Media Analytics segítségével a fejlesztők gyorsan bevihetik a fejlett videofunkciókat az alkalmazásokba. Nagyvállalati környezeteket biztosít a nagy szervezetek által megkövetelt teljes körű, megfelelőségi, biztonsági és globális eléréssel.

Az alábbi ábrán a Media Analytics és a Media Services platform más főbb részei láthatók. 

![VoD-munkafolyamat](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

A Médiaelemzés médiafeldolgozói MP4- vagy JSON-fájlokat hoznak létre. Ha egy médiaprocesszor MP4-fájlt hoz létre, fokozatosan letöltheti a fájlt. Ha egy médiaprocesszor létrehoz egy JSON-fájlt, letöltheti a fájlt az Azure Blob storage-ból. 

## <a name="media-analytics-services"></a>Media Analytics-szolgáltatások

### <a name="indexer"></a>Indexelő
Az Azure Media Indexer segítségével kereshetővé teheti a tartalmat, és feliratozási sávokat hozhat létre. Részletes információkat és példákat az [Indexelő Médiafájlok az Azure Media Indexelővel című](media-services-index-content.md)témakörben talál.

### <a name="motion-detector"></a>Mozgásérzékelő
A Mozgásérzékelő vel érzékelheti a mozgást egy álló hátterű videóban. Ez lehetővé teszi, hogy ellenőrizze a hamis pozitív mozgás események által észlelt térfigyelő kamerák. Részletes információkat és példákat az [Azure Media Analytics Mozgásérzékelése](media-services-motion-detection.md)című témakörben talál.

### <a name="face-detector"></a>Arcérzékelő
Az Arcdetektor segítségével észlelheti az emberek arcát és érzelmeit, beleértve a boldogságot, a szomorúságot és a meglepetést. Ez számos hasznos iparági alkalmazások, később leírt, beleértve a gregating és elemzése reakciók az emberek részt vesznek egy eseményen. Részletes információkat és példákat az [Azure Media Analytics Arc- és érzelemfelismerés című témakörében talál.](media-services-face-and-emotion-detection.md)

### <a name="video-summarization"></a>Videó összegzése
A videóösszegzés segítségével a forrásvideóból automatikusan érdekes kódrészleteket választhat ki a hosszú videók összegzéséhez. Ez a képesség akkor hasznos, ha gyors áttekintést szeretne adni arról, hogy mire számíthat egy hosszú videóban. Részletes információkat és példákat az [Azure Media Videoindexképek használata videóösszegzés létrehozásához](media-services-video-summarization.md)című témakörben talál.
### <a name="optical-character-recognition"></a>Optikai karakterfelismerés
Az Azure Media Optikai karakterekkel (optikai karakterfelismerés) szerkeszthető, kereshető digitális szöveggé alakíthatja a videofájlok szöveges tartalmát. Ezután automatizálhatja az értelmes metaadatok kinyerését a média videojeléből.
### <a name="scalable-face-redaction"></a>Skálázható arc kivonás
Az Azure Media Redactor egy Media Analytics-médiaprocesszor, amely skálázható arckivonást kínál a felhőben. Az arckivonás használatával módosíthatja a videót, hogy elhomályosítsa a kiválasztott személyek arcát. Érdemes lehet használni az arc kivonás szolgáltatás a média, vagy ha a közbiztonság van szó. Néhány percnyi, több arcot tartalmazó felvétel manuális kivonása órákba telhet, de ezzel a szolgáltatással az arckivonás csak néhány egyszerű lépést tesz lehetővé. További információkért tekintse meg a [Redact arcok az Azure Media Analytics cikket.](media-services-face-redaction.md)

### <a name="content-moderation"></a>Tartalom moderálása
Az Azure Content Moderator lehetővé teszi, hogy gépi támogatású moderálása a videók. Például előfordulhat, hogy szeretné észlelni az esetleges felnőtt vagy kényes tartalmakat a videókban, majd egy emberi moderátorcsapattal kívánja felülvizsgáltatni a megjelölt tartalmakat. A videók nemkívánatos tartalomra történő manuális moderálása időigényes és költséges feladat. Ezzel a szolgáltatással és a kapcsolódó felülvizsgálati eszközökkel kombinálhatja a gépi támogatású moderálást a human-in-the-loop képességekkel a legjobb eredmény érdekében. További információkért tekintse meg a [videók feldolgozása az Azure Content Moderator cikket.](media-services-content-moderation.md)

## <a name="common-scenarios"></a>Gyakori forgatókönyvek
A Media Analytics segítségével a szervezetek és a vállalatok új információkat nyerhetnek a videókból, és hatékonyabban kezelhetik a nagy mennyiségű videotartalmat. Íme néhány forgatókönyv:

* **Call centerek**. Még az Advent a szociális média, ügyfél call centerek még mindig megkönnyítik a nagy százaléka ügyfélszolgálati tranzakciók. Ebben a hangadatban nagy mennyiségű ügyfélinformáció van, amely elemezhető a nagyobb ügyfél-elégedettség elérése érdekében. A Media Indexer használatával a szervezetek kinyerhetik a szöveget, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszok, a panaszok forrásai és más releváns adatok körüli információkat.
* **Felhasználó által generált tartalommoderálás**. A hírmédiumoktól a rendőri szervekig számos szervezet rendelkezik nyilvános portálokkal, amelyek elfogadják a felhasználók által létrehozott médiát, például a videókat és a képeket. A tartalom mennyisége váratlan események miatt megemelkedhet. Ezekben a forgatókönyvekben nehéz hatékony manuális felülvizsgálatot végezni a tartalom megfelelősége érdekében. Az ügyfelek a tartalommoderálási szolgáltatásra támaszkodhatnak, hogy a megfelelő tartalomra összpontosíthassanak.

## <a name="media-analytics-media-processors"></a>Media Analytics médiaprocesszorok
Ez a szakasz a Media Analytics médiaprocesszorokat sorolja fel, és bemutatja, hogyan lehet a .NET vagy a REST használatával beszerezni egy mp-objektumot.

### <a name="mp-names"></a>MP-nevek

* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media tartalommoderátor

### <a name="net"></a>.NET
A következő függvény a megadott mp-nevek egyikét veszi fel, és egy MP-objektumot ad vissza.

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
    x-ms-version: 2.19
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

## <a name="demos"></a>Demók
Tekintse meg az [Azure Media Analytics bemutatóit.](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
Lásd: [Media Services Analytics közlemény](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
