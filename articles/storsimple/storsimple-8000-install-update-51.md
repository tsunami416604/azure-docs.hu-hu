---
title: Az 5.1-es frissítés telepítése a StorSimple 8000 sorozatú eszközre | Microsoft dokumentumok
description: A cikk a StorSimple 8000 Series 5.1-es sorozatának 5.1-es sorozatának telepítését ismerteti a StorSimple 8000 sorozatú eszközön.
services: storsimple
documentationcenter: NA
author: priestlg
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: v-grpr
ms.openlocfilehash: b90f0f35f908d16b0746075e638fe66769939a64
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658039"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Az 5.1-es frissítés telepítése StorSimple eszközre

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan telepítheti az 5.1-es frissítést egy korábbi szoftververziót futtató StorSimple-eszközre az Azure Portalon keresztül. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Az 5.1-es frissítés nem zavaró biztonsági frissítéseket tartalmaz. A nem zavaró vagy rendszeres frissítések az Azure Portalon keresztül alkalmazhatók <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Az 5.1-es frissítés kötelező frissítés, amelyet azonnal telepíteni kell. További információt az [5.1-es frissítésről szóló kiadási megjegyzések című témakörben talál.](storsimple-update51-release-notes.md)
> * A telepítés előtt manuális és automatikus előzetes ellenőrzéseket végeznek az eszköz állapotának meghatározására a hardverállapot és a hálózati kapcsolat szempontjából. Ezek az előzetes ellenőrzések csak akkor hajtják végre, ha alkalmazza a frissítéseket az Azure Portalon.
> * Ha a gyorsjavítási módszerrel szeretné telepíteni, forduljon a [Microsoft támogatási szolgálatához.](mailto:support@microsoft.com)

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Az 5.1-es frissítés telepítése az Azure Portalon keresztül

Az alábbi lépésekkel frissítheti az eszközt az [5.1-es frissítésre.](storsimple-update51-release-notes.md)

> [!NOTE]
> A Microsoft további diagnosztikai információkat vesz fel az eszközről. Ennek eredményeképpen, amikor műveleti csapatunk azonosítja a problémákat okozó eszközöket, jobban fel vagyunk szerelve az eszközről származó adatok gyűjtésére és a problémák diagnosztizálására.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Frissítés telepítése az Azure Portalról

1. A StorSimple szolgáltatás oldalán válassza ki az eszközét.

    ![Eszköz kiválasztása](./media/storsimple-8000-install-update-51/update1.png)

2. Nyissa meg az **Eszközbeállítások** > **Eszközfrissítések lapot.**

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update-51/update2.png)

3. Értesítés jelenik meg, ha új frissítések érhetők el. Másik lehetőségként az **Eszközfrissítések** panelen kattintson a **Frissítések bekeresése gombra.** Létrejön egy feladat, amely megkeresi az elérhető frissítéseket. A feladat sikeres befejezéséről értesítést kap.

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update-51/update3.png)

4. Azt javasoljuk, hogy mielőtt alkalmazná a frissítést az eszközön, tekintse át a kibocsátási megjegyzéseket. A frissítések alkalmazásához kattintson **a Frissítések telepítése gombra.** A **Rendszeres frissítések megerősítése** panelen tekintse át a frissítések alkalmazása előtt végrehajtandó előfeltételeket. Jelölje be a jelölőnégyzetet, jelezve, hogy készen áll az eszköz frissítésére, majd kattintson a **Telepítés gombra.**

    ![Kattintson az Eszközfrissítések elemre](./media/storsimple-8000-install-update-51/update4.png)

5. Megkezdődik az előfeltételek ellenőrzése. Ezekbe az ellenőrzésekbe a következők tartoznak:
   
   * **A vezérlő állapotának ellenőrzései** annak ellenőrzéséhez, hogy mindkét eszközvezérlő kifogástalan állapotú és online legyen.
   * **A hardverösszetevők állapotának ellenőrzései** annak ellenőrzéséhez, hogy a StorSimple eszközön lévő összes hardverösszetevő kifogástalan állapotú legyen.
   * **DATA 0 ellenőrzések** annak ellenőrzéséhez, hogy a DATA 0 engedélyezett legyen az eszközön. Ha ez az illesztőfelület nem engedélyezett, engedélyeznie kell, majd újra kell próbálkoznia.

     A frissítés csak akkor töltődik le és települ, ha az összes ellenőrzés sikeresen befejeződött. Értesítést kap, amikor az ellenőrzések folyamatban vannak. Ha az előzetes ellenőrzések sikertelenek, akkor megkapja a hiba okait. Oldja meg ezeket a problémákat, majd próbálkozzon újra a művelettel. Elképzelhető, hogy kapcsolatba kell lépnie a Microsoft támogatási szolgálatával, ha nem tudja egyedül kezelni ezeket a problémákat.

7. Az előzetes ellenőrzések sikeres befejezése után létrejön egy frissítési feladat. A frissítési feladat sikeres létrehozásáról értesítést kap.
   
    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update-51/update6.png)
   
    A rendszer ezután alkalmazza a frissítést az eszközre.

9. A frissítés elvégzése néhány órát igényel. Válassza ki a frissítési feladatot, és kattintson a **Részletek** gombra, így bármikor megtekintheti a feladat részleteit.

    ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update-51/update8.png)

     A frissítési feladat előrehaladását az **Eszközbeállítások > feladatok**területen is figyelheti. A **Feladatok** panelen láthatja a frissítés folyamatát.

     ![Frissítési feladat létrehozása](./media/storsimple-8000-install-update-51/update7.png)

10. A feladat befejezése után keresse meg az **Eszközbeállítások > Eszközfrissítések .** A szoftver verzióját most frissíteni kell.


Ellenőrizze, hogy az eszközön fut-e a **StorSimple 8000 Series 5.1 frissítése (6.3.9600.17885)**. Az **utolsó frissítés dátumát** módosítani kell.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>További lépések

További információ az [5.1-es frissítés kiadásáról.](storsimple-update51-release-notes.md)
