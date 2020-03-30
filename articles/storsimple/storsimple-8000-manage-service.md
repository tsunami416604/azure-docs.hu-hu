---
title: A StorSimple Eszközkezelő szolgáltatás üzembe helyezése az Azure-ban | Microsoft dokumentumok
description: Bemutatja, hogyan hozhat létre és törölheti a StorSimple Eszközkezelő szolgáltatást az Azure Portalon, és ismerteti a szolgáltatás regisztrációs kulcsának kezelését.
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
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267780"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>A StorSimple Eszközkezelő szolgáltatás telepítése a StorSimple 8000 sorozatú eszközökhöz

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás fut a Microsoft Azure-ban, és több StorSimple-eszközhöz csatlakozik. A szolgáltatás létrehozása után használhatja a StorSimple Eszközkezelő szolgáltatáshoz egyetlen központi helyről csatlakoztatott összes eszköz kezelésére, ezáltal minimalizálva a felügyeleti terheket.

Ez az oktatóanyag ismerteti a szolgáltatás létrehozásához, törléséhez, áttelepítéséhez és a szolgáltatás regisztrációs kulcsának kezeléséhez szükséges lépéseket. Az ebben a cikkben található információk csak a StorSimple 8000 sorozatú eszközökre vonatkoznak. A StorSimple virtuális tömbökkel kapcsolatos további információkért [látogasson el egy StorSimple Eszközkezelő szolgáltatás telepítéséhez a StorSimple virtuális tömbhöz.](storsimple-virtual-array-manage-service.md)

> [!NOTE]
> -  Az Azure Portal támogatja az 5.0-s vagy újabb frissítést futtató eszközöket. Ha az eszköz nem naprakész, azonnal telepítse az 5. További információt az [5.](storsimple-8000-install-update-5.md) 
> - Ha egy StorSimple cloud appliance (8010/8020) használata esetén nem frissítheti a felhőalapú készüléket. A szoftver legújabb verziójával hozzon létre egy új felhőalapú készüléket az 5.0-s frissítéssel, majd a feladatátvételt az új onnan létrehozott felhőalapú berendezésre. 
> - A 4.0-s vagy korábbi frissítést futtató összes eszköz csökkentett felügyeleti funkciókat fog tapasztalni. 

## <a name="create-a-service"></a>Szolgáltatás létrehozása
StorSimple Eszközkezelő szolgáltatás létrehozásához a következőkre van szükség:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Aktív Microsoft Azure tárfiók
* A hozzáférés-kezeléshez használt számlázási adatok

Csak a nagyvállalati szerződéssel rendelkező előfizetések engedélyezettek. A szolgáltatás létrehozásakor is létrehozhat egy alapértelmezett tárfiókot.

Egyetlen szolgáltatás több eszközt is kezelhet. Egy eszköz azonban nem terjedhet ki több szolgáltatásra. Egy nagyvállalat több szolgáltatáspéldánysal is rendelkezhet különböző előfizetésekkel, szervezetekkel vagy akár üzembe helyezési helyekkel. 

> [!NOTE]
> A StorSimple Device Manager szolgáltatás külön példányaira van szükség a StorSimple 8000 sorozatú eszközök és a StorSimple virtuális tömbök kezeléséhez.

A szolgáltatás létrehozásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Minden StorSimple Eszközkezelő szolgáltatáshoz a következő attribútumok léteznek:

* **Név** – A StorSimple Eszközkezelő szolgáltatáshoz a létrehozáskor hozzárendelt név. **A szolgáltatás neve nem módosítható a szolgáltatás létrehozása után. Ez más entitásokra is igaz, például eszközökre, kötetekre, kötettárolókra és biztonsági mentési szabályzatokra, amelyek nem nevezhetők át az Azure Portalon.**
* **Állapot** – A szolgáltatás állapota, amely lehet **Aktív**, **Creating**vagy **Online**.
* **Hely** – Az a földrajzi hely, ahol a StorSimple eszköz lesz telepítve.
* **Előfizetés** – A szolgáltatáshoz társított számlázási előfizetés.

## <a name="delete-a-service"></a>Szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem használják a csatlakoztatott eszközök. Ha a szolgáltatás használatban van, inaktiválja a csatlakoztatott eszközöket. Az inaktiválási művelet megszakítja az eszköz és a szolgáltatás közötti kapcsolatot, de megőrzi az eszköz adatait a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlése után a művelet nem vonható vissza. Minden olyan eszközt, amely a szolgáltatást használta, vissza kell állítani a gyári alapértékekre, mielőtt egy másik szolgáltatással használható lenne. Ebben a forgatókönyvben az eszközön lévő helyi adatok, valamint a konfiguráció elvesznek.

