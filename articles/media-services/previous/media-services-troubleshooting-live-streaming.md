---
title: Hibaelhárítási útmutató az élő streamelés |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt a javaslatok élő streamelési problémák elhárítása.
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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: e6b135e14f06ecf4edfbb97913c411f55711854a
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351452"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Hibaelhárítási útmutató az élő streameléshez
Ez a cikk áttekintést nyújt a javaslatok élő streamelési problémák elhárítása.

## <a name="issues-related-to-on-premises-encoders"></a>Helyszíni kódolók használatával kapcsolatos problémák
Ez a szakasz javaslatokat ad a helyszíni kódolókkal történő továbbítását egyféle sávszélességű adatfolyamot küldeni a valós idejű kódolásra képes csatornák AMS konfigurált kapcsolatos problémák elhárítása.

### <a name="problem-would-like-to-see-logs"></a>Probléma: Szeretné látni a naplók
* **Lehetséges probléma**: nem keresés encoder-naplók segíthetnek a hibaelhárítás során.
  
  * **Telestream Wirecast**: általában megtalálható a C:\Users alatt\{felhasználónév} \AppData\Roaming\Wirecast\ 
  * **Elemental Live**: naplók mutató hivatkozásokat tartalmaz a felügyeleti portálon találja. Kattintson a **Stats**, majd **naplók**. Az a **naplófájlok** lapon látni fogja a naplók a videókhoz-elemek listáját; válassza ki a megfelelő az aktuális munkamenet. 
  * **Flash Media Live Encoder**: annak a **naplókönyvtár...**  lépve a **kódolás Log** fülre.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Probléma: Nincs lehetőség az ad ki egy progresszív stream
* **Lehetséges probléma**: A használt kódoló nem automatikusan félképek összefésülése. 
  
    **Hibaelhárítási lépések**: keresse meg a kódoló felületen belül egy megszüntetéséhez kötésre lehetőséget. Váltakozó megszüntetéséhez engedélyezését követően újra fokozatos kimeneti beállításokat ellenőrzi. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probléma: Kódoló kimeneti több beállítást próbált meg, és továbbra is nem lehet csatlakozni.
* **Lehetséges probléma**: Azure kódolási csatorna nem megfelelően volt alaphelyzetbe. 
  
    **Hibaelhárítási lépések**: Ellenőrizze, hogy a kódoló már nem küld az AMS-nek, állítsa le, és alaphelyzetbe állítja a csatornát. Ha fut újra, próbáljon a kódoló az új beállításokkal. Ha ez még nem oldja meg a problémát, próbálja meg teljesen létrehozni egy új csatorna, néha csatornák megsérül több meghiúsult próbálkozást követően.  
* **Lehetséges probléma**: A Képcsoporttal méret és kulcs keret beállítások nem állnak optimális. 
  
    **Hibaelhárítási lépések**: Képcsoporttal ajánlott mérete vagy a kulcsképkocka időköz két másodperc. Néhány kódolók kiszámítása képkockaszámát, ezt a beállítást, míg mások használni. Például: 30 képkocka exportálásakor Képcsoporttal mérete lenne 60 keretek, amely 03T00 2 másodperc.  
* **Lehetséges probléma**: lezárt portok forgalomszűrők blokkolják a streamet. 
  
    **Hibaelhárítási lépések**: amikor streamelési RTMP-n keresztül, ellenőrizze a tűzfallal és/vagy a proxykiszolgáló beállításait, győződjön meg arról, hogy 1935 és 1936 kimenő portok nyitva-e. 

> [!NOTE]
> Ha az még nem sikerült továbbíthat hibaelhárítási lépések után az Azure portal használatával támogatási jegyet is küldhet.
> 
> 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

