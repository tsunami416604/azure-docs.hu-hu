---
title: Az adathordozó elemzése a Azure Portal használatával | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan lehet feldolgozni az adathordozót Media Analytics Media processors (MPs) használatával a Azure Portal segítségével.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 26a951ffaf0253371ffe69c6df798120f0464082
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881898"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Médiatartalmak elemzése az Azure Portal használatával 

> [!IMPORTANT]
> Tekintse át az egyes adathordozó-processzorok [nyugdíjazási terveit](media-services-analytics-overview.md#retirement-plans) .

## <a name="overview"></a>Áttekintés
A Azure Media Services Analytics a beszéd-és jövőkép-összetevők (nagyvállalati szintű, megfelelőség, biztonság és globális elérhetőség) gyűjteménye, amely megkönnyíti a szervezetek és a vállalatok számára, hogy a videofájlok alapján hasznosítható elemzéseket hozzanak létre. A Azure Media Services Analytics részletesebb áttekintését lásd [ebben a](media-services-analytics-overview.md) témakörben. 

Ez a témakör azt ismerteti, hogyan lehet feldolgozni az adathordozót Media Analytics Media processors (MPs) használatával a Azure Portal segítségével. Media Analytics MPs MP4-fájlokat vagy JSON-fájlokat hoz létre. Ha egy adathordozó-feldolgozó MP4-fájlt hozott létre, fokozatosan letölti a fájlt. Ha egy adathordozó-feldolgozó JSON-fájlt hozott létre, az Azure Blob Storage-ból tölti le a fájlt. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Válasszon ki egy elemezni kívánt eszközt
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Válassza ki az elemezni kívánt eszközt, és nyomja le az **elemzés** gombot.
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. A **folyamat Media asset Media Analytics** ablakban válassza ki a processzort. 
   
    A cikk további része az egyes processzorok használatának okát és használatát ismerteti. 
5. Kattintson a **Létrehozás** gombra a feladatok elindításához.

## <a name="azure-media-indexer"></a>Azure Media Indexer
A **Azure Media Indexer** adathordozó-feldolgozó lehetővé teszi a médiafájlok és a tartalmak kereshetővé tételét, valamint a kódolt feliratozási sávok létrehozását. Ez a szakasz részletesen ismerteti az ehhez a felügyeleti csomaghoz megadott beállításokat.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Nyelv
A multimédiás fájlban felismerhető természetes nyelv. Például: angol vagy spanyol. 

### <a name="captions"></a>Feliratok
A tartalomból generált képaláírás-formátumot is választhat. Az indexelési feladatok a következő formátumokban hozhatnak be zárt képaláírás-fájlokat:  

* **SZÁMI**
* **TTML**
* **WebVTT**

Az ezekben a formátumokban lezárt feliratú (CC) fájlok használhatók hang-és videofájlok elérhetővé tételéhez a fogyatékkal élők számára.

### <a name="aib-file"></a>AIB-fájl
Válassza ezt a beállítást, ha az audio index blob-fájlját elő szeretné állítani az egyéni SQL Server IFilter használatával való használatra. További információkért tekintse meg [ezt a](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogot.

### <a name="keywords"></a>Kulcsszavak
Válassza ezt a lehetőséget, ha XML-fájlt szeretne készíteni. Ez a fájl a beszédfelismerési tartalomból kinyert kulcsszavakat tartalmazza, a gyakorisággal és az eltolással kapcsolatos információkkal együtt.

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladatok azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan lehet figyelni a feladatok állapotát. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

### <a name="speed"></a>Gyorsaság
Adja meg azt a sebességet, amellyel fel szeretné gyorsítani a bemeneti videót. A kimenet a bemeneti videó stabilizált és időkorlátos kiadatását eredményezi.

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladatok azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan lehet figyelni a feladatok állapotát. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
A **Azure Media Face Detector** Media Processor (mp) lehetővé teszi a mozgások megszámlálását, nyomon követését, valamint az arc-kifejezéseken keresztüli közönség részvételének és reagálásának mérését. Ez a szolgáltatás két funkciót tartalmaz: 

* **Arcfelismerés**
  
    A Arcfelismerés megkeresi és nyomon követi az emberi arcokat a videón belül. Több arc is észlelhető, és ezt követően nyomon követhető, ahogy mozognak, a JSON-fájlban visszaadott idő és hely metaadataival. A nyomon követés során a rendszer megkísérli egy egységes azonosító megadását ugyanarra az oldalra, miközben a személy a képernyőn mozog, még akkor is, ha a felhasználó nem akadályozza vagy röviden elhagyják a keretet.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez arc-felismerést. Az a személy, aki elhagyja a keretet, vagy túl sokáig akadályozni fog, új azonosítót kap, amikor visszatérnek.
  > 
  > 
* **Érzelem észlelése**
  
    Az érzelem észlelése a Arcfelismerés adathordozó-feldolgozó egyik választható összetevője, amely az észlelt arcok több érzelmi attribútumának elemzését adja vissza, beleértve a boldogságot, a szomorúságot, a félelmet és a düh, valamint egyebeket. 

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Észlelési mód
A processzor a következő módok egyikét használhatja:

* Arcfelismerés
* Face érzelem észlelése
* érzelmek összesített észlelése

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladatok azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan lehet figyelni a feladatok állapotát. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
A **Azure Media Motion Detector** Media Processor (mp) lehetővé teszi, hogy hatékonyan azonosítsa a érdekeltségi részeket egy egyébként hosszú és eseménytelen videón belül. A mozgásérzékelő statikus kamerás felvételeken használható a videó azon részeinek azonosítására, ahol a mozgás történik. Létrehoz egy JSON-fájlt, amely tartalmazza a metaadatokat az időbélyegekkel, valamint az eseményt tartalmazó határ régiót.

A biztonsági videós hírcsatornák megkeresése révén ez a technológia a releváns eseményekre és a téves változásokra, például árnyékokra és a megvilágításra irányuló mozgásokat képes kategorizálni. Ez lehetővé teszi, hogy biztonsági riasztásokat készítsen a kamerás hírcsatornák számára, anélkül, hogy a végtelen lényegtelen események nélkül levélszemétbe kerül, miközben nagyon hosszú megfigyelési videókból kinyerheti a fontos pillanatokat

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Ez a processzor segítséget nyújt a hosszú videók összegzésének létrehozásához, így automatikusan kijelöli az érdekes kódrészleteket a forrás videóból. Ez akkor hasznos, ha gyors áttekintést szeretne nyújtani arról, hogy mire számíthat egy hosszú videóban. Részletes információkat és példákat a [videók összegzésének használata a Azure Media Video thumbnails használatával](media-services-video-summarization.md) című témakörben talál.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladatok azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan lehet figyelni a feladatok állapotát. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Ez a processzor segít felderíteni a videókban rejlő lehetséges felnőtteket és zamatos tartalmakat. A processzor automatikusan észleli a videóban lévő képeket és képkockákat. A szolgáltatás a lehetséges felnőtt vagy a zamatos tartalmakra is kiértékeli a képkockákat, és az alapértelmezett küszöbértékeken alapuló felülvizsgálatokat javasol. Részletes információkért és Példákért lásd: az [Azure Media Content moderator használata mérsékelt videókhoz](media-services-content-moderation.md)

![Mérsékelt videók](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verzió 
Használja az "2,0".

### <a name="mode"></a>Mód
Az 2,0-es verzió figyelmen kívül hagyja a `Mode` beállítást.

## <a name="next-steps"></a>Következő lépések
Megtekintheti Media Services képzési útvonalakat.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
