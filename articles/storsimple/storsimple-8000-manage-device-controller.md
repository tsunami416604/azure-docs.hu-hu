---
title: A StorSimple 8000 series eszközvezérlők kezelése |} Microsoft Docs
description: Megtudhatja, hogyan leállítása, újraindítása, állítsa le vagy alaphelyzetbe állítja a StorSimple eszköz tartományvezérlőket.
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
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875115"
---
# <a name="manage-your-storsimple-device-controllers"></a>A StorSimple eszköz tartományvezérlők kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag leírja a különböző műveletek végrehajtható a StorSimple eszköz tartományvezérlőn. A StorSimple eszköz tartományvezérlőinek redundáns (társközi) vezérlők aktív-passzív konfigurációban. Egy adott időben csak egy vezérlő aktív, és a lemezek és a hálózati műveletek feldolgozása. A többi tartományvezérlő a passzív módban van. Az aktív vezérlő nem sikerül, ha a passzív vezérlő automatikusan aktiválódik.

Ez az oktatóanyag segítségével történő kezeléséhez a eszközvezérlők részletes utasításokat tartalmaz a:

* **Tartományvezérlők** panel az eszközt a StorSimple eszköz Manager szolgáltatást.
* A Windows PowerShell-lel.

Azt javasoljuk, hogy a eszközvezérlők a StorSimple Device Manager szolgáltatáson keresztül kezelheti. Egy művelet csak a StorSimple Windows PowerShell használatával hajtható végre, ha az oktatóanyag teszi jegyezze fel azt.

Ez az oktatóanyag elolvasása, után fogja tudni:

* A StorSimple eszköz vezérlő leállítása vagy újraindítása
* Állítsa le a StorSimple eszköz
* A StorSimple eszközt a gyári beállítások visszaállítása

## <a name="restart-or-shut-down-a-single-controller"></a>Csak egy vezérlő leállítása vagy újraindítása
A tartományvezérlő újraindítás vagy leállítás nincs szükség a szokásos működésének részeként. Egyetlen eszköz tartományvezérlő leállítás műveletek esetében közös csak abban az esetben egy sikertelen eszköz hardverösszetevő használatához szükséges cserélni. Tartományvezérlő újraindításra is szükség lehet, amelyben teljesítmény túl sok memória használata, vagy egy nem megfelelően működő tartományvezérlő által érintett helyzetben. Is szükség lehet a vezérlő újraindítása után sikeres vezérlő helyettesíti, ha szeretné engedélyezni, és ellenőrizze a kicserélt vezérlőt.

Egy eszköz újraindítása nincs csatlakoztatott kezdeményezőkhöz, feltéve, hogy a passzív vezérlő érhető el őket. Ha a passzív tartományvezérlő nem érhető el, és be van kapcsolva ki, majd az aktív vezérlővel újraindítása eredményezhet a megszakítás a szolgáltatás és az állásidő.

