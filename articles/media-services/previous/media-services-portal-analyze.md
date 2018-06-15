---
title: A média az Azure portál használatával elemezheti |} Microsoft Docs
description: Ez a témakör ismerteti, hogyan kell feldolgozni a media Médiaelemzés media processzorral (felügyeleti csomagok) az Azure portál használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 60da24b5b0bb7d83414539f07936c61a196e0b62
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788850"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Médiatartalmak elemzése az Azure Portal használatával
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Media Services Analytics beszéd- és vizuális összetevők (a vállalati méretű, megfelelőség, biztonsági és globális reach), amelyek megkönnyítik a szervezetek és vállalatok számára, hogy készítsenek gyakorlatban használható elemzések gyűjteménye. További részletes Azure Media Services Analytics áttekintése: [ez](media-services-analytics-overview.md) témakör. 

Ez a témakör ismerteti, hogyan kell feldolgozni a media Médiaelemzés media processzorral (felügyeleti csomagok) az Azure portál használatával. Media Analytics felügyeleti csomagok MP4- vagy JSON-fájlok előállításához. Ha egy media processzor MP4-fájlokat, fokozatosan letölteni a fájlt. Ha egy media processzor egy JSON-fájl, akkor töltse le a fájlt az Azure blob storage. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Válassza ki az elemezni kívánt eszköz
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
   .
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Válassza ki az objektumot, amelyeket szeretne elemzéséhez, és nyomja le az **elemzés** gombra.
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Az a **folyamat media eszköz Media Analytics** ablakban válassza ki a processzor. 
   
    A többi a cikk azt ismerteti, miért és hogyan lehet minden processzor. 
5. Nyomja le az **létrehozása** számára a feladat elindítása.

## <a name="azure-media-indexer"></a>Azure Media Indexer
A **Azure Media Indexer** media processzor teszi lehetővé tegye médiafájlok és a tartalom kereshető, valamint a készítése lezárt feliratok követi nyomon. Ez a szakasz tájékoztatást nyújt a néhány a felügyeleti csomag a megadott beállításokat.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Nyelv
A természetes nyelvű, hogy felismerje a multimédia fájlban. Például angol és spanyol. 

### <a name="captions"></a>Akadálymentes feliratok
Kiválaszthatja a fejléc formátuma nem hoz létre a tartalomról. Az indexelő feladat feliratfájlokat fájlok hozhat létre a következő formátumban:  

* **SAMI**
* **TTML**
* **WebVTT**

Lezárt felirat (CC) az alábbi formátumokban fájlok használhatók hang- és fájlok elérhetővé a fogyatékkal élők nyújtanak segítséget.

### <a name="aib-file"></a>AIB fájl
Válassza ezt a lehetőséget, ha azt szeretné, hozza létre a hang Index Blob fájlt az egyéni SQL Server IFilter való használatra. További információkért lásd: [ez](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Kulcsszavak
Válassza ezt a lehetőséget, ha azt szeretné, a kulcsszavak XML-fájl létrehozásához. Ez a fájl tartalmazza a beszédfelismerés tartalom kinyert gyakorisággal és oszlopeltolási információ kulcsszavak.

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a feladat azonosításához. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalmat. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Az Azure Media Hyperlapse egy felügyeleti csomag által létrehozott zökkenőmentes idő letelt videók első, aki vagy művelet – kamera tartalomról.  További információ [ebben](media-services-hyperlapse-content.md) a témakörben érhető el. Ez a szakasz tájékoztatást nyújt a néhány a felügyeleti csomag a megadott beállításokat.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Gyorsaság
Adja meg a sebesség, amellyel a bemeneti videó felgyorsítása érdekében. Az eredménye egy stabil és az idő letelt verzióinak a bemeneti videó.

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a feladat azonosításához. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalmat. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
A **Azure Media Arcfelismerési érzékelő** media processzor (MP) lehetővé teszi a száma, nyomon követésére típusú áthelyezések, és akkor is fel tudja mérni a célközönség részvételét és reakciót arcfelismerést kifejezések keresztül. Ez a szolgáltatás két funkciókat tartalmazza: 

* **arcfelismerési észlelése**
  
    Arcfelismerési észlelési talál, és nyomon követi a videó emberi lapjaira. Több lapokat észlelhető, és ezt követően nyomon követheti az adott vissza egy JSON-fájlban ideje és helye metaadatokkal körül, mozgás. Nyomon követése, során a program megpróbálja adjon egységes azonosító ugyanazon felületére, amíg a személy van Navigálás a képernyőn, még akkor is, ha kényszerítő vagy röviden hagyja a keret.
  
  > [!NOTE]
  > A szolgáltatások nem végez arcfelismerést. Személy elhagyja a keret vagy a válik fedhetik túl hosszú kap egy új Azonosítót amikor azok tér vissza.
  > 
  > 
* **Érzelemfelismerés**
  
    Érzelemfelismerés Arcfelismerési észlelési adathordozó-processzor elemzés több érzelmi attribútum vissza a lapok észlel, például Boldogsága, sadness, félelem, utasítás és egyéb választható összetevőként. 

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Észlelési mód
A következő módok egyikét használhatja a processzor:

* arcfelismerési észlelése
* egy oldallal érzelemfelismerés
* összesített érzelemfelismerés

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a feladat azonosításához. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalmat. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
A **Azure Media mozgásérzékelő** media processzor (MP) lehetővé teszi a szakaszok egy egyébként hosszú és Eseménytelen video házirendsablonokkal hatékonyan azonosításához. Mozgásérzékelés statikus kamerák felvételei szakaszok a videó hol mozgásérzékelési jelentkezik azonosításához használhatja. Az időbélyegzőket és a határoló régió, ahol az esemény történt a metaadatokat tartalmazó JSON-fájlt hoz létre.

Cél biztonsági videó hírcsatornák, ez a technológia el tudja mozgásérzékelési kategorizálása kapcsolódó eseményeket és vakriasztások például árnyékok és megvilágítási módosításokat. Ez lehetővé teszi, hogy a biztonsági riasztások generálása kamera hírcsatornák közben igényt érdeklő pillanat kinyerése rendkívül hosszú felügyeleti videók végtelen irreleváns események, éppen címünkre nélkül.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
A processzor segítségével létre videók kijelölésével automatikusan érdekes kódtöredékek a forrás-videót. Ez akkor hasznos, ha lehetővé szeretné tenni, mi történik, a hosszú videó gyors áttekintést. Részletes útmutatást és példákat, lásd: [használata Azure Media Videoindexképek használatával egy videó összegzésének létrehozása](media-services-video-summarization.md)

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a feladat azonosításához. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalmat. 

## <a name="azure-media-content-moderator"></a>Az Azure Media tartalom moderátor
A processzor segítségével azonosíthatók a potenciális felnőtt és ellopható tartalom videók. A processzor pillanatfelvételek és kulcsképek automatikusan a videó észleli. A kulcsképek pontszámaihoz lehetséges felnőtt vagy ellopható tartalom, és alapértelmezett küszöbértékek alapján értékelést javasol. Részletes útmutatást és példákat, lásd: [használata Azure Media tartalom moderátor mérsékelt videók](media-services-content-moderation.md)

![Mérsékelt videók](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verzió 
Használja a "2.0-s".

### <a name="mode"></a>Mód
A 2.0-s verzió figyelmen kívül hagyja a `Mode` beállítást.

## <a name="next-steps"></a>További lépések
Nézet Media Services tanulási útvonalai.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
