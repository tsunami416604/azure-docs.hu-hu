---
title: A StorSimple 8000 sorozat eszközvezérlők kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan leállítása, újraindítása, állítsa le vagy alaphelyzetbe állítása a StorSimple eszköz tartományvezérlőket.
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
ms.openlocfilehash: 5e461f340e1c58f64c6d645a1e47cfd811bc4de5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261706"
---
# <a name="manage-your-storsimple-device-controllers"></a>A StorSimple eszköz tartományvezérlők kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple eszköz tartományvezérlőkön végrehajtható műveletek. A StorSimple eszközön a vezérlők redundáns (peer) tartományvezérlők egy aktív / passzív konfigurációt. Egy adott időben csak egy tartományvezérlő aktív, és a lemezek és a hálózati műveletek feldolgozása folyamatban van. A másik vezérlőre a passzív módban van. Ha az aktív vezérlő nem sikerül, a passzív vezérlő automatikusan aktiválódik.

Ebben az oktatóanyagban a eszközvezérlők kezeléséhez részletes utasításokat tartalmaz a:

* **Tartományvezérlők** az eszközt a StorSimple-Eszközkezelő szolgáltatás paneljén.
* Windows PowerShell storsimple-höz készült.

Azt javasoljuk, hogy Ön által kezelt eszköz vezérlő a StorSimple-Eszközkezelő szolgáltatáson keresztül. Ha a művelet csak a storsimple-höz készült Windows PowerShell használatával hajtható végre, az oktatóanyag teszi jegyezze fel.

Ebben az oktatóanyagban elolvasásával fogja tudni:

* Indítsa újra, vagy állítsa le a StorSimple eszköz vezérlő
* Állítsa le a StorSimple eszköz
* A StorSimple-eszköz gyári Alapértelmezések visszaállítása

## <a name="restart-or-shut-down-a-single-controller"></a>Indítsa újra, vagy csak egy vezérlő leállítása
A vezérlő újraindítása vagy leállítása, nem szükséges egy normál rendszer művelet részeként. Egy adott eszköz vezérlő leállítása műveletek gyakoriak csak abban az esetben, amelyben sikertelen eszköz hardverkomponensek cseréje szükséges. Egy vezérlő újraindítása is szükség lehet olyan helyzetekben, amelyekben teljesítmény túlzott mértékű memóriahasználat és a egy nem megfelelően működő tartományvezérlő által érintett. Is szükség lehet egy vezérlő újraindítása után sikeres vezérlő helyett, ha szeretné engedélyezni, és tesztelje a lecserélt vezérlő.

Eszköz újraindítása nem zavaró a csatlakoztatott kezdeményezőket, feltéve, hogy a passzív vezérlő nem érhető el. Ha a passzív vezérlő nem érhető el, és be van kapcsolva ki, majd az aktív vezérlő újraindítása a megszakítások időtartamát, szolgáltatás és az állásidőt eredményezhet.

