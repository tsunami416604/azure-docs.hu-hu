---
title: Hibaelhárítási útmutató az élő közvetítéshez | Microsoft dokumentumok
description: Ez a cikk javaslatokat ad az Azure Media Services élő streamelési problémáinak elhárítására.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: b00df5e8176aaad86be5cf3ae4e04c736f36cf5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885602"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Hibaelhárítási útmutató az élő streameléshez  

Ez a cikk javaslatokat ad az élő streamelési problémák elhárítására vonatkozóan.

## <a name="issues-related-to-on-premises-encoders"></a>A helyszíni kódolókkal kapcsolatos problémák
Ez a szakasz javaslatokat ad arra vonatkozóan, hogyan háríthatók el a helyszíni kódolókkal kapcsolatos problémák, amelyek úgy vannak beállítva, hogy egyetlen bitráta-adatfolyamot küldjenek az élő kódolásra engedélyezett AMS-csatornáknak.

### <a name="problem-would-like-to-see-logs"></a>Probléma: Szeretnélátni naplók
* **Lehetséges probléma:** Nem található olyan kódolónapló, amely segíthet a hibakeresési problémák megoldásában.
  
  * **Telestream Wirecast**: A naplók általában a\{C:\Users username}\AppData\Roaming\Wirecast\ alatt találhatók. 
  * **Elemental Live**: A felügyeleti portálon található naplókra mutató hivatkozásokat talál. Kattintson a **Statisztika**, majd **naplók**. A **Naplófájlok** lapon megjelenik az összes LiveEvent elem naplóinak listája; válassza ki az aktuális munkamenetnek megfelelőt. 
  * **Flash Media Live Encoder**: A **Napló könyvtára...** a **Kódolási napló** lapra navigálva található.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Probléma: Nincs lehetőség progresszív adatfolyam-adatfolyam-bevitelre
* **Potenciális probléma**: A használt kódoló nem automatikusan deinterlace. 
  
    **Hibaelhárítási lépések:** Keressen egy de-interlacing beállítást a kódoló felületén. Ha a delacing engedélyezve van, ellenőrizze újra a progresszív kimeneti beállításokat. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probléma: Több kódoló kimeneti beállítással próbálkozott, és még mindig nem tudott csatlakozni.
* **Potenciális probléma:** Az Azure kódolási csatornája nem lett megfelelően alaphelyzetbe állítva. 
  
    **Hibaelhárítási lépések:** Győződjön meg arról, hogy a kódoló már nem nyomja az AMS-t, állítsa le és állítsa alaphelyzetbe a csatornát. Ha ismét fut, próbálja meg csatlakoztatni a kódolót az új beállításokkal. Ha ez még mindig nem oldja meg a problémát, próbáljon meg teljesen új csatornát létrehozni, néha a csatornák több sikertelen kísérlet után is megsérülhetnek.  
* **Potenciális probléma**: A gop mérete vagy a kulcskeret beállításai nem optimálisak. 
  
    **Hibaelhárítási lépések:** Ajánlott GOP méret vagy keyframe időköz két másodperc. Egyes kódolók ezt a beállítást a keretek számában számítják ki, míg mások másodperceket használnak. Például: 30 fps kihelyezésekor a GOP mérete 60 képkocka lenne, ami 2 másodpercnek felel meg.  
* **Potenciális probléma**: A zárt portok blokkolják az adatfolyamot. 
  
    **Hibaelhárítási lépések:** RTMP-n keresztül történő streamelés esetén ellenőrizze a tűzfal és/vagy a proxy beállításait, és ellenőrizze, hogy az 1935-ös és 1936-os kimenő portok nyitva vannak-e. 

> [!NOTE]
> Ha a hibaelhárítási lépések követése után továbbra sem tudja streamelni, küldjön be egy támogatási jegyet az Azure Portalon keresztül.
> 
> 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

