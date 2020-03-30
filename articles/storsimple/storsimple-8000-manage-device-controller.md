---
title: StorSimple 8000 sorozatú eszközvezérlők kezelése | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthatja le, indítható újra, állíthatja le vagy állíthatja alaphelyzetbe a StorSimple eszközvezérlőket.
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
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267767"
---
# <a name="manage-your-storsimple-device-controllers"></a>A StorSimple eszközvezérlők kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple-eszközvezérlőkön elvégezhető különböző műveleteket. A StorSimple-eszköz vezérlői redundáns (társ)vezérlők aktív-passzív konfigurációban. Egy adott időpontban csak egy vezérlő aktív, és az összes lemez- és hálózati műveletet feldolgozza. A másik vezérlő passzív módban van. Ha az aktív vezérlő meghibásodik, a passzív vezérlő automatikusan aktívvá válik.

Ez az oktatóanyag lépésenkénti utasításokat tartalmaz az eszközvezérlők kezeléséhez a következők használatával:

* **Vezérlők** panel az eszköz höz a StorSimple Eszközkezelő szolgáltatásban.
* Windows PowerShell a StorSimple számára.

Azt javasoljuk, hogy az eszközvezérlők kezelése a StorSimple Eszközkezelő szolgáltatáson keresztül. Ha egy művelet csak a StorSimple Windows PowerShell használatával hajtható végre, az oktatóanyag feljegyzi azt.

Elolvasása után ez a bemutató, akkor képes lesz arra, hogy:

* StorSimple eszközvezérlő újraindítása vagy leállítása
* StorSimple eszköz leállítása
* Állítsa vissza a StorSimple-eszközt a gyári alapértékekre

## <a name="restart-or-shut-down-a-single-controller"></a>Egyetlen vezérlő újraindítása vagy leállítása
A vezérlő újraindítása vagy leállítása nem szükséges a rendszer normál működésének részeként. Egyetlen eszközvezérlő leállítási műveletei csak olyan esetekben gyakoriak, amikor egy meghibásodott eszközhardver-összetevőt ki kell cserélni. A vezérlő újraindítására akkor is szükség lehet, ha a teljesítményt a túlzott memóriahasználat vagy a hibás vezérlő befolyásolja. Ha engedélyezni és tesztelni szeretné a lecserélt vezérlőt, akkor előfordulhat, hogy a sikeres vezérlőcsere után újra kell indítania a vezérlőt.

Az eszköz újraindítása nem okoz zavart a csatlakoztatott kezdeményezők működésében, feltéve, hogy a passzív vezérlő elérhető. Ha a passzív vezérlő nem érhető el vagy ki van kapcsolva, az aktív vezérlő újraindítása a szolgáltatás megszakadását és leállást eredményezhet.

> [!IMPORTANT]
> * **A futó vezérlőt soha nem szabad fizikailag eltávolítani, mivel ez a redundancia elvesztését és az állásidő fokozott kockázatát eredményezné.**
> * A következő eljárás csak a StorSimple fizikai eszközre vonatkozik. A StorSimple felhőalapú berendezés elindításáról, leállításáról és újraindításáról [a Felhőalapú készülék munkája](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance)című témakörben talál.

Egyetlen eszközvezérlő t indíthat újra vagy állíthatj le a StorSimple Eszközkezelő szolgáltatás Azure-portálján vagy a StorSimple-hez készült Windows PowerShell használatával.

