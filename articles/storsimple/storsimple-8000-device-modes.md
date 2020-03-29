---
title: StorSimple eszközmód módosítása | Microsoft dokumentumok
description: A StorSimple eszközmódok ismertetése, és bemutatja, hogyan használhatja a Windows PowerShell for StorSimple eszközmód módosítása.
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
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60576089"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Az eszköz mód módosítása a StorSimple eszközön

Ez a cikk röviden ismerteti a Különböző módok, amelyben a StorSimple-eszköz működhet. A StorSimple eszköz három üzemmódban is működhet: normál, karbantartás és helyreállítás.

Elolvasása után ezt a cikket, akkor tudni fogja:

* Mi a StorSimple eszköz mód
* Hogyan lehet kitalálni, hogy melyik módban van a StorSimple eszköz
* Hogyan változtassunk normál ról karbantartási módra, és *fordítva*

A fenti felügyeleti feladatok csak a StorSimple-eszköz Windows PowerShell felületén keresztül hajthatók végre.

## <a name="about-storsimple-device-modes"></a>A StorSimple eszközmódokról

A StorSimple eszköz normál, karbantartási vagy helyreállítási módban is működhet. Az alábbiakban röviden ismertetjük ezeket a módokat.

### <a name="normal-mode"></a>Normál mód

Ez a teljesen konfigurált StorSimple-eszköz normál működési módja. Alapértelmezés szerint az eszköznek normál módban kell lennie.

### <a name="maintenance-mode"></a>Karbantartási mód

Előfordulhat, hogy a StorSimple eszközt karbantartási módba kell helyezni. Ez a mód lehetővé teszi az eszközön a karbantartást, és zavaró frissítések telepítését, például a lemez belső vezérlőprogramjához kapcsolódó frissítéseket.

A rendszert csak a StorSimple-hez használható Windows PowerShell en keresztül helyezheti karbantartási módba. Ebben a módban minden I/O-kérés szünetel. Az olyan szolgáltatások, mint a nem felejtő véletlen hozzáférésű memória (NVRAM) vagy a fürtözési szolgáltatás is leállnak. Mindkét vezérlő újraindul, amikor belép vagy kilép ebből a módból. Amikor kilép a karbantartási módból, az összes szolgáltatás folytatódik, és kifogástalan állapotúnak kell lennie. Ez eltarthat néhány percig.

> [!NOTE]
> **A karbantartási mód csak megfelelően működő eszközön támogatott. Nem támogatott olyan eszközön, amelyben az egyik vagy mindkét vezérlő nem működik.**


### <a name="recovery-mode"></a>Helyreállítási mód

A helyreállítási mód "Hálózati támogatással rendelkező Windows csökkentett mód". A helyreállítási mód bevonja a Microsoft támogatási csapatát, és lehetővé teszi számukra a rendszer diagnosztikaelvégzését. A helyreállítási mód elsődleges célja a rendszernaplók beolvasása.

Ha a rendszer helyreállítási módba lép, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. További információt a [Microsoft támogatási szolgálatának felkeresve talál.](storsimple-8000-contact-microsoft-support.md)

> [!NOTE]
> **Az eszköz nem helyezhető helyreállítási módba. Ha az eszköz rossz állapotban van, a helyreállítási mód megpróbálja az eszközt olyan állapotba hozni, amelyben a Microsoft támogatási személyzete megvizsgálhatja azt.**

## <a name="determine-storsimple-device-mode"></a>StorSimple-eszköz üzemmódjának megállapítása

#### <a name="to-determine-the-current-device-mode"></a>Az aktuális eszközmód meghatározása

1. Jelentkezzen be az eszköz soros konzoljára a [PuTTY használata az eszköz soros konzoljához való csatlakozáslépései](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)vel.
2. Nézze meg a szalagcím üzenetet a készülék soros konzolmenüjében. Ez az üzenet egyértelműen jelzi, hogy az eszköz karbantartási vagy helyreállítási módban van-e. Ha az üzenet nem tartalmaz a rendszermódra vonatkozó konkrét információkat, az eszköz normál módban van.

## <a name="change-the-storsimple-device-mode"></a>A StorSimple eszközmód módosítása

A StorSimple eszközt karbantartási módba helyezheti (normál módból) a karbantartási vagy karbantartási módfrissítések telepítéséhez. A karbantartási módba való belépéshez vagy kilépéshez hajtsa végre az alábbi eljárásokat.

> [!IMPORTANT]
> A karbantartási módba való belépés előtt ellenőrizze, hogy mindkét eszközvezérlő kifogástalan állapotú-e az **Eszköz hardverállapotának > elérésével** az Azure Portalon. Ha az egyik vagy mindkét vezérlő nem kifogástalan, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. További információt a [Microsoft támogatási szolgálatának felkeresve talál.](storsimple-8000-contact-microsoft-support.md)
 

#### <a name="to-enter-maintenance-mode"></a>A karbantartási módba való belépéshez

1. Jelentkezzen be az eszköz soros konzoljára a [PuTTY használata az eszköz soros konzoljához való csatlakozáslépései](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)vel.
2. A soros konzol menüjében **Log in with full access**válassza az 1. Amikor a rendszer kéri, adja meg az **eszköz rendszergazdai jelszavát.** Az alapértelmezett jelszó: `Password1`.
3. A parancssorba írja be a következőt: 
   
    `Enter-HcsMaintenanceMode`
4. Megjelenik egy figyelmeztető üzenet, amely arról szól, hogy a karbantartási mód megszakítja az összes I/O-kérést, és megszakítja a kapcsolatot az Azure Portalon, és a rendszer megerősítést kér. A karbantartási módba való belépéshez írja be az **Y** parancsot.
5. Mindkét vezérlő újraindul. Amikor az újraindítás befejeződött, a soros konzol szalagasztere jelzi, hogy az eszköz karbantartási üzemmódban van. Az alábbiakban egy példa látható a kimenetre.

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

#### <a name="to-exit-maintenance-mode"></a>A karbantartási módból való kilépés

1. Jelentkezzen be az eszköz soros konzoljára. Ellenőrizze a szalagcímüzenetből, hogy az eszköz karbantartási üzemmódban van.Verify from the banner message that your device is in maintenance mode.
2. A parancssorba írja be a következőt:
   
    `Exit-HcsMaintenanceMode`
3. Egy figyelmeztető üzenet és egy megerősítő üzenet jelenik meg. Írja be az **Y** parancsot a karbantartási módból való kilépéshez.
4. Mindkét vezérlő újraindul. Amikor az újraindítás befejeződött, a soros konzol szalagasztere jelzi, hogy az eszköz normál módban van. Az alábbiakban egy példa látható a kimenetre.

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

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [alkalmazhat normál és karbantartási módfrissítéseket](storsimple-update-device.md) a StorSimple eszközön.

