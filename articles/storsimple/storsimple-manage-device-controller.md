---
title: "A StorSimple eszköz tartományvezérlők kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan leállítása, újraindítása, állítsa le vagy alaphelyzetbe állítja a StorSimple eszköz tartományvezérlőket."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 4ee989d0-956f-4c14-951e-fd4e490ea09d
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: alkohli
ms.openlocfilehash: 67dbb0c4066002256efbab6061157c641527e441
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="manage-your-storsimple-device-controllers"></a>A StorSimple eszköz tartományvezérlők kezelése
## <a name="overview"></a>Áttekintés
Ez az oktatóanyag leírja a különböző műveletek végrehajtható a StorSimple eszköz tartományvezérlőn. A StorSimple eszköz tartományvezérlőinek redundáns (társközi) vezérlők aktív-passzív konfigurációban. Egy adott időben csak egy vezérlő aktív, és a lemezek és a hálózati műveletek feldolgozása. A többi tartományvezérlő a passzív módban van. Az aktív vezérlő nem sikerül, ha a passzív vezérlő automatikusan aktívvá válik.

Ez az oktatóanyag segítségével történő kezeléséhez a eszközvezérlők részletes utasításokat tartalmaz a:

* **Tartományvezérlők** szakasza a **karbantartási** a StorSimple Manager szolgáltatás lapján
* A Windows PowerShell-lel.

Azt javasoljuk, hogy a eszközvezérlők a StorSimple Manager szolgáltatás használatával kezelheti. Egy művelet csak a StorSimple Windows PowerShell használatával hajtható végre, ha az oktatóanyag teszi jegyezze fel azt.

Ez az oktatóanyag elolvasása, után fogja tudni:

* A StorSimple eszköz vezérlő leállítása vagy újraindítása
* Állítsa le a StorSimple eszköz
* A StorSimple eszközt a gyári beállítások visszaállítása

## <a name="restart-or-shut-down-a-single-controller"></a>Csak egy vezérlő leállítása vagy újraindítása
A tartományvezérlő újraindítás vagy leállítás nincs szükség a szokásos működésének részeként. Egyetlen eszköz tartományvezérlő leállítás műveletek esetében közös csak abban az esetben egy sikertelen eszköz hardverösszetevő használatához szükséges cserélni. Tartományvezérlő újraindításra is szükség lehet, amelyben teljesítmény túl sok memória használata, vagy egy nem megfelelően működő tartományvezérlő által érintett helyzetben. Is szükség lehet a vezérlő újraindítása után sikeres vezérlő helyettesíti, ha szeretné engedélyezni, és ellenőrizze a kicserélt vezérlőt.

Egy eszköz újraindítása nincs csatlakoztatott kezdeményezőkhöz, feltéve, hogy a passzív vezérlő érhető el őket. Ha a passzív tartományvezérlő nem érhető el, és be van kapcsolva ki, majd az aktív vezérlővel újraindítása eredményezhet a megszakítás a szolgáltatás és az állásidő.

> [!IMPORTANT]
> * **Egy futó tartományvezérlő kell soha nem kell fizikailag eltávolítani, mert ez adatvesztést redundanciát és az állásidő megnövekedett kockázata eredményezne.**
> * Az alábbi eljárás csak a StorSimple fizikai eszköz vonatkozik. Indítás, Leállítás, és indítsa újra a virtuális eszköz kapcsolatos információkért lásd: [a virtuális eszközhöz](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).
> 
> 

A StorSimple a klasszikus Azure-portálon a StorSimple Manager szolgáltatás vagy a Windows PowerShell használatával egyetlen eszközt vezérlő leállítása vagy újraindítása

A eszközvezérlők kezelése a klasszikus Azure portálra, hajtsa végre az alábbi lépéseket.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>A klasszikus portál vezérlő leállítása vagy újraindítása
1. Navigáljon a **eszközök > karbantartási**.
2. Ugrás a **hardverállapot** , és ellenőrizze, hogy az eszközön mindkét vezérlőhöz állapota **kifogástalan**.
   
    ![Ellenőrizze, megfelelő StorSimple eszközvezérlők](./media/storsimple-manage-device-controller/IC766017.png)
3. Alsó részén a **karbantartási** kattintson **tartományvezérlők kezelése**.
   
    ![A StorSimple eszköz tartományvezérlők kezelése](./media/storsimple-manage-device-controller/IC766018.png)</br>
   
   > [!NOTE]
   > Ha nem látható **tartományvezérlők kezelése**, akkor kell telepíteni a frissítéseket. További információkért lásd: [a StorSimple eszköz frissítése](storsimple-update-device.md).
   > 
   > 
