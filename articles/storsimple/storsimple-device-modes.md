---
title: "A StorSimple eszköz üzemmód módosítása |} Microsoft Docs"
description: "A StorSimple eszköz módok és használatához a Windows PowerShell-lel módosíthatja eszköz módját ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: e9d7d277-8a2f-45eb-9fef-355486e14cbc
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2016
ms.author: alkohli
ms.openlocfilehash: 33c65bf2eecff3914f3227c76f7d638a4507e1f6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>A StorSimple eszköz az eszköz üzemmód módosítása
A cikkben a különböző módokat, amelyben a StorSimple eszköz működhet rövid leírása. A StorSimple eszköz működhet három üzemmódban: normál, karbantartási és helyreállítás. 

A cikk elolvasása után tudni fogja:

* Milyen a StorSimple eszköz módok a következők:
* Mérje fel, melyik módban a StorSimple eszköz hogyan van
* A normál és a karbantartási mód módosítása és *ez fordítva is igaz*

A fenti kezelési feladatok csak a Windows PowerShell-felületet a StorSimple eszköz keresztül végezhető el.

## <a name="about-storsimple-device-modes"></a>A StorSimple eszköz módokkal kapcsolatos
A StorSimple eszköz normál, karbantartási vagy helyreállítási módban is működik. Módokban az alábbiakban röviden bemutatjuk.

### <a name="normal-mode"></a>Normál mód
Ez a teljesen konfigurált a StorSimple eszköz normál működési mód típusúként van definiálva. Alapértelmezés szerint az eszköz normál üzemmódban kell lennie.

### <a name="maintenance-mode"></a>A karbantartási mód
A StorSimple eszköz néha szükség lehet karbantartási módba kell helyezni. Ebben a módban lehetővé teszi az eszköz karbantartásához és zavaró frissítések, például a lemez belső vezérlőprogram kapcsolódó telepítése.

A StorSimple helyezhetik a rendszer csak a Windows PowerShell segítségével karbantartási módba. Az összes i/o-kérelmek fel van függesztve, ebben a módban. Például nem felejtő közvetlen elérésű memória (NVRAM) vagy a fürtözött szolgáltatás is le van állítva. Mindkét vezérlőhöz adja meg, vagy lépjen ki az ebben a módban újraindul. A karbantartási mód kilépéskor a szolgáltatások folytatódik, és kifogástalan kell lennie. Ez eltarthat néhány percig.

> [!NOTE]
> **A karbantartási mód csak egy megfelelően működő eszközön támogatott. Nem támogatott egy eszközön, amelyben a tartományvezérlők valamelyike nem megfelelően működnek.**
> </br>
> 
> 

### <a name="recovery-mode"></a>Helyreállítási módban
Helyreállítási módban, "A Windows csökkentett módban hálózati támogatás" jelentheti. Helyreállítási módban kapcsolatba lép a Microsoft Support csoport, és lehetővé teszi a diagnosztikát végezzen a rendszeren. Helyreállítási módban elsődleges célja a rendszernaplókat beolvasása.

Ha a rendszer helyreállítási módba kerül, a következő lépéshez lépjen kapcsolatba a Microsoft Support. További információkért látogasson el [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md).

> [!NOTE]
> **Az eszköz nem helyezhető el a következő helyreállítási módban. Ha az eszköz rossz állapotban van, helyreállítási módban próbálja meg olyan állapotba, amelyben Microsoft Support szakembereinek ellenőrizheti, hogy az eszköz eléréséhez.**
> 
> 

## <a name="determine-storsimple-device-mode"></a>Határozza meg a StorSimple eszköz mód
#### <a name="to-determine-the-current-device-mode"></a>Az aktuális eszköz mód meghatározásához
1. Jelentkezzen be az eszköz soros konzoljához lépéseit követve [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. Tekintse meg az eszköz soros konzoljához menüjében a szalagcím üzenet. Ez az üzenet explicit módon azt jelzi, hogy az eszköz karbantartás vagy helyreállítási módban. Ha az üzenet nem tartalmaz a rendszer módra vonatkozó információk, az eszköz normál üzemmódban van.

## <a name="change-the-storsimple-device-mode"></a>A StorSimple eszköz üzemmód módosítása
Elhelyezhet a StorSimple eszköz karbantartási módba (a normál módba) karbantartás vagy a karbantartási mód frissítések telepítéséhez. Hajtsa végre a következő adja meg, vagy kilép karbantartási módból.

> [!IMPORTANT]
> Mielőtt karbantartási módba, ellenőrizze, hogy mindkét eszközvezérlők kifogástalan elérésével a **hardverállapot** a a **karbantartási** a klasszikus Azure portálra a lap. Ha egyik vagy mindkét a tartományvezérlők nem működik megfelelően, a következő lépéseket illetően forduljon a Microsoft Support. További információkért látogasson el [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md).
> 
> 

#### <a name="to-enter-maintenance-mode"></a>Adja meg a karbantartási módból való
1. Jelentkezzen be az eszköz soros konzoljához lépéseit követve [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszava: `Password1`.
3. Írja be a parancssorba 
   
    `Enter-HcsMaintenanceMode`
4. Látni fogja, hogy a karbantartási mód lesz megzavarhatják összes i/o-kérelmek és -kiszolgálón a kapcsolat a klasszikus Azure portálra, és megerősítés bekéri felszólító figyelmeztető üzenetet. Típus **Y** a karbantartási üzemmódba.
5. Mindkét tartományvezérlők újraindul. Ha az újraindítás befejeződött, egy másik üzenet jelenik meg arról, hogy az eszköz karbantartási módban.

#### <a name="to-exit-maintenance-mode"></a>A kilépéshez a karbantartási mód
1. Jelentkezzen be az eszköz soros konzoljához. Ellenőrizze a szalagcím üzenet, amely az eszköz karbantartási módban van.
2. A parancssorba írja be:
   
    `Exit-HcsMaintenanceMode`
3. Ekkor megjelenik egy figyelmeztető üzenet, és egy megerősítő üzenetet. Típus **Y** kilép karbantartási módból.
4. Mindkét tartományvezérlők újraindul. Ha az újraindítás befejeződött, egy másik üzenet jelenik meg arról, hogy az eszköz normál üzemmódban.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [normál és a karbantartási mód frissítések alkalmazása](storsimple-update-device.md) a StorSimple eszköz.