> [!IMPORTANT]
> * **Egy futó tartományvezérlő kell soha nem kell fizikailag eltávolítani, mert ez adatvesztést redundanciát és az állásidő megnövekedett kockázata eredményezne.**
> * Az alábbi eljárás csak a StorSimple fizikai eszköz vonatkozik. Indítás, Leállítás, és indítsa újra a StorSimple felhő készülék kapcsolatos információkért lásd: [dolgozni a felhő készülék](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Indítsa újra, vagy állítsa le a StorSimple Device Manager szolgáltatás vagy a Windows PowerShell Azure-portálon egyetlen eszközt vezérlő lel.

Az Azure-portálról a eszközvezérlők kezeléséhez a következő lépésekkel.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Az Azure-portálon vezérlő leállítása vagy újraindítása
1. A StorSimple eszköz Manager szolgáltatást, lépjen **eszközök**. Válassza ki az eszköz az eszközök a listából. 

    ![Egy eszköz kiválasztása](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Ugrás a **beállítások > tartományvezérlői**.
   
    ![Ellenőrizze, megfelelő StorSimple eszközvezérlők](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Az a **tartományvezérlők** panelen ellenőrizze, hogy van-e az eszközön mindkét vezérlőhöz állapotának **kifogástalan**. Válassza ki a vezérlő. Kattintson a jobb gombbal, majd válassza ki **indítsa újra a** vagy **leállítása**.

    ![StorSimple eszközvezérlők leállítása vagy újraindítása kiválasztása](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Egy feladat jön létre, vagy állítsa le a tartományvezérlő, és lehetősége lesz vonatkozó figyelmeztetésekkel fejeződött be, ha van ilyen. Az újraindítás vagy leállítás figyeléséhez, keresse fel **szolgáltatás > tevékenységi naplóit** és szűréssel paraméterek a szolgáltatásra. Ha a tartományvezérlő le volt állítva, majd szüksége lesz, majd kapcsolja be, hogy a vezérlő bekapcsolása a főkapcsoló.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>A StorSimple a Windows PowerShell egy tartományvezérlőre leállítása vagy újraindítása
A következő lépésekkel leállítani, vagy indítsa újra a StorSimple csak egy vezérlő a StorSimple eszközön a Windows powershellből.

1. Az eszközt a soros konzol vagy egy telnet-munkamenet keresztül érhető el egy távoli számítógépről. Csatlakozhat a vezérlő 0 vagy 1 vezérlő, kövesse a [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
3. A szalagcím üzenetben jegyezze fel a vezérlő csatlakozik (a vezérlő 0 vagy a vezérlő 1), és hogy-e az aktív vagy passzív (készenléti) tartományvezérlő.
   
   * Állítsa le egyetlen tartományvezérlő, a parancssorba írja be:
     
       `Stop-HcsController`
     
       Ezzel leállítja a vezérlőre, amihez kapcsolódik. Ha megszakítja az aktív vezérlővel, majd az eszköz feladatátadás a passzív vezérlő.

   * Indítsa újra a tartományvezérlő, a parancssorba írja be:
     
       `Restart-HcsController`
     
       Ez újraindítja a vezérlőre, amihez kapcsolódik. Újraindítja az aktív vezérlővel, ha az nem keresztül a passzív vezérlő az újraindítás előtt.

## <a name="shut-down-a-storsimple-device"></a>Állítsa le a StorSimple eszköz

Ez a szakasz ismerteti, hogyan lehet leállítani egy futó vagy egy távoli számítógépről nem sikerült a StorSimple eszköz. Egy eszköz ki van kapcsolva, miután mindkét a eszközvezérlők állnak le. Egy eszköz leállítás történik, amikor az eszköz fizikailag helyezi át, vagy nem működik használatban van.

> [!IMPORTANT]
> Az eszköz leállítása előtt ellenőrizze az eszköz összetevők állapotát. Nyissa meg az eszközre, és kattintson a **beállítások > hardver állapotának**. Az a **állapotát és a hardver állapotának** panelen ellenőrizze, hogy a LED összes összetevő állapota zöld. Csak a megfelelő eszköz zöld állapotba került. Ha az eszköz épp most állítja le a hibás összetevő lecseréléséhez látni fogja a sikertelen (piros) vagy a megfelelő összetevője egy csökkent (sárga) állapotát.


#### <a name="to-shut-down-a-storsimple-device"></a>A StorSimple eszköz leállítása

1. Használja a [vezérlő leállítása vagy újraindítása](#restart-or-shut-down-a-single-controller) eljárás segítségével azonosíthatja, és állítsa le a passzív vezérlő az eszközön. Az Azure portálon vagy a Windows PowerShellben a StorSimple is végezheti ezt a műveletet.
2. Ismételje meg a fenti állítsa le az aktív vezérlőhöz.
3. Meg kell most nézze meg az eszköz a háttérben vezérlősík. A két vezérlő teljesen állnak le, miután kell mindkét vezérlőhöz állapotát LED villogó piros. Ha az eszköz teljesen kikapcsolása jelenleg van szüksége, tükrözés Power és hűtési modulok (PCMs) is a power kapcsolók a OFF állásba. Ez kapcsolja ki az eszközt.

## <a name="reset-the-device-to-factory-default-settings"></a>Állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra

> [!IMPORTANT]
> Ha az eszköz visszaállítása a gyári alapértelmezett beállításokra van szüksége, forduljon a Microsoft Support. Az alább ismertetett eljárás csak a Microsoft Support együtt használandó.

Ez az eljárás ismerteti a Microsoft Azure StorSimple eszköz visszaállítása a gyári alapértelmezett beállításokra StorSimple a Windows PowerShell használatával.
Gyári beállítások visszaállításával a eltávolítja minden adatot és beállítást alapértelmezés szerint a teljes fürtöt.

Végezze el a Microsoft Azure StorSimple eszköz visszaállítása a gyári alapértelmezett beállítások a következő lépéseket:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Alaphelyzetbe állítja az eszköz StorSimple a Windows PowerShell-alapértelmezett beállításai
1. A soros konzolon keresztül férnek hozzá az eszközt. Ellenőrizze, hogy csatlakozni tudjanak a szalagcím üzenetet a **aktív** vezérlő.
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
3. A parancssorba írja be az egész fürt számára, az összes adatokat, a metaadatok és a tartományvezérlő-beállítások eltávolítása alaphelyzetbe állítani a következő parancsot:
   
    `Reset-HcsFactoryDefault`
   
    Helyette alaphelyzetbe az egyetlen tartományvezérlő, használja a [alaphelyzetbe állítása-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) parancsmagot a `-scope` paraméter.)
   
    A rendszer többször újraindul. Ön értesítést fog kapni a visszaállítás sikeresen befejeződött. Attól függően, hogy a rendszer modell 8100-as eszköz és a 60-90 percig, amíg ez a folyamat befejezéséhez egy 8600 45-60 percig is tarthat.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Kérdések és válaszok eszközvezérlők kezelése
Ez a szakasz azt rendelkezik összegzett gyakran feltett kérdésekre vonatkozó kezelése StorSimple eszközvezérlők.

**K.** Mi történik, ha az eszközön mindkét vezérlőhöz kifogástalan és kikapcsolt be és indítsa újra, vagy állítsa le az aktív vezérlővel?

**V.** Ha az eszközön mindkét vezérlőhöz kifogástalan, és van kapcsolva, akkor megerősítést kér. Ki is:

* **Indítsa újra az aktív vezérlő** – értesítés jelenik meg, hogy az eszközt, hogy áthelyezze a feladatokat a passzív vezérlőhöz az aktív vezérlőhöz újraindítása miatt. A vezérlő indul újra.
* **Állítsa le az aktív vezérlőhöz** – értesítés jelenik meg, hogy az aktív vezérlőhöz leállítása a leállás eredményez. Szükség a főkapcsoló leküldéses kapcsolja be a vezérlő az eszközön.

**K.** Mi történik, ha a passzív vezérlő meg az eszköz nem érhető el, és be van kapcsolva a kikapcsolva, és indítsa újra, vagy állítsa le az aktív vezérlővel?

**V.** Ha az eszközön a passzív vezérlő ki van kapcsolva vagy nem érhető el, és úgy dönt, hogy:

* **Indítsa újra az aktív vezérlő** – értesítés jelenik meg, hogy a művelet folytatása a szolgáltatás ideiglenes megszűnésének fog eredményezni, és megerősítést kér.
* **Állítsa le az aktív vezérlőhöz** – értesítés jelenik meg, hogy a művelet folytatása leállást eredményez. Szükség a főkapcsoló leküldéses legalább az egyik tartományvezérlőn az eszköz bekapcsolása. Kell megerősítést kérni.

**K.** Ha nem a a tartományvezérlő újraindítása és leállítása sikertelen előrehaladás?

**V.** A vezérlő leállítása és újraindítása sikertelen lehet, ha:

* Egy eszköz frissítése folyamatban van.
* A tartományvezérlő újraindítása már folyamatban van.
* Egy tartományvezérlő leállítás már folyamatban van.

**K.** Hogyan meg kitalálja, hogy ha egy tartományvezérlő újraindult, vagy állítsa le?

**V.** A tartományvezérlő állapotát, a tartományvezérlő panelen ellenőrizheti. A tartományvezérlő állapotát jelzi, hogy a tartományvezérlő újraindítása és leállítása folyamatban van a. Emellett a **riasztások** panelre az információs riasztások tartalmaznia, ha a tartományvezérlő újraindult, vagy állítsa le. A vezérlő újraindítás és leállítás műveletek is tárolja, amely a valók naplókat. Valók naplók kapcsolatos további információkért látogasson el [megtekintése a tevékenységi naplóit](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**K.** Tartományvezérlő feladatátvétel miatt i/o gyakorolt hatás van?

**V.** A TCP-kapcsolatok kezdeményezők és az aktív vezérlő között vezérlő feladatátvétel miatt vissza lesz állítva, de hozni, ha a passzív vezérlő azt feltételezi, hogy a művelet. Valószínűleg i/o-tevékenységben kezdeményezők és az eszköz között (kevesebb, mint 30 másodperc) ideiglenes szünetet Ez a művelet során.

**K.** Hogyan térjen vissza a vezérlő számára a szolgáltatás leállítása és eltávolítása után?

**V.** A tartományvezérlő szolgáltatás visszaállításához kell behelyezi a váz leírtak [cserélje le a StorSimple eszköz vezérlő modul](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Következő lépések
* Ha problémába ütközik a StorSimple eszköz vezérlőkkel nem oldható fel az ebben az oktatóanyagban szereplő eljárások [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A StorSimple Device Manager szolgáltatással kapcsolatos további tudnivalókért keresse fel [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

