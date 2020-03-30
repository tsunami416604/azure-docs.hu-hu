---
title: Médiaanyag-elemzés az Azure Portalon | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan dolgozhatja fel a médiafájlokat a Media Analytics médiaprocesszorokkal (MP) az Azure Portalhasználatával.
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
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462613"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>A médiatartalmak elemzése az Azure Portalon 

> [!IMPORTANT]
> Tekintse át egyes médiafeldolgozók [nyugdíjazási terveit.](media-services-analytics-overview.md#retirement-plans)

## <a name="overview"></a>Áttekintés
Az Azure Media Services Analytics beszéd- és látásösszetevők gyűjteménye (nagyvállalati méretekben, megfelelőségi, biztonsági és globális elérésű) összetevőkből, amelyek megkönnyítik a szervezetek és a vállalatok számára, hogy végrehajtható elemzéseket nyerjenek a videofájljaikból. Az Azure Media Services Analytics részletesebb áttekintését [ebben a](media-services-analytics-overview.md) témakörben találja. 

Ez a témakör bemutatja, hogyan dolgozhatja fel a médiafájlokat a Media Analytics médiaprocesszorokkal (MP) az Azure Portalhasználatával. A Media Analytics mp-ek MP4- vagy JSON-fájlokat készítenek. Ha egy médiaprocesszor MP4-fájlt hozott létre, fokozatosan letölti a fájlt. Ha egy médiaprocesszor létrehozott egy JSON-fájlt, letölti a fájlt az Azure blob storage.If a media processor produced a JSON file, you download the file from the Azure blob storage. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Az elemezni kívánt eszköz kiválasztása
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Válassza ki az elemezni kívánt eszközt, és nyomja meg az **Elemzés** gombot.
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. A **Media Analytics folyamata** ablakban válassza ki a processzort. 
   
    A cikk további része elmagyarázza, hogy miért és hogyan kell használni az egyes processzorok. 
5. Nyomja meg a **Létrehozás** gombot a feladat elindításához.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Az **Azure Media Indexer** médiaprocesszor lehetővé teszi a médiafájlok és a tartalom kereshetővé tévővé, valamint a feliratozási sávok létrehozásához. Ez a szakasz néhány részletet tartalmaz az mp-hez megadott beállításokról.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Nyelv
A multimédiás fájlban felismerendő természetes nyelv. Például angol vagy spanyol. 

### <a name="captions"></a>Akadálymentes feliratok
Kiválaszthatja a tartalomból generált feliratformátumot. Az indexelési feladat a következő formátumokban hozhat létre feliratfájlokat:  
 
* **TTML**
* **WebVTT**

Az ilyen formátumú feliratfájlok segítségével a hang- és videofájlok hozzáférhetők lehetnek a hallássérült ek számára.

### <a name="keywords"></a>Kulcsszavak
Akkor válassza ezt a lehetőséget, ha kulcsszó XML-fájlt szeretne létrehozni. Ez a fájl tartalmazza a kulcsszavakat kivont beszédtartalma, a frekvencia és ofszet információkat.

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladat azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

### <a name="speed"></a>Sebesség
Adja meg a bemeneti videó felgyorsításának sebességét. A kimenet a bemeneti videó stabilizált és időtelik változata.

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladat azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Az **Azure Media Face Detector médiafeldolgozó** (MP) lehetővé teszi a számlálást, a mozgások nyomon követését, és akár a közönség részvételét és reakcióját is felmérheti arckifejezéseken keresztül. Ez a szolgáltatás két funkciót tartalmaz: 

* **Arcfelismerés**
  
    Az arcfelismerés emberi arcokat talál és követ nyomon egy videón belül. Több arc észlelhető, majd nyomon követhető, ahogy mozognak, és az idő- és helymetaadatok at egy JSON-fájlban adják vissza. A követés során megpróbálja egységes azonosítót adni ugyanannak az arcnak, miközben a személy a képernyőn mozog, még akkor is, ha akadályoztatva van, vagy rövid időre elhagyja a keretet.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez arcfelismerést. Az egyén, aki elhagyja a keretet, vagy lesz akadályozta túl sokáig kap egy új azonosítót, amikor visszatérnek.
  > 
  > 
* **Érzelemfelismerés**
  
    Emotion Detection egy opcionális eleme a Face Detection Media Processor, hogy visszatér elemzés több érzelmi attribútumok az arcok észlelt, beleértve a boldogság, szomorúság, félelem, harag, és így tovább. 

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Észlelési mód
A processzor az alábbi módok egyikét használhatja:

* arcfelismerés
* arconkénti érzelemészlelése
* összesített érzelemfelismerés

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladat azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Az **Azure Media Motion Detector médiaprocesszor** (MP) lehetővé teszi, hogy hatékonyan azonosítsa az érdeklődési szakaszokat egy egyébként hosszú és eseménytelen videón belül. A mozgásérzékelés statikus kamerafelvételeken használható a videó azon részeinek azonosítására, ahol mozgás történik. Létrehoz egy JSON-fájlt, amely metaadatokat tartalmaz időbélyegekkel és az esemény előfordulási régiójával.

A biztonsági videocsatornákra irányuló technológia képes kategorizálni a mozgást a releváns eseményekbe és hamis pozitívumokba, például az árnyékokba és a megvilágítási változásokba. Ez lehetővé teszi, hogy biztonsági riasztásokat generáljon a kamera hírcsatornáiból anélkül, hogy végtelen irreleváns eseményekkel spamelne, miközben képes kivonni a rendkívül hosszú felügyeleti videók érdekes pillanatait.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Ez a processzor segít létrehozni összefoglalók a hosszú videók automatikusan kiválasztja érdekes kódrészletek a forrás videó. Ez akkor hasznos, ha gyors áttekintést szeretne adni arról, hogy mire számíthat egy hosszú videóban. Részletes információkat és példákat az [Azure Media videoindexképek használata videóösszegzés létrehozásához](media-services-video-summarization.md) című témakörben talál.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Feladat neve
Egy rövid név, amely lehetővé teszi a feladat azonosítását. [Ez](media-services-portal-check-job-progress.md) a cikk azt ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Rövid név, amely lehetővé teszi a kimeneti tartalom azonosítását. 

## <a name="azure-media-content-moderator"></a>Azure Media tartalommoderátor
Ez a processzor segít felismerni a potenciális felnőtt és pikáns tartalmat a videókban. A processzor automatikusan felismeri a felvételeket és a kulcsképeket a videóban. A lehetséges felnőtt vagy pikáns tartalom kulcsképkétcéljait pontozza, és az alapértelmezett küszöbértékek alapján javasol értékeléseket. Részletes információkat és példákat az [Azure Media tartalommoderátor használata videók moderálására című](media-services-content-moderation.md) témakörben talál.

![Mérsékelt videók](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verzió 
Használja a "2.0" kifejezést.

### <a name="mode"></a>Mód
A 2.0-s `Mode` verzió figyelmen kívül hagyja a beállítást.

## <a name="next-steps"></a>További lépések
A Media Services tanulási útvonalainak megtekintése.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