4. Az a **vezérlő beállítások módosítása** párbeszédpanelen tegye a következőket:
   
   1. Az a **válassza ki a tartományvezérlő** legördülő listára, válassza ki a kezelni kívánt tartományvezérlő. A beállítások esetén a vezérlő 0 és a vezérlő 1. Ezek a tartományvezérlők is aktív vagy passzív azonosítja.
      
      > [!NOTE]
      > A vezérlő nem tudja felügyelni, ha ki van kapcsolva vagy nem érhető el, és már nem jelenik a legördülő listában.
      > 
      > 
   2. Az a **művelet kiválasztása** legördülő menüben válassza ki **indítsa újra a tartományvezérlő** vagy **állítsa le a tartományvezérlő**.
      
       ![Indítsa újra a StorSimple eszköz passzív vezérlő](./media/storsimple-manage-device-controller/IC766020.png)
   3. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-device-controller/IC740895.png).

Ez indítsa újra, vagy állítsa le a tartományvezérlő. Az alábbi táblázat összefoglalja a részleteit, hogy mi történik, attól függően, hogy a megadott beállítások megmaradnak a **vezérlő beállítások módosítása** párbeszédpanel megnyitásához.  

| Kijelölés # | Ha úgy dönt, hogy... | Ez akkor történik. |
| --- | --- | --- |
| 1. |Indítsa újra a passzív vezérlő. |Egy feladatot hoz létre, hogy indítsa újra a tartományvezérlő, és értesítést fog kapni a feladat sikeres létrehozása után. Ez lesz újraindításhoz a tartományvezérlő. Figyelheti az újraindítási folyamatot elérésével **szolgáltatás > irányítópult > műveletnaplók megtekintése** és majd a szolgáltatásra vonatkozó paraméterek szerinti szűrését. |
| 2. |Indítsa újra az aktív vezérlőhöz. |A következő figyelmeztetés jelenik meg: "újraindítja az aktív vezérlővel, ha az eszköz hajt végre feladatátvételt a passzív vezérlőhöz. Így is szeretné folytatni?" </br>Ha a műveletet, a következő lépéseket azoknak a passzív controller újraindításához használt egyeznie kell (lásd a kijelölés 1). |
| 3. |Állítsa le a passzív vezérlő. |A következő üzenet jelenik meg: "leállítási befejezése után, szüksége lesz, majd a bekapcsológombot a tartományvezérlőn kapcsolhatja be. Biztos, hogy állítsa le a vezérlő kívánt?" </br>Ha a műveletet, a következő lépéseket azoknak a passzív controller újraindításához használt egyeznie kell (lásd a kijelölés 1). |
| 4. |Állítsa le az aktív vezérlőhöz. |A következő üzenet jelenik meg: "leállítási befejezése után, szüksége lesz, majd a bekapcsológombot a tartományvezérlőn kapcsolhatja be. Biztos, hogy állítsa le a vezérlő kívánt?" </br>Ha a műveletet, a következő lépéseket azoknak a passzív controller újraindításához használt egyeznie kell (lásd a kijelölés 1). |

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>A StorSimple a Windows PowerShell egy tartományvezérlőre leállítása vagy újraindítása
A következő lépésekkel leállítani, vagy indítsa újra a klasszikus Azure-portálon a StorSimple eszköz csak egy vezérlő.

