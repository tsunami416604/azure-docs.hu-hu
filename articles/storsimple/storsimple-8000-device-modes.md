---
title: A StorSimple eszköz módváltás |} Microsoft Docs
description: A StorSimple eszköz módok és használatához a Windows PowerShell-lel módosíthatja eszköz módját ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dd160ede1189b0de544c8cf5db3b13228d212419
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875017"
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


### <a name="recovery-mode"></a>Helyreállítási módban

Helyreállítási módban, "A Windows csökkentett módban hálózati támogatás" jelentheti. Helyreállítási módban kapcsolatba lép a Microsoft Support csoport, és lehetővé teszi a diagnosztikát végezzen a rendszeren. Helyreállítási módban elsődleges célja a rendszernaplókat beolvasása.

Ha a rendszer helyreállítási módba kerül, a következő lépéshez lépjen kapcsolatba a Microsoft Support. További információkért látogasson el [forduljon a Microsoft ügyfélszolgálatához](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Az eszköz nem helyezhető el a következő helyreállítási módban. Ha az eszköz rossz állapotban van, helyreállítási módban próbálja meg olyan állapotba, amelyben Microsoft Support szakembereinek ellenőrizheti, hogy az eszköz eléréséhez.**

## <a name="determine-storsimple-device-mode"></a>Határozza meg a StorSimple eszköz mód

#### <a name="to-determine-the-current-device-mode"></a>Az aktuális eszköz mód meghatározásához

1. Jelentkezzen be az eszköz soros konzoljához lépéseit követve [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Tekintse meg az eszköz soros konzoljához menüjében a szalagcím üzenet. Ez az üzenet explicit módon azt jelzi, hogy az eszköz karbantartás vagy helyreállítási módban. Ha az üzenet nem tartalmaz a rendszer módra vonatkozó információk, az eszköz normál üzemmódban van.

## <a name="change-the-storsimple-device-mode"></a>A StorSimple eszköz üzemmód módosítása

Elhelyezhet a StorSimple eszköz karbantartási módba (a normál módba) karbantartás vagy a karbantartási mód frissítések telepítéséhez. Hajtsa végre a következő adja meg, vagy kilép karbantartási módból.

> [!IMPORTANT]
> Mielőtt karbantartási módba, ellenőrizze, hogy mindkét eszközvezérlők kifogástalan elérésével a **eszközbeállítások > hardver állapotának** az eszközt az Azure-portálon. Ha egyik vagy mindkét a tartományvezérlők nem működik megfelelően, a következő lépéseket illetően forduljon a Microsoft Support. További információkért látogasson el [forduljon a Microsoft ügyfélszolgálatához](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Adja meg a karbantartási módból való

1. Jelentkezzen be az eszköz soros konzoljához lépéseit követve [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszava: `Password1`.
3. Írja be a parancssorba 
   
    `Enter-HcsMaintenanceMode`
4. Látni fogja, hogy a karbantartási mód lesz megzavarhatják összes i/o-kérelmek és -kiszolgálón a kapcsolat az Azure portálra, és megerősítés bekéri felszólító figyelmeztető üzenetet. Típus **Y** a karbantartási üzemmódba.
5. Mindkét tartományvezérlők újraindul. Ha az újraindítás befejeződött, a soros konzol szalagcím tájékoztatja arról, hogy az eszköz karbantartási módban. Az alábbiakban egy példa látható a kimenetre.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>A kilépéshez a karbantartási mód

1. Jelentkezzen be az eszköz soros konzoljához. Ellenőrizze a szalagcím üzenet, amely az eszköz karbantartási módban van.
2. A parancssorba írja be:
   
    `Exit-HcsMaintenanceMode`
3. Ekkor megjelenik egy figyelmeztető üzenet, és egy megerősítő üzenetet. Típus **Y** kilép karbantartási módból.
4. Mindkét tartományvezérlők újraindul. Ha az újraindítás befejeződött, a soros konzol szalagcím azt jelzi, hogy az eszköz normál üzemmódban. Az alábbiakban egy példa látható a kimenetre.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [normál és a karbantartási mód frissítések alkalmazása](storsimple-update-device.md) a StorSimple eszköz.

