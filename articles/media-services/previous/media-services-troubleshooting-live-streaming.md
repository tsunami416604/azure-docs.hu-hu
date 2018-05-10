---
title: Az élő adatfolyamként történő hibaelhárítási útmutatója |} Microsoft Docs
description: Ez a témakör nyújt javaslatokat élő adatfolyam-továbbítási problémák elhárításáról.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 84e3e9fc18671d7199eeaf638377a6681cf09fb4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Hibaelhárítási útmutató az élő streameléshez
Ez a cikk nyújt javaslatokat élő adatfolyam-továbbítási problémák elhárításáról.

## <a name="issues-related-to-on-premises-encoders"></a>A helyszíni kódolókkal kapcsolatos problémák
Ez a szakasz a helyszíni kódolókkal egyféle sávszélességű adatfolyamot küldeni AMS élő kódolásra képes csatornák konfigurált kapcsolatos problémák elhárítása javaslatokat nyújt.

### <a name="problem-would-like-to-see-logs"></a>Probléma: Szeretne látni a naplókat
* **Lehetséges probléma**: nem található kódoló naplózza, hogy elősegítheti hibáinak feltárására.
  
  * **Telestream Wirecast**: általában található naplók alapján C:\Users\{felhasználónév} \AppData\Roaming\Wirecast\ 
  * **Elemi Live**: hivatkozásokat a következő naplókban is rendelkezzen a felügyeleti portálon található. Kattintson a **statisztikák**, majd **naplók**. Az a **naplófájlok** lapon látni fogja a naplók LiveEvent elemeinek listáját; jelölje ki a megfelelő az aktuális munkamenet. 
  * **Élő kódoló adathordozó Flash**: megtalálhatja a **naplókönyvtár...**  navigáljon a **kódolás napló** fülre.

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Hiba: Nincs lehetőség a progresszív adatfolyam írása
* **Lehetséges probléma**: A használt kódoló nem automatikusan félképek összefésülése. 
  
    **Hibaelhárítási lépések**: keresse meg a kódoló felületen belül egy formátumból kötésre lehetőséget. Vonja rövidebbnek engedélyezése után újból fokozatos kimeneti beállításokat ellenőrzi. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Probléma: Kísérlet történt több kódoló kimeneti beállításait, és még nem lehet csatlakozni.
* **Lehetséges probléma**: Azure kódolási csatorna nem megfelelően alaphelyzetbe állítása. 
  
    **Hibaelhárítási lépések**: Győződjön meg arról, hogy a kódoló van már nem kérdez le, hogy AMS, állítsa le, és állítsa alaphelyzetbe a csatornát. Ha újra futtatni, próbáljon a kódoló az új beállításokkal. Ha ezt még nem sikerül megoldani a problémát, próbálja meg létrehozni teljesen új csatornát, néha csatornák megsérül több meghiúsult próbálkozást követően.  
* **Lehetséges probléma**: a rendszer a GOP méret és kulcskocka beállítások nem optimális. 
  
    **Hibaelhárítási lépések**: GOP ajánlott méret vagy keyframe időköz: két másodperc. Néhány kódolók kiszámításához képkockaszámát, ennek a beállításnak, míg mások használnak másodperc. Például: 30 fps exportálásakor GOP méretének lenne 60 keretek, amely megegyezik 2 perc.  
* **Lehetséges probléma**: lezárt portok blokkolják az adatfolyam. 
  
    **Hibaelhárítási lépések**: amikor streaming RTMP keresztül, ellenőrizze a tűzfal és/vagy a proxy beállításait, és győződjön meg arról, hogy 1935 és 1936 kimenő portjai nyitva-e. 

> [!NOTE]
> Ha az még nem sikerült továbbíthat hibaelhárítási lépések után nyújt egy támogatási jegy az Azure portál használatával.
> 
> 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

