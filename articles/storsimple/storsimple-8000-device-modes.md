---
title: A StorSimple-eszköz üzemmódjának módosítása |} A Microsoft Docs
description: A StorSimple eszköz módok és ismerteti a storsimple-höz készült Windows PowerShell használata az eszköz üzemmódjának módosítása.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576089"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>A StorSimple-eszköz az eszköz üzemmódjának módosítása

A cikk ismerteti a különböző módokat, amely a StorSimple-eszköz működhet rövid leírását. A StorSimple-eszköz működhet a három mód: normal, a karbantartással és a helyreállítás.

Ez a cikk elolvasása után tudni fogja:

* Milyen a StorSimple eszköz módok
* Hogyan döntse el, melyik módban a StorSimple-eszköz szerepel.
* A normál és a karbantartási mód módosítása és *fordítva*

A fenti felügyeleti feladatokat csak a Windows PowerShell felületét a StorSimple eszköz használatával hajtható végre.

## <a name="about-storsimple-device-modes"></a>A StorSimple eszköz módokkal kapcsolatos

A StorSimple-eszköz működhet, normál, karbantartási vagy helyreállítási módban. Az egyes módokban röviden alább ismertetjük.

### <a name="normal-mode"></a>Normál módra

Ez a rendes működési módja egy teljes körűen konfigurált StorSimple-eszköz definiálható. Alapértelmezés szerint az eszköz normál üzemmódban kell lennie.

### <a name="maintenance-mode"></a>Karbantartási mód

A StorSimple-eszköz egyes esetekben szükség lehet karbantartási módba helyezni. Ebben a módban lehetővé teszi, hogy az eszköz karbantartásával, és telepítse a zavart okozó frissítések, például a lemezfirmware kapcsolatos.

A rendszer csak a Windows Powershellen keresztül karbantartási módba helyezheti a storsimple-höz készült. Ebben a módban az összes i/o-kérések fel van függesztve. Nem felejtő közvetlen elérésű memória (NVRAM) például szolgáltatások vagy a fürtözési szolgáltatás is le lesz állítva. Mindkét vezérlő újra lesz indítva, adja meg, vagy lépjen ki az ebben a módban. Miután kilépett a karbantartási módot, az összes szolgáltatás folytatódik, és kifogástalan állapotban kell lennie. Ez eltarthat néhány percig.

> [!NOTE]
> **Karbantartási mód csak egy megfelelően működő eszközön támogatott. Nem támogatott egy eszközön, amely az egyik vagy mindkét vezérlő nem működnek.**


### <a name="recovery-mode"></a>A helyreállítási mód

Helyreállítási módban, a "Windows hálózati támogatást csökkentett módban" írható le. Helyreállítási módban megkezdi a Microsoft Support csoporthoz, és lehetővé teszi, hogy a rendszer a diagnosztikai végezhetnek el. Helyreállítási módban elsődleges célja, hogy a rendszer naplók begyűjtéséről.

Ha a rendszer helyreállítási módba lép, forduljon a Microsoft Support a következő lépéseket. További információért ugorjon [forduljon a Microsoft támogatási](storsimple-8000-contact-microsoft-support.md).

> [!NOTE]
> **Az eszköz nem helyezhető el a következő helyreállítási módban. Ha az eszköz rossz állapotban van, a helyreállítási mód próbál, amelyben Microsoft Support megvizsgálhatja, hogy az eszköz előállhat.**

## <a name="determine-storsimple-device-mode"></a>StorSimple-eszköz üzemmódjának meghatározása

#### <a name="to-determine-the-current-device-mode"></a>A jelenlegi eszköz mód meghatározására.

1. Jelentkezzen be a lépéseket követve az eszköz soros konzoljához [a PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. Tekintse meg az eszköz a soros konzol menüjének címsorában látható. Ez az üzenet explicit módon azt jelzi, hogy az eszköz karbantartási vagy helyreállítási módban. Ha az üzenet nem tartalmaz minden olyan konkrét információkat a rendszer módot, az eszköz normál üzemmódban van.

## <a name="change-the-storsimple-device-mode"></a>A StorSimple-eszköz üzemmódjának módosítása

A StorSimple-eszköz (a normál módú) karbantartási módba helyezheti karbantartásával, vagy a karbantartási módú frissítések telepítése. Hajtsa végre az alábbi eljárásokat adja meg, vagy kilépett a karbantartási módból.

> [!IMPORTANT]
> Mielőtt karbantartási üzemmódba, győződjön meg arról, hogy mindkét eszközvezérlő kifogástalan állapotú elérésével a **eszközbeállítások > hardverállapot** az eszköz az Azure Portalon. Ha egyik vagy mindkét vezérlő nem kifogástalan, forduljon a Microsoft Support a következő lépésekhez. További információért ugorjon [forduljon a Microsoft támogatási](storsimple-8000-contact-microsoft-support.md).
 

#### <a name="to-enter-maintenance-mode"></a>Az adja meg a karbantartási mód

1. Jelentkezzen be a lépéseket követve az eszköz soros konzoljához [a PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**. Amikor a rendszer kéri, adja meg a **eszköz rendszergazdai jelszava**. Az alapértelmezett jelszava: `Password1`.
3. Írja be a parancssorba 
   
    `Enter-HcsMaintenanceMode`
4. Látni fogja, hogy karbantartási módba fog megszakítja az összes i/o-kérelmek és a kapcsolatot az Azure portal-kiszolgálóhoz, és megerősítést fog kérni, amely közli, figyelmeztető üzenetet. Típus **Y** , adja meg a karbantartási módot.
5. Mindkét vezérlő újraindul. Az újraindítás befejeződése után a soros konzol szalagcím jelzi, hogy az eszköz karbantartási módban van. Az alábbiakban egy példa látható a kimenetre.

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

#### <a name="to-exit-maintenance-mode"></a>Karbantartási módból való kilépéshez

1. Jelentkezzen be az eszköz soros konzoljához. Győződjön meg arról, hogy az eszköz karbantartási módban van címsorában látható a.
2. A parancssorba írja be a következőt:
   
    `Exit-HcsMaintenanceMode`
3. Megjelenik egy figyelmeztető üzenet és a egy megerősítő üzenetet. Típus **Y** karbantartási módból való kilépéshez.
4. Mindkét vezérlő újraindul. Az újraindítás befejeződése után a soros konzol szalagcím azt jelzi, hogy az eszköz normál üzemmódban. Az alábbiakban egy példa látható a kimenetre.

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

Ismerje meg, hogyan [normál és a karbantartási módú frissítések alkalmazása](storsimple-update-device.md) a StorSimple eszközön.