Az eszközvezérlők azure-portálról történő kezeléséhez hajtsa végre a következő lépéseket.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Vezérlő újraindítása vagy leállítása az Azure Portalon
1. A StorSimple Eszközkezelő szolgáltatásban nyissa meg az **Eszközök**lehetőséget. Válassza ki az eszközt az eszközök listájából. 

    ![Válasszon egy eszközt.](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Nyissa meg **a Beállítások > vezérlők lehetőséget.**
   
    ![A StorSimple eszközvezérlők kifogástalan állapotának ellenőrzése](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. A **Vezérlők** panelen ellenőrizze, hogy az eszköz mindkét vezérlőjének állapota **kifogástalan-e.** Jelöljön ki egy vezérlőt, kattintson a jobb gombbal, majd válassza **az Újraindítás** vagy **a Leállítás parancsot.**

    ![Válassza ki a StorSimple eszközvezérlők újraindítását vagy leállítását](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Létrejön egy feladat a vezérlő újraindításához vagy leállításához, és a megfelelő figyelmeztetések jelennek meg, ha vannak ilyenek. Az újraindítás vagy a leállítás figyeléséhez nyissa meg **a Service > tevékenységnaplókat,** és szűrje a szolgáltatásra jellemző paraméterek szerint. Ha egy vezérlőt leállítottak, akkor meg kell nyomnia a bekapcsológombot, hogy bekapcsolja a vezérlőt.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Vezérlő újraindítása vagy leállítása a StorSimple-hez készült Windows PowerShell ben
Hajtsa végre az alábbi lépéseket a StorSimple-eszközön lévő egyetlen vezérlő leállításához vagy újraindításához a StorSimple-hez készült Windows PowerShellből.

1. Az eszköz elérése a soros konzolon vagy a telnet munkameneten keresztül távoli számítógépről. A 0 vagy 1 vezérlőhöz való csatlakozáshoz kövesse a [PuTTY használata](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)az eszköz soros konzoljához való csatlakozás lépéseit.
2. A soros konzol menüjében **Log in with full access**válassza az 1.
3. A szalagcímüzenetben jegyezze fel azt a vezérlőt, amelyhez csatlakozik (0 vezérlő vagy 1 vezérlő), és hogy az aktív vagy passzív (készenléti) vezérlőről van-e szó.
   
   * Egyetlen vezérlő leállításához írja be a következőt:
     
       `Stop-HcsController`
     
       Ezzel leállítja azt a vezérlőt, amelyhez csatlakozik. Ha leállítja az aktív vezérlőt, akkor az eszköz átadja a feladatokat a passzív vezérlőnek.

   * Vezérlő újraindításához írja be a parancssorba a következőt:
     
       `Restart-HcsController`
     
       Ezzel újraindítja azt a vezérlőt, amelyhez csatlakozik. Ha újraindítja az aktív vezérlőt, az újraindítás előtt átadja a feladatokat a passzív vezérlőnek.

## <a name="shut-down-a-storsimple-device"></a>StorSimple eszköz leállítása

Ez a szakasz bemutatja, hogyan lehet leállítani egy futó vagy egy sikertelen StorSimple-eszközt egy távoli számítógépről. Az eszköz ki van kapcsolva, miután mindkét eszközvezérlő leállt. Az eszköz leállítása akkor történik, amikor az eszközt fizikailag áthelyezik, vagy üzemen kívül helyezik.

> [!IMPORTANT]
> Az eszköz leállítása előtt ellenőrizze az eszköz összetevőinek állapotát. Nyissa meg az eszközt, majd kattintson **a Beállítások > hardver állapota parancsra.** Az **Állapot és hardver állapot** panelen ellenőrizze, hogy az összes összetevő LED-állapota zöld-e. Csak egy kifogástalan állapotú eszköz rendelkezik zöld állapottal. Ha az eszköz leállítása egy hibás összetevő cseréje érdekében történik, az adott összetevő(k) hibás (piros) vagy degradálódott (sárga) állapotot fog látni.


#### <a name="to-shut-down-a-storsimple-device"></a>StorSimple eszköz leállítása

1. Használja az [újraindítást, vagy állítsa le a vezérlő](#restart-or-shut-down-a-single-controller) eljárás azonosítására és leállítására a passzív vezérlő az eszközön. Ezt a műveletet az Azure Portalon vagy a Windows PowerShell storSimple.
2. Ismételje meg a fenti lépést az aktív vezérlő leállításához.
3. Most meg kell néznie a készülék hátsó síkját. A két vezérlő teljes leállítása után mindkét vezérlő állapotJELZŐ LED-jének pirosan kell villognia. Ha most teljesen ki kell kapcsolnia a készüléket, fordítsa ki a táp- és hűtőmodulok (PCM) áramellátását KI állásba. Ez majd kikapcsolja az eszközt.

## <a name="reset-the-device-to-factory-default-settings"></a>Az eszköz visszaállítása a gyári alapértelmezett beállításokra

> [!IMPORTANT]
> Ha vissza kell állítania az eszközt a gyári alapértelmezett beállításokra, forduljon a Microsoft támogatási szolgálatához. Az alábbiakban ismertetett eljárás csak a Microsoft támogatási szolgálatával együtt használható.

Ez az eljárás azt ismerteti, hogyan állíthatja vissza a Microsoft Azure StorSimple-eszközt a gyári alapértelmezett beállításokra a Windows PowerShell for StorSimple használatával.
Az eszköz alaphelyzetbe állítása alapértelmezés szerint eltávolítja az összes adatot és beállítást a teljes fürtből.

Hajtsa végre az alábbi lépéseket a Microsoft Azure StorSimple-eszköz gyári alapértelmezett beállításainak visszaállításához:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Az eszköz alaphelyzetbe állítása a StorSimple-hez való Windows PowerShell alapértelmezett beállításaira
1. Az eszköz elérése a soros konzolon keresztül. Ellenőrizze a szalagcímüzenetet, és győződjön meg arról, hogy csatlakozik az **Aktív** vezérlőhöz.
2. A soros konzol menüjében **Log in with full access**válassza az 1.
3. A parancssorba írja be a következő parancsot a teljes fürt alaphelyzetbe állításához, eltávolítva az összes adatot, metaadatot és vezérlőbeállítást:
   
    `Reset-HcsFactoryDefault`
   
    Egyetlen vezérlő alaphelyzetbe állításához használja a [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) parancsmamot a `-scope` paraméterrel.)
   
    A rendszer többször is újraindul. Értesítést fog kapni, ha az alaphelyzetbe állítás sikeresen befejeződött. A rendszermodelltől függően egy 8100-as eszköz esetében 45–60 perc, a 8600-as pedig 60–90 perc alatt fejeződhet be a folyamat.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Kérdések és válaszok az eszközvezérlők kezelésével kapcsolatban
Ebben a szakaszban összefoglaltuk a StorSimple eszközvezérlők kezelésével kapcsolatos gyakori kérdéseket.

**K.** Mi történik, ha az eszköz mindkét vezérlője kifogástalan állapotban van, és be van kapcsolva, és újraindítom vagy leállítom az aktív vezérlőt?

**A.** Ha az eszköz mindkét vezérlője kifogástalan állapotú és be van kapcsolva, a rendszer megerősítést kér. Dönthet úgy, hogy:

* **Indítsa újra az aktív vezérlőt** – Értesítést kap arról, hogy egy aktív vezérlő újraindítása miatt az eszköz átadott feladatokat a passzív vezérlőnek. A vezérlő újraindul.
* **Aktív vezérlő leállítása** – Értesítést kap arról, hogy egy aktív vezérlő leállítása állásidő vel jár. A kontroller bekapcsolásához meg kell nyomnia a készülék bekapcsológombját is.

**K.** Mi történik, ha az eszközöm passzív vezérlője nem érhető el vagy ki van kapcsolva, és újraindítom vagy leállítom az aktív vezérlőt?

**A.** Ha az eszköz passzív vezérlője nem érhető el vagy nem kapcsol ki, és a következőket választja:

* **Indítsa újra az aktív vezérlőt** – Értesítést kap arról, hogy a művelet folytatása a szolgáltatás ideiglenes megszakadását eredményezi, és megerősítést kér.
* **Aktív vezérlő leállítása** – Értesítést kap arról, hogy a művelet folytatása leállást eredményez. Az eszköz bekapcsolásához az egyik vagy mindkét vezérlőbe meg kell nyomnia a bekapcsológombot. A rendszer megerősítést kér.

**K.** Mikor indul újra vagy nem sikerül leállítani a vezérlőt?

**A.** A vezérlő újraindítása vagy leállítása sikertelen lehet, ha:

* Eszközfrissítés van folyamatban.
* A vezérlő újraindítása már folyamatban van.
* A vezérlő leállítása már folyamatban van.

**K.** Hogyan állapíthatja meg, hogy egy vezérlőt újraindítottak vagy leállítottak?

**A.** A vezérlő állapotát a Controller panelen ellenőrizheti. A vezérlő állapota jelzi, hogy a vezérlő újraindítása vagy leállítása folyamatban van-e. Emellett a **Riasztások** panel információs riasztást is tartalmaz, ha a vezérlőt újraindítja vagy leállítja. A vezérlő újraindítási és leállítási műveletei is rögzítésre kerülnek a tevékenységnaplókban. A tevékenységnaplókról a [Tevékenységnaplók megtekintése](storsimple-8000-service-dashboard.md#view-the-activity-logs)című területen talál további információt.

**K.** Van-e hatása az I/O eredményeként vezérlő feladatátvétel?

**A.** A kezdeményezők és az aktív vezérlő közötti TCP-kapcsolatok a vezérlő feladatátvétele miatt alaphelyzetbe állnak, de a passzív vezérlő működését feltételezve újra létrejön. A művelet során ideiglenes (kevesebb mint 30 másodperc) szünet elállhat az I/O-tevékenységben a kezdeményezők és az eszköz között.

**K.** Hogyan tudom visszaállítani a vezérlőt a szolgáltatásba, miután leállították és eltávolították?

**A.** A vezérlő szervizbe helyezéséhez be kell helyeznie azt a házba a [StorSimple eszköz vezérlőmoduljának cseréje](storsimple-8000-controller-replacement.md)című részben leírtak szerint.

## <a name="next-steps"></a>További lépések
* Ha olyan problémákat tapasztal a StorSimple eszközvezérlőkkel kapcsolatban, amelyeket az oktatóanyagban felsorolt eljárások használatával nem tud megoldani, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)
* Ha többet szeretne tudni a StorSimple Eszközkezelő szolgáltatás használatáról, olvassa el [a StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című webhelyet.](storsimple-8000-manager-service-administration.md)

