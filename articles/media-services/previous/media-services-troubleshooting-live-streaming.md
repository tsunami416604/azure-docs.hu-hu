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
ms.openlocfilehash: 7e2fe84c51f93bdeb6cd99e23624d1796aff6c1f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Hibaelhárítási útmutató az élő streameléshez
Ez a témakör nyújt javaslatokat élő adatfolyam-továbbítási problémák elhárításáról.

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
  
    **Hibaelhárítási lépések**: GOP ajánlott méret vagy keyframe időköz érték 2 másodperc. Néhány kódolók kiszámításához képkockaszámát, ennek a beállításnak, míg mások használnak másodperc. Például: 30fps exportálásakor GOP méretének lenne 60 keretek, amely megegyezik 2 perc.  
* **Lehetséges probléma**: lezárt portok blokkolják az adatfolyam. 
  
    **Hibaelhárítási lépések**: amikor streaming RTMP keresztül, ellenőrizze a tűzfal és/vagy a proxy beállításait, és győződjön meg arról, hogy 1935 és 1936 kimenő portjai nyitva-e. RTP streaming használatakor győződjön meg arról, hogy nyitva-e kimenő port: 2010. 

### <a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Probléma: A kódoló adatfolyam a RTP protokoll konfigurálásakor nincs nincs helyet adjon meg egy állomásnevet.
* **Lehetséges probléma**: sok RTP kódolók állomásnevek nem engedélyezi, és egy IP-címet kell beolvasni.  
  
    **Hibaelhárítási lépések**: az IP-cím található, nyisson meg egy parancssort, minden olyan számítógépen. Ehhez a Windows, nyissa meg a Futtatás indító (WIN + K), és írja be a "cmd" lehetőségre.  
  
    Ha nyissa meg a parancssort, írja be a "Ping [AMS állomásnév]". 
  
    Az állomásnév, kihagyva a portot a Azure betöltési URL-címet, az alábbi példában a kijelölt származtathatók: 
  
    RTP://test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 
  
    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

> [!NOTE]
> Ha az még nem sikerült továbbíthat hibaelhárítási lépések után nyújt egy támogatási jegy az Azure portál használatával.
> 
> 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