> [!IMPORTANT]
> * **Egy futó tartományvezérlő kell soha nem kell fizikailag eltávolított, ez a redundancia adatvesztést és a egy állásidő megnövekedett kockázata eredményezne.**
> * Az alábbi eljárás csak a StorSimple fizikai eszköz vonatkozik. Indítás, Leállítás, és indítsa újra a StorSimple felhőalapú készülék kapcsolatos információkért lásd: [használata a felhőalapú berendezés](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

A storsimple az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás vagy a Windows PowerShell egyetlen eszköz vezérlő leállítása vagy újraindítása.

Az Azure Portalon lehet kezelni a eszközvezérlők, hajtsa végre az alábbi lépéseket.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Indítsa újra, vagy az Azure Portalon egy vezérlő leállítása
1. A StorSimple-Eszközkezelő szolgáltatáshoz, lépjen a **eszközök**. Az eszközök listájából válassza ki az eszközt. 

    ![Válasszon egy eszközt.](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Lépjen a **beállítások > tartományvezérlők**.
   
    ![Ellenőrizze, hogy a StorSimple eszközvezérlő kifogástalan állapotú](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Az a **tartományvezérlők** panelen ellenőrizze, hogy van-e az eszközön mindkét vezérlő állapota **kifogástalan**. Válassza ki a tartományvezérlő, kattintson a jobb gombbal, majd **indítsa újra a** vagy **Leállítás**.

    ![Válassza ki az újraindítás, vagy állítsa le a StorSimple eszköz vezérlők](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Indítsa újra, vagy állítsa le a vezérlő létrejön egy feladat, és lehetősége lesz vonatkozó figyelmeztetésekkel fejeződött be, ha van ilyen. Az újraindítás vagy leállítás monitorozásához lépjen **Service > tevékenységeket tartalmazó naplók** , és szűrjön a szolgáltatásra paraméter alapján. Ha a tartományvezérlő le volt állítva, majd meg kell kapcsolhatja be a vezérlő bekapcsolása kikapcsoló gombját.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Indítsa újra, vagy állítsa le a tartományvezérlő a Windows PowerShell storsimple-höz készült
A következő lépésekkel leállítani, vagy indítsa újra a csak egy vezérlő a StorSimple eszközön a Windows PowerShell storsimple-höz készült.

1. Az eszköz a soros konzol vagy egy telnet-munkamenet távoli számítógépről történő hozzáférést. Ha csatlakozni szeretne a vezérlő 0 vagy 1. vezérlő, kövesse [a PuTTY használata az eszköz soros konzoljához való kapcsolódáshoz](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**.
3. Címsorában látható, jegyezze fel a vezérlő csatlakozik (a 0. vezérlő vagy a vezérlő 1), és hogy-e az aktív vagy passzív (készenléti) tartományvezérlő.
   
   * Állítsa le egy egyetlen tartományvezérlő, a parancssorba írja be:
     
       `Stop-HcsController`
     
       Ezzel leállítja a tartományvezérlővel, amely csatlakozik. Ha megszakítja az aktív vezérlőt, majd az eszköz átadja a feladatokat a passzív vezérlő.

   * Indítsa újra a vezérlő, amikor a rendszer kéri, írja be:
     
       `Restart-HcsController`
     
       Ezzel újraindítja a tartományvezérlővel, amely csatlakozik. Ha újraindítja az aktív vezérlőt, hogy átadja a feladatokat a passzív vezérlő az újraindítás előtt.

## <a name="shut-down-a-storsimple-device"></a>Állítsa le a StorSimple eszköz

Ez a szakasz ismerteti, hogyan lehet leállítani egy futó vagy egy meghibásodott StorSimple-eszköz távoli számítógépről. Egy eszköz ki van kapcsolva, miután mindkét eszköz vezérlő leállt. Egy eszköz leállítás történik, amikor az eszköz fizikailag helyezett át, vagy nem működik készül.

> [!IMPORTANT]
> Mielőtt leállítja az eszközt, az eszköz összetevők állapotának ellenőrzéséhez. Keresse meg az eszközt, és kattintson a **beállítások > hardverállapot**. Az a **állapot és hardverállapot** panelen ellenőrizze, hogy az összes összetevő LED állapota zöld. Csak kifogástalan állapotú eszköz zöld állapotba került. Ha az eszköz pedig leáll le a cserélje le a hibás összetevő, látni fogja a sikertelen (piros) vagy a megfelelő összetevőknek a csökkentett teljesítményű (sárga) állapot.


#### <a name="to-shut-down-a-storsimple-device"></a>A StorSimple eszköz leállítása

1. Használja a [újraindítás és kikapcsolás vezérlő](#restart-or-shut-down-a-single-controller) eljárás azonosításához, és állítsa le a passzív vezérlő az eszközön. Elvégezheti ezt a műveletet az Azure Portalon vagy a Windows PowerShell storsimple-höz készült.
2. Ismételje meg a fenti az aktív vezérlő leállítása.
3. Meg kell most nézzük meg az eszközt vissza síkját. A két vezérlőn teljesen leállítja, miután kell mindkét vezérlő állapota LED-ek villogó piros. Kapcsolja ki az eszköz teljes mértékben jelenleg van szüksége, ha tükrözés Power-és hűtési modulok (PCMs) a power kapcsolók OFF helyére. Ez az eszköz célszerű kikapcsolni.

## <a name="reset-the-device-to-factory-default-settings"></a>Állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra

> [!IMPORTANT]
> Ha az eszköz visszaállítása a gyári alapértelmezett beállításokra van szüksége, forduljon a Microsoft Support. Az alább leírt eljárás csak a Microsoft Support együtt használandó.

Ez az eljárás ismerteti a Microsoft Azure StorSimple-eszköz visszaállítása a gyári alapértelmezett beállításokra storsimple-höz készült Windows PowerShell használatával.
A gyári eltávolítja az összes adatot és beállítást alapértelmezés szerint a teljes fürtöt.

Hajtsa végre az alábbi lépéseket a Microsoft Azure StorSimple-eszköz visszaállítása a gyári alapértelmezett beállításokra:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Az eszköz visszaállítja az alapértelmezett beállítások a Windows PowerShell storsimple-höz készült
1. A soros konzolon keresztül az eszköz elérésére. Ellenőrizze, győződjön meg arról, hogy csatlakozott a címsorában látható a **aktív** vezérlő.
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes hozzáféréssel**.
3. A parancssorba írja be az egész fürt, az összes adat, a metaadatokat és a vezérlő beállítások eltávolítása alaphelyzetbe állítása a következő parancsot:
   
    `Reset-HcsFactoryDefault`
   
    Helyette alaphelyzetbe egy adatkezelő, használja a [alaphelyzetbe állítása – HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) parancsmagot a `-scope` paraméter.)
   
    A rendszer többször újraindul. Értesíteni fogjuk, ha az alaphelyzetbe állítás sikeresen befejeződött. A rendszer modelltől függően 45-60 perc-8100-as eszköz és a egy 8600-as, a folyamat befejezéséhez 60 – 90 percet is igénybe vehet.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Kérdések és válaszok eszközvezérlők kezeléséről
Ebben a szakaszban azt kell foglalja össze a gyakori kérdésekre vonatkozó kezelése a StorSimple-eszközvezérlők.

**K.** Mi történik, ha az eszköz mindkét vezérlő kifogástalan állapotú és bekapcsolva a és e indítsa újra, vagy állítsa le az aktív vezérlőt?

**V.** Ha az eszközön mindkét vezérlő kifogástalan állapotú és van kapcsolva, akkor megerősítést kér. Dönthet úgy, hogy:

* **Indítsa újra az aktív vezérlőn** – értesítés jelenik meg, hogy a passzív vezérlő átveszi az eszköz aktív vezérlő újraindítása miatt. A vezérlő újraindítása.
* **Állítsa le az aktív vezérlő** – értesítés jelenik meg, hogy egy aktív vezérlő leállítása a állásidőt eredményez. Is kell főkapcsoló leküldése a vezérlő bekapcsolása az eszközön.

**K.** Mi történik, ha az eszköz a passzív vezérlő nem érhető el, és be van kapcsolva kikapcsolva, és nem indítsa újra, vagy állítsa le az aktív vezérlőt?

**V.** Ha az eszközön a passzív vezérlő nem érhető el, és be van kapcsolva kikapcsolva, és úgy dönt, hogy:

* **Indítsa újra az aktív vezérlőn** – értesítés jelenik meg, hogy a művelet folytatása egy ideiglenes a szolgáltatás megszakadását eredményezi, és a rendszer felszólítja a megerősítésre.
* **Állítsa le az aktív vezérlő** – értesítés jelenik meg, hogy a művelet folytatása állásidőt eredményez. Is kell főkapcsoló leküldéses legalább az egyik tartományvezérlőn az eszköz bekapcsolása. Megerősítést kér.

**K.** Ha nem a a vezérlő újraindítása és leállítása nem halad?

**V.** Egy vezérlő leállítása és újraindítása sikertelen lehet, ha:

* Egy eszköz frissítése folyamatban van.
* Már folyamatban van egy vezérlő újraindítása.
* Már folyamatban van egy vezérlő leállítása.

**K.** Hogyan lehet kideríteni Ha egy vezérlő újraindítása, vagy állítsa le?

**V.** A vezérlő állapota az adatkezelő panelen ellenőrizheti. A vezérlő állapota jelzi, hogy egy vezérlő újraindítása és leállítása folyamatban van a. Ezenkívül a **riasztások** panel az információs riasztások tartalmazzák, ha a vezérlő csomópontot újraindítják, vagy állítsa le. A vezérlő újraindítása és leállítása műveletek is tárolja, amely a Tevékenységnaplókban. Tevékenységnaplók kapcsolatos további információkért látogasson el [a Tevékenységnaplók megtekintése](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**K.** A vezérlő feladatátvétele miatt az i/o hatással van?

**V.** A TCP-kapcsolatok kezdeményezők és aktív vezérlő között visszaáll a vezérlő feladatátvétele miatt, de ha a passzív vezérlő feltételezi, hogy a művelet újra létrejön. Ez a művelet során előfordulhat, hogy lehet egy ideiglenes (30 másodpercnél rövidebb) szüneteltetése a kezdeményező és az eszköz közötti i/o.

**K.** Hogyan adja a vezérlő leállítása és eltávolítása után kiszolgálásához?

**V.** Térjen vissza a tartományvezérlő szolgáltatás, kell behelyezi a váz leírtak szerint [cserélje le a StorSimple eszköz vezérlő modul](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>További lépések
* Ha problémát tapasztal az ebben az oktatóanyagban szereplő eljárások használatával nem oldható fel a StorSimple eszközvezérlők [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos további információkért lépjen [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