A szolgáltatás törléséhez hajtsa végre az alábbi lépéseket.

### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Keresse meg a törölni kívánt szolgáltatást. Kattintson **az Erőforrások** ikonra, majd adja meg a kereséshez szükséges kifejezéseket. A keresési eredmények között kattintson a törölni kívánt szolgáltatásra.

    ![A törlödő keresési szolgáltatás](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Ezzel a StorSimple Device Manager szolgáltatás panel. Kattintson a **Törlés** gombra.

    ![Szolgáltatás törlése](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kattintson az **Igen** gombra a megerősítő értesítésben. A szolgáltatás törlése eltarthat néhány percig.

    ![Törlés megerősítése](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése

Miután sikeresen létrehozott egy szolgáltatást, regisztrálnia kell a StorSimple-eszközt a szolgáltatással. Az első StorSimple-eszköz regisztrálásához szüksége lesz a szolgáltatás regisztrációs kulcsára. További eszközök regisztrálásához egy meglévő StorSimple szolgáltatás, szükség van mind a regisztrációs kulcs és a szolgáltatás adattitkosítási kulcs (amely a regisztráció során az első eszközön jön létre). A szolgáltatás adattitkosítási kulcsáról további információt a [StorSimple biztonság című](storsimple-8000-security.md)témakörben talál. A regisztrációs kulcsot a StorSimple Eszközkezelő panel **kulcsainak** elérésével szerezheti be.

Hajtsa végre az alábbi lépéseket a szolgáltatás regisztrációs kulcsának levételéhez.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Tartsa a szolgáltatás regisztrációs kulcsát biztonságos helyen. Szüksége lesz erre a kulcsra, valamint a szolgáltatás adattitkosítási kulcsára, hogy további eszközöket regisztrálhasson ezzel a szolgáltatással. A szolgáltatás regisztrációs kulcsának beszerzése után konfigurálnia kell az eszközt a Windows PowerShell storSimple felületen keresztül.

A regisztrációs kulcs használatáról a [3.](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Újra kell generálnia egy szolgáltatásregisztrációs kulcsot, ha kulcselforgatást kell végrehajtania, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. A kulcs újragenerálásakor az új kulcs csak a későbbi eszközök regisztrálására szolgál. A már regisztrált eszközöket ez a folyamat nem érinti.

A szolgáltatásregisztrációs kulcs újragenerálásához hajtsa végre az alábbi lépéseket.

### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
1. A **StorSimple Eszközkezelő** panelen nyissa meg a **Felügyeleti &gt; ** **kulcsok lapot.**
    
    ![Kulcsok panel](./media/storsimple-8000-manage-service/regenregkey2.png)

2. A **Billentyűk** panelen kattintson a **Regenerate gombra.**

    ![Kattintson az újragenerálás gombra](./media/storsimple-8000-manage-service/regenregkey3.png)
3. A **Szolgáltatás regisztrációs kulcsának újragenerálása** panelen tekintse át a kulcsok újragenerálásakor szükséges műveletet. A szolgáltatással regisztrált összes további eszköz az új regisztrációs kulcsot használja. A megerősítéshez kattintson a **Regenerate gombra.** A regenerálás befejezése után értesítést kap.

    ![Regenerálás megerősítése](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Egy új szolgáltatásregisztrációs kulcs jelenik meg.

5. Másolja a kulcsot, és mentse el az új eszközök regisztrálásához ezzel a szolgáltatással.



## <a name="change-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs módosítása
A szolgáltatásadatok titkosítási kulcsai a StorSimple Manager-szolgáltatásból a StorSimple-eszközre küldött bizalmas ügyféladatok, például a tárfiók hitelesítő adatainak titkosítására szolgálnak. Ezeket a kulcsokat rendszeresen módosítania kell, ha az informatikai szervezet rendelkezik kulcsrotációs házirenddel a tárolóeszközökön. A kulcsmódosítási folyamat némileg eltérő lehet attól függően, hogy van-e egyetlen eszköz vagy több, a StorSimple Manager szolgáltatás által felügyelt eszköz. További információ: [StorSimple biztonság és adatvédelem](storsimple-8000-security.md).

A szolgáltatásadat-titkosítási kulcs módosítása háromlépésből áll:

1. Az Azure Resource Manager Windows PowerShell-parancsfájljainak használatával engedélyezze az eszközt a szolgáltatásadat-titkosítási kulcs módosítására.
2. A StorSimple hez való Windows PowerShell használatával kezdeményezze a szolgáltatás adattitkosítási kulcsának módosítását.
3. Ha egynél több StorSimple-eszközzel rendelkezik, frissítse a szolgáltatás adattitkosítási kulcsát a többi eszközön.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. lépés: A Windows PowerShell parancsfájl használatával engedélyezi az eszközt a szolgáltatásadat-titkosítási kulcs módosításához
Az eszköz rendszergazdája általában kérni fogja, hogy a szolgáltatás rendszergazdája engedélyezzen egy eszközt a szolgáltatásadat-titkosítási kulcsok módosítására. A szolgáltatás rendszergazdája ezután engedélyezi az eszköz számára a kulcs módosítását.

Ez a lépés az Azure Resource Manager alapú parancsfájl használatával történik. A szolgáltatás rendszergazdája kiválaszthatja az engedélyezett eszköz kiválasztását. Az eszköz ezután jogosult a szolgáltatás adattitkosítási kulcs módosítási folyamatának elindítására. 

A parancsfájl használatával kapcsolatos további tudnivalókért látogasson el az [Authorize-ServiceEncryptionRollover.ps1 című e-engedélyre.](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Mely eszközök engedélyezhetők a szolgáltatásadat-titkosítási kulcsok módosítására?
Az eszköznek meg kell felelnie az alábbi feltételeknek ahhoz, hogy engedélyezhető legyen a szolgáltatásadat-titkosítási kulcs módosításának kezdeményezésére:

* Az eszköznek online állapotban kell lennie ahhoz, hogy jogosult legyen a szolgáltatásadat-titkosítási kulcsmódosítási engedélyezésére.
* Ha a kulcsmódosítást nem kezdeményezte, 30 perc elteltével újra engedélyezheti ugyanazt az eszközt.
* Másik eszközt is engedélyezhet, feltéve, hogy a kulcsmódosítást nem a korábban engedélyezett eszköz kezdeményezte. Az új eszköz engedélyezése után a régi eszköz nem tudja kezdeményezni a módosítást.
* Nem engedélyezhet egy eszközt, amíg a szolgáltatásadat-titkosítási kulcs váltása folyamatban van.
* Engedélyezheti az eszközt, ha a szolgáltatáshoz regisztrált eszközök egy része átgördült a titkosításon, míg mások nem. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. lépés: A StorSimple hez való Windows PowerShell használata a szolgáltatásadat-titkosítási kulcs módosításának kezdeményezéséhez
Ez a lépés a Windows PowerShell for StorSimple felületen történik az engedélyezett StorSimple eszközön.

> [!NOTE]
> A StorSimple Manager-szolgáltatás Azure portalján nem hajthatók végre műveletek, amíg a kulcsváltás be nem fejeződik.


Ha az eszköz soros konzolját használja a Windows PowerShell-felülethez való csatlakozáshoz, hajtsa végre a következő lépéseket.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>A szolgáltatásadat-titkosítási kulcs módosításának kezdeményezése
1. Válassza az 1-es lehetőséget a teljes hozzáféréssel való bejelentkezéshez.
2. A parancssorba írja be a következőt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Miután a parancsmag sikeresen befejeződött, egy új szolgáltatásadat-titkosítási kulcsot kap. A kulcs másolása és mentése a folyamat 3. Ez a kulcs a StorSimple Manager szolgáltatásban regisztrált összes többi eszköz frissítésére szolgál.
   
   > [!NOTE]
   > Ezt a folyamatot a StorSimple-eszköz engedélyezésétől számított négy órán belül el kell indítani.
   > 
   > 
   
   Ezt az új kulcsot ezután elküldi a szolgáltatásnak, hogy a szolgáltatásban regisztrált összes eszközre leküldhető legyen. Ezután egy riasztás jelenik meg a szolgáltatás irányítópultján. A szolgáltatás letiltja a regisztrált eszközökön futó összes műveletet, és az eszköz rendszergazdájának frissítenie kell a szolgáltatás adattitkosítási kulcsát a többi eszközön. Azonban az I/O (a felhőbe küldő állomások) nem lesz megzavarva.
   
   Ha egyetlen eszköz van regisztrálva a szolgáltatáshoz, a váltási folyamat most befejeződött, és kihagyhatja a következő lépést. Ha több eszköz van regisztrálva a szolgáltatáshoz, folytassa a 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. lépés: Frissítse a szolgáltatás adattitkosítási kulcsát más StorSimple eszközökön
Ezeket a lépéseket a StorSimple-eszköz Windows PowerShell-felületén kell végrehajtani, ha több eszköz van regisztrálva a StorSimple Manager szolgáltatáshoz. A 2.

Hajtsa végre az alábbi lépéseket a szolgáltatásadatok titkosításának frissítéséhez az eszközön.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>A szolgáltatásadat-titkosítási kulcs frissítése fizikai eszközökön
1. A Windows PowerShell for StorSimple segítségével csatlakozzon a konzolhoz. Válassza az 1-es lehetőséget a teljes hozzáféréssel való bejelentkezéshez.
2. A parancssorba írja be a következőt:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Adja meg a szolgáltatás adattitkosítási kulcsát, amelyet a [2.](#to-initiate-the-service-data-encryption-key-change)

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>A szolgáltatásadat-titkosítási kulcs frissítése az összes 8010/8020 felhőalapú készüléken
1. Töltse le és állítsa be [az Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-parancsfájlt. 
2. Nyissa meg a PowerShellt, és a parancssorba írja be a következőt:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ez a parancsfájl biztosítja, hogy a szolgáltatás adattitkosítási kulcs a 8010/8020 felhőalapú készülékek az eszközkezelő alatt van beállítva.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Támogatott műveletek az 5.0-s frissítés előtti verziót futtató eszközökön
Az Azure Portalon csak az 5.0-s vagy újabb frissítést futtató StorSimple-eszközök támogatottak. A régebbi verziókat futtató eszközök támogatása korlátozott. Miután áttelepült az Azure Portalra, az alábbi táblázat segítségével ismerje meg, hogy mely műveletek támogatottak az 5.0-s frissítés előtti verziókat futtató eszközökön.

| Művelet                                                                                                                       | Támogatott      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Eszköz regisztrálása                                                                                                               | Igen            |
| Eszközbeállítások , például általános, hálózati és biztonsági beállítások konfigurálása                                                                | Igen            |
| Frissítések bekéselése, letöltése és telepítése                                                                                             | Igen            |
| Eszköz inaktiválása                                                                                                               | Igen            |
| Eszköz törlése                                                                                                                   | Igen            |
| Kötettároló létrehozása, módosítása és törlése                                                                                   | Nem             |
| Kötet létrehozása, módosítása és törlése                                                                                             | Nem             |
| Biztonságimentési házirend létrehozása, módosítása és törlése                                                                                      | Nem             |
| Kézi biztonsági mentés készítése                                                                                                            | Nem             |
| Ütemezett biztonsági mentés készítése                                                                                                         | Nem alkalmazható |
| Visszaállítás hátfelborulásból                                                                                                        | Nem             |
| Klónozás 3.0-s vagy újabb frissítést futtató eszközre <br> A forráseszköz a 3.0-s frissítés előtt fut.                                | Igen            |
| Klónozás a 3.0-s frissítés előtti verziókat futtató eszközre                                                                          | Nem             |
| Feladatátvétel forráseszközként <br> (a 3.0-s frissítés előtti verziót futtató eszközről a 3.0-s vagy újabb verziót futtató eszközre)                                                               | Igen            |
| Feladatátvétel céleszközként <br> (a 3.0-s frissítés előtt szoftververziót futtató eszközre)                                                                                   | Nem             |
| Riasztás törlése                                                                                                                  | Igen            |
| Tekintse meg a biztonsági mentési házirendeket, a biztonsági mentési katalógust, a köteteket, a kötettárolókat, a figyelési diagramokat, a feladatokat és a klasszikus portálon létrehozott riasztásokat | Igen            |
| Eszközvezérlők be- és kikapcsolása                                                                                              | Igen            |


## <a name="next-steps"></a>További lépések
* További információ a [StorSimple telepítési folyamatáról.](storsimple-8000-deployment-walkthrough-u2.md)
* További információ [a StorSimple tárfiók kezeléséről.](storsimple-8000-manage-storage-accounts.md)
* További információ [aStorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)
