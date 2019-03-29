---
title: Az Azure portal használatával médiatartalmak elemzése |} A Microsoft Docs
description: Ez a témakör ismerteti a médiafeladat a médiaelemzés médiafeldolgozói (MPs) az Azure portal használatával.
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
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619441"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Médiatartalmak elemzése az Azure Portal használatával 
> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Áttekintés
Az Azure Médiaelemzés-szolgáltatások gyűjteménye, beszéd- és vizuális összetevők (a nagyvállalati szintű, megfelelőséget, biztonságot és globális elérésű), amelyek megkönnyítik a szervezetek és vállalatok számára, hogy használható elemzéseket készítsenek videófájljaikból. További részletes, az Azure Media Services Analytics áttekintése: [ez](media-services-analytics-overview.md) témakör. 

Ez a témakör ismerteti a médiafeladat a médiaelemzés médiafeldolgozói (MPs) az Azure portal használatával. Media Analytics felügyeleti csomagok termék MP4-fájlokat vagy a JSON-fájlokat. Ha egy médiaprocesszorral MP4-fájlokat, fokozatosan letölteni a fájlt. Ha egy médiaprocesszorral egy JSON-fájlt, töltse le a fájl az Azure blob storage-ból. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Válassza ki az elemezni kívánt adategység
1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. A **Settings** (Beállítások) ablakban válassza az **Assets** (Objektumok) lehetőséget.  
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Válassza ki a szeretné elemzése, és nyomja le az **elemzés** gombra.
   
    ![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Az a **médiaobjektum feldolgozása a Media Analytics szolgáltatással** ablakban válassza ki a processzor. 
   
    A cikk azt ismerteti, miért és hogyan használhatja az egyes processzor. 
5. Nyomja meg **létrehozás** , a feladat elindítása.

## <a name="azure-media-indexer"></a>Azure Media Indexer
A **Azure Media Indexer** médiafeldolgozót lehetővé teszi, hogy az adathordozó-fájlok és tartalmak kereshetővé, valamint a készítése a lezárt feliratozási nyomon követi. Ez a szakasz néhány, amely a felügyeleti csomag a megadott beállítások részleteit.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Nyelv
A multimédiás fájlban ismeri fel a természetes nyelv. Például angol vagy spanyol. 

### <a name="captions"></a>Akadálymentes feliratok
Kiválaszthatja egy a tartalomból generált felirat formátuma. Egy indexelési feladat feliratfájlok hozhat létre a következő formátumban:  

* **SAMI**
* **TTML**
* **WebVTT**

Ezek a formátumok a fájlok használhatók elérhetővé a hang- és videofájlok, a hallás fogyatékkal személyek (CC) felirat lezárva.

### <a name="aib-file"></a>AIB-fájl
Válassza ezt a lehetőséget, ha szeretné létrehozni a hang Index Blob fájlt, és az egyéni SQL Server ifilterrel való használatra. További információkért lásd: [ez](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Kulcsszavak
Válassza ezt a lehetőséget, ha szeretne egy kulcsszavak XML-fájl létrehozásához. Ez a fájl tartalmazza, gyakorisági és eltérési információkkal együtt a beszédtartalomból kinyert kulcsszavakat.

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a művelet azonosítására. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalom megbízható azonosításához. 

### <a name="speed"></a>Sebesség
Adja meg a sebesség, amellyel a bemeneti videó felgyorsítása érdekében. A bemeneti videó egy stabil és idő lejárt megjelenítés kimenete.

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a művelet azonosítására. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalom megbízható azonosításához. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
A **Azure Media Face detector használatával** médiafeldolgozót. (pont) lehetővé teszi a száma, nyomon követheti a áthelyezések száma – és még mérőműszer célközönség való részvétel és reagálás arckifejezések keresztül. Ez a szolgáltatás két funkciókat tartalmazza: 

* **Arcfelismerés**
  
    Arcfelismerés megkeresi és nyomon követi a videó emberi arcokat. Több arcok észlelése, és ezt követően a visszaadott JSON-fájlban időt és tartózkodási helyet metaadatokkal mozgás körül, követni. Során nyomon követése, megkísérli a konzisztens ID adhat az azonos face, bár a személy van Navigálás a képernyőn, még akkor is, ha azok kényszerítő, vagy röviden hagyja a keret.
  
  > [!NOTE]
  > A szolgáltatások arcfelismerés nem hajt végre. Olyan személy, akinek elhagyja a keretet, vagy a válik fedhetik túl sokáig kap egy új azonosító Ha vissza.
  > 
  > 
* **Érzelemfelismerő**
  
    Érzelemfelismerő adja vissza elemzési több érzelmi attribútumok az arcok észlelése esetén például boldogság, szomorúság, félelem, düh, és egyéb arcok észlelése médiafeldolgozó választható összetevője. 

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Észlelés üzemmód
A következő módok egyikét használhatja a processzor:

* Arcfelismerés
* arconkénti érzelemfelismerő
* összesített érzelemfelismerő

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a művelet azonosítására. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalom megbízható azonosításához. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
A **Azure Media Motion detector használatával** médiafeldolgozót. (pont) lehetővé teszi, hogy hatékonyan az egyes szakaszokhoz egy egyébként hosszú és Eseménytelen videó házirendsablonokkal. Mozgásérzékelés a statikus kamerák felvételei, azonosíthatja a videó szakaszok hol történik a mozgásban lévő adatoknak egyaránt használható. Az időbélyegeket és a határoló régió, ahol az esemény történt egy metaadatokat tartalmazó JSON-fájlt állít elő.

Biztonsági videóközvetítési céloz, ezt a technológiát is képes mozgásban lévő adatoknak egyaránt kategorizálásához kapcsolódó eseményeket, és a téves, például az árnyékok és megvilágítási módosításokat. Ez lehetővé teszi, hogy a biztonsági riasztások létrehozása a kamera hírcsatornák nélkül végtelen irreleváns eseményekkel rendelkező során képes arra, hogy a lényeges pillanat kinyerése rendkívül hosszú felügyeleti videók címünkre folyamatban van.

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
A processzor, a hosszú videókról összefoglalókat készíthet automatikusan válassza ki a forrás videó érdekes kódrészletek is segítenek. Ez akkor hasznos, ha lehetővé szeretné tenni mire számítson hosszú videó gyors áttekintést. Részletes információkért és példákért lásd: [használata az Azure Media Videóindexképek használatával egy videó összegzésének létrehozása](media-services-video-summarization.md)

![Videók elemzése](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Feladat neve
Egy rövid nevet, amely lehetővé teszi, hogy a művelet azonosítására. [Ez](media-services-portal-check-job-progress.md) a cikk ismerteti, hogyan figyelheti a feladat előrehaladását. 

### <a name="output-file"></a>Kimeneti fájl
Egy rövid nevet, amely lehetővé teszi a kimeneti tartalom megbízható azonosításához. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
A processzor segítségével észlelheti a potenciális felnőtt és szexuális tartalom videókat. A processzor automatikusan észleli helyességének és kulcsképek a videóban. A kulcskép pontszámmodell lehetséges felnőtt vagy pikáns tartalom, és értékelések alapértelmezett küszöbértékek alapján javasol. Részletes információkért és példákért lásd: [használata az Azure Media a Content Moderator közepes videók](media-services-content-moderation.md)

![Mérsékelt videók](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Verzió 
Használja a "2.0-s".

### <a name="mode"></a>Mód
A 2.0-s verzió figyelmen kívül hagyja a `Mode` beállítás.

## <a name="next-steps"></a>További lépések
Nézet Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