1. A soros konzol vagy egy telnet-munkamenet távoli számítógépről az eszközhöz való hozzáféréshez. Csatlakozás a lépések vezérlő 0 vagy a vezérlő 1 [a PuTTY használata az eszköz soros konzoljához való csatlakozáshoz](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
3. A szalagcím üzenetben jegyezze fel a vezérlő csatlakozik (a vezérlő 0 vagy a vezérlő 1), és hogy-e az aktív vagy passzív (készenléti) tartományvezérlő.
   
   * Állítsa le egyetlen tartományvezérlő, a parancssorba írja be:
     
       `Stop-HcsController`
     
       Ez a vezérlőre, amihez kapcsolódik le fog állni. Ha megszakítja az aktív vezérlővel, majd azt hajt végre feladatátvételt a passzív vezérlőhöz után állítja le.
   * Indítsa újra a tartományvezérlő, a parancssorba írja be:
     
       `Restart-HcsController`
     
       Ez újra fog indulni a vezérlőre, amihez kapcsolódik. Ha újraindítja az aktív vezérlővel, akkor hajt végre feladatátvételt a passzív vezérlőhöz az újraindítás előtt.

## <a name="shut-down-a-storsimple-device"></a>Állítsa le a StorSimple eszköz
Ez a szakasz ismerteti, hogyan lehet leállítani egy futó vagy egy távoli számítógépről nem sikerült a StorSimple eszköz. Egy eszköz ki van kapcsolva, mind a eszközvezérlők leállítása után. Egy eszköz leállítás történik, amikor az eszköz fizikai mozgatására, vagy nem működik használatban van.

> [!IMPORTANT]
> Az eszköz leállítása előtt ellenőrizze az eszköz összetevők állapotát. Navigáljon a **eszközök > karbantartási > hardverállapot** , és ellenőrizze, hogy a LED összes összetevő állapota zöld. Csak a megfelelő eszköz egy zöld állapot lesz. Ha az eszköz épp most állítja le a hibás összetevő lecseréléséhez látni fogja a sikertelen (piros) vagy a megfelelő összetevője egy csökkent (sárga) állapotát.
> 
> 

#### <a name="to-shut-down-a-storsimple-device"></a>A StorSimple eszköz leállítása
1. Használja a [vezérlő leállítása vagy újraindítása](#restart-or-shut-down-a-single-controller) eljárás segítségével azonosíthatja, és állítsa le a passzív vezérlő az eszközön. A klasszikus Azure portálon vagy a Windows PowerShellben a StorSimple is végezheti ezt a műveletet.
2. Ismételje meg a fenti állítsa le az aktív vezérlőhöz.
3. Akkor nézze meg az eszköz a háttérben vezérlősík most. A két vezérlő teljesen állnak le, miután kell mindkét vezérlőhöz állapotát LED villogó piros. Ha az eszköz teljesen kikapcsolása jelenleg van szüksége, tükrözés Power és hűtési modulok (PCMs) is a power kapcsolók a OFF állásba. Ez kapcsolja ki az eszközt.

<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra
> [!IMPORTANT]
> Ha az eszköz visszaállítása a gyári alapértelmezett beállításokra van szüksége, forduljon a Microsoft Support. Az alább ismertetett eljárás csak a Microsoft Support együtt használandó.
> 
> 

Ez az eljárás ismerteti a Microsoft Azure StorSimple eszköz visszaállítása a gyári alapértelmezett beállításokra StorSimple a Windows PowerShell használatával.
Gyári beállítások visszaállításával a eltávolítja minden adatot és beállítást alapértelmezés szerint a teljes fürtöt.

Végezze el a Microsoft Azure StorSimple eszköz visszaállítása a gyári alapértelmezett beállítások a következő lépéseket:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Alaphelyzetbe állítja az eszköz StorSimple a Windows PowerShell-alapértelmezett beállításai
1. A soros konzolon keresztül férnek hozzá az eszközt. Ellenőrizze a szalagcím üzenet győződjön meg arról, hogy csatlakozik az aktív vezérlőhöz.
2. A soros konzol menüben válassza az 1. lehetőség – **jelentkezzen be a teljes körű hozzáférési**.
3. A parancssorba írja be az egész fürt számára, az összes adatokat, a metaadatok és a tartományvezérlő-beállítások eltávolítása alaphelyzetbe állítani a következő parancsot:
   
    `Reset-HcsFactoryDefault`
   
    Helyette alaphelyzetbe az egyetlen tartományvezérlő, használja a [alaphelyzetbe állítása-HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) parancsmagot a `-scope` paraméter.)
   
    A rendszer többször újraindul. Ön értesítést fog kapni a visszaállítás sikeresen befejeződött. Attól függően, hogy a rendszer modell 8100-as eszköz és a 60-90 percig, amíg ez a folyamat befejezéséhez egy 8600 45-60 percig is tarthat.
   
   > [!TIP]
   > * Ha használ frissítés 1.2-es vagy korábbi használja a `–SkipFirmwareVersionCheck` paraméter kihagyását a belső vezérlőprogram verziójának ellenőrzése (ellenkező esetben látni fogja, a belső vezérlőprogram verzióeltérési hiba: a gyári beállítások visszaállítása nem folytatódhat, mert a belső vezérlőprogram-verziók nem egyeznek. ).
   > * A gyári alaphelyzetbe állítása eljárás 1. frissítés vagy 1.1 a kormányzati portálon futtatja, és végeztek el (az 1. frissítés előtti szoftvert teljesített helyettesítő vezérlők) sikeres egyszeres vagy kettős vezérlő helyettesíti a StorSimple eszközök sikertelenek lehetnek. Ez történik, ha a gyári kép előtti 1 szoftverfrissítést, amely nem létezik a tartományvezérlőn SHA1 fájl meglétének van hitelesítve. Ha azt látja, ez gyári hiba, forduljon a Microsoft Support segítheti a következő lépéseket. A probléma nem látják az 1. frissítés vagy újabb szoftvert gyárból származó teljesített helyettesítő vezérlők.
   > 
   > 

## <a name="questions-and-answers-about-managing-device-controllers"></a>Kérdések és válaszok eszközvezérlők kezelése
Ez a szakasz azt rendelkezik összegzett gyakran feltett kérdésekre vonatkozó kezelése StorSimple eszközvezérlők.

**K.** Mi történik, ha az eszközön mindkét vezérlőhöz kifogástalan és kikapcsolt be és indítsa újra, vagy állítsa le az aktív vezérlővel?

**V.** Ha az eszközön mindkét vezérlőhöz kifogástalan, és a kikapcsolt, kérni fogja megerősítést kér. Ki is:

* **Indítsa újra az aktív vezérlő** – értesítést fog kapni, hogy az aktív vezérlőhöz újraindítása miatt az eszköz a passzív vezérlőhöz feladatátvételt. A vezérlő újraindul.
* **Állítsa le az aktív vezérlőhöz** – értesítést fog kapni, hogy az aktív vezérlőhöz leállítása leállást eredményez. Szüksége lesz is, a főkapcsoló leküldéses kapcsolja be a vezérlő az eszközön.

**K.** Mi történik, ha a passzív vezérlő meg az eszköz nem érhető el, és be van kapcsolva a kikapcsolva, és indítsa újra, vagy állítsa le az aktív vezérlővel?

**V.** Ha az eszközön a passzív vezérlő ki van kapcsolva vagy nem érhető el, és úgy dönt, hogy:

* **Indítsa újra az aktív vezérlő** – értesítést fog kapni, hogy a művelet folytatása eredményezi egy ideiglenes működőképes legyen a, és megerősítés kéri.
* **Állítsa le az aktív vezérlőhöz** – értesítést fog kapni, hogy a művelet folytatása jár állásidővel, és hogy szüksége lesz a főkapcsoló leküldéses kapcsolja be az eszközön legalább az egyik tartományvezérlőn. Megerősítést kér bekéri.

**K.** Ha nem a a tartományvezérlő újraindítása és leállítása sikertelen előrehaladás?

**V.** A vezérlő leállítása és újraindítása sikertelen lehet, ha:

* Egy eszköz frissítése folyamatban van.
* A tartományvezérlő újraindítása már folyamatban van.
* Egy tartományvezérlő leállítás már folyamatban van.

**K.** Hogyan meg kitalálja, hogy ha egy tartományvezérlő újraindult, vagy állítsa le?

**V.** A tartományvezérlő állapotát, a karbantartási oldalon ellenőrizheti. A tartományvezérlő állapotát jelzi, hogy a tartományvezérlő újraindítása vagy leállítása. Emellett a riasztások lapon fogja tartalmazni az információs riasztások, ha a tartományvezérlő újraindítása vagy leállítása. A vezérlő újraindítás és leállítás műveletek is tárolja, amely a műveleti naplói. A műveletnaplók kapcsolatos további információkért látogasson el [megtekintése a műveletnaplók](storsimple-service-dashboard.md#view-the-operations-logs).

**K.** A vezérlő feladatátvétel miatt az i/o gyakorolt hatás van?

**V.** A TCP-kapcsolatok kezdeményezők és az aktív vezérlő között vezérlő feladatátvétel miatt vissza lesz állítva, de hozni, ha a passzív vezérlő azt feltételezi, hogy a művelet. Valószínűleg i/o-tevékenységben kezdeményezők és az eszköz között (kevesebb, mint 30 másodperc) ideiglenes szünetet Ez a művelet során.

**K.** Hogyan térjen vissza a vezérlő számára a szolgáltatás leállítása és eltávolítása után?

**V.** A tartományvezérlő szolgáltatás visszaállításához kell behelyezi a váz leírtak [cserélje le a StorSimple eszköz vezérlő modul](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Következő lépések
* Ha problémába ütközik a StorSimple eszköz vezérlőkkel nem oldható fel az ebben az oktatóanyagban szereplő eljárások [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).
* A StorSimple Manager szolgáltatással kapcsolatos további tudnivalókért keresse fel [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

