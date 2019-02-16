---
title: Az Azure-ban a StorSimple-Eszközkezelő szolgáltatás üzembe helyezése |} A Microsoft Docs
description: Azt ismerteti, hogyan hozhat létre, és az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás törlése, és a szolgáltatás regisztrációs kulcsának kezelését ismerteti.
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
ms.openlocfilehash: eb1fe69a7fb99949ac95291c33e76c1a32bf5439
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310057"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>A StorSimple 8000 sorozatú eszközök StorSimple-Eszközkezelő szolgáltatás üzembe helyezése

## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatás a Microsoft Azure-ban fut, és több StorSimple-eszközhöz csatlakozik. Miután létrehozta a szolgáltatást, használhatja az egységes, központi helyről, a StorSimple-Eszközkezelő szolgáltatás csatlakozó eszközök kezelésére, ezáltal minimálisra csökkentik az adminisztratív terhek.

Ebben az oktatóanyagban a létrehozás, törlés, a szolgáltatás és a szolgáltatás regisztrációs kulcsának kezelése a szükséges lépéseket ismerteti. Ebben a cikkben található információk csak a StorSimple 8000 sorozat eszközeire vonatkozó. További információ a StorSimple Virtual Arrayt Ugrás [a StorSimple Virtual Array egy StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Az Azure Portalon 5.0-s vagy újabb frissítést futtató eszközöket támogatja. Ha az eszköz nem naprakész, 5. frissítés azonnal kell telepíteni. További információért ugorjon [telepítése Update 5](storsimple-8000-install-update-5.md). 
> - Ha a StorSimple Cloud Appliance (8010/8020) használ, a felhőalapú berendezés nem frissíthető. A szoftverek legújabb verziójának használatával hozzon létre egy új felhőalapú berendezést az 5.0-s frissítést, és ezután átadja a feladatokat a létrehozott új felhőalapú berendezést. 
> - Minden eszköz 4.0-s vagy régebbi frissítést futtató korlátozott felügyeleti funkciók fog tapasztalni. 

## <a name="create-a-service"></a>Szolgáltatás létrehozása
A StorSimple-Eszközkezelő szolgáltatás létrehozásához szüksége lesz:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Egy aktív Microsoft Azure storage-fiók
* A számlázási adatokat, amely hozzáférés-kezelés szolgál

Csak nagyvállalati szerződéssel rendelkező előfizetések engedélyezettek. Hozzon létre egy alapértelmezett tárfiókot, a szolgáltatások létrehozásakor, azt is beállíthatja.

Egy egyetlen szolgáltatással kezelheti a több eszközre. Azonban egy eszköz nem ívelhet át több szolgáltatást. Nagy vállalatok lehet együttműködni különböző előfizetésekben találhatóak, a szervezeteknek, illetve a telepítési helyek még több szolgáltatáspéldány. 

> [!NOTE]
> Kezelheti a StorSimple 8000 sorozatú eszközök és virtuális StorSimple-tömbök StorSimple-Eszközkezelő szolgáltatás külön példányát kell.

A következő lépésekkel létrehoz egy szolgáltatást.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Minden egyes StorSimple-Eszközkezelő szolgáltatás létezik a következő attribútumokat:

* **Név** – a nevét, amely hozzá lett rendelve a StorSimple-Eszközkezelő szolgáltatás létrehozásakor. **A szolgáltatás neve nem lehet módosítani, a szolgáltatás létrehozása után. Ez a is egyéb entitások, például az eszközök, kötetek, kötettárolók és biztonsági mentési házirendeket, amelyek nem nevezhető át az Azure Portalon.**
* **Állapot** – a szolgáltatás, amely lehet állapotának **aktív**, **létrehozása**, vagy **Online**.
* **Hely** – az a földrajzi hely, amelyben a StorSimple eszköz üzembe helyezve.
* **Előfizetés** – a számlázási előfizetés, amely a szolgáltatás társítva van.

## <a name="delete-a-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez előtt ellenőrizze, hogy nincsenek csatlakoztatott eszközök használják. Ha a szolgáltatás használja, kapcsolja ki a csatlakoztatott eszközök. Az inaktiválás műveletet az eszköz és a szolgáltatás közötti kapcsolat-kiszolgálóhoz, de megőrizni az eszközön lévő adatokat a felhőben.

> [!IMPORTANT]
> A szolgáltatás törlését követően a művelet nem vonható vissza. A szolgáltatás használatával lett minden olyan eszköz gyári alaphelyzetbe kell állítani ahhoz, hogy egy másik szolgáltatást kell használni kell. Ebben a forgatókönyvben a helyi adatokat az eszköz, valamint a konfigurációt, az elvész.

A következő lépésekkel törölheti a szolgáltatást.

### <a name="to-delete-a-service"></a>Szolgáltatás törlése

1. Keresse meg a törölni kívánt szolgáltatást. Kattintson a **erőforrások** ikon és a megfelelő használati kereséséhez, majd bemeneti. A keresési eredmények között kattintson a törölni kívánt szolgáltatást.

    ![Keresési szolgáltatás törlése](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Ekkor megjelenik a StorSimple-Eszközkezelő szolgáltatás paneljén. Kattintson a **Törlés** gombra.

    ![Szolgáltatás törlése](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kattintson a **Igen** a a megerősítési értesítés. Eltarthat néhány percig, a szolgáltatás törlését.

    ![Törlés megerősítése](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése

Miután sikeresen létrehozott egy szolgáltatás, szüksége lesz a StorSimple-eszköz regisztrálása a szolgáltatással. Az első StorSimple eszköz regisztrálásához, szüksége lesz a szolgáltatás regisztrációs kulcsát. További eszközöket regisztrálni egy meglévő StorSimple szolgáltatásban, szüksége van a regisztráció és a szolgáltatásadat-titkosítási kulcs (amely jön létre az első eszköz regisztrációja során). A szolgáltatásadat-titkosítási kulcs kapcsolatos további információkért lásd: [StorSimple biztonsági](storsimple-8000-security.md). Megtekintheti a regisztrációs kulcs elérésével **kulcsok** a StorSimple-Eszközkezelő panel.

A következő lépésekkel kérnie a Szolgáltatásregisztrációs kulcsot.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

A szolgáltatás regisztrációs kulcsával tartsa biztonságos helyen. Ezt a kulcsot, valamint a szolgáltatásadat-titkosítási kulcs, ez a szolgáltatás további eszközöket regisztrálni kell. Miután beszerezte a szolgáltatás regisztrációs kulcsával, konfigurálnia kell az eszközt a Windows Powershellen keresztül a StorSimple adapter.

További információ a Szolgáltatásregisztrációs kulcs használatával: [3. lépés: Eszköz konfigurálása és regisztrálása a Windows PowerShell storsimple-höz készült](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
A szolgáltatás regisztrációs kulcsának újragenerálása, ha kulcsrotálás végrehajtásához szükséges, vagy ha a szolgáltatás-rendszergazdák listája megváltozott kell. Amikor újragenerálja a kulcsot, akkor az új kulcs csak az azt követő eszközök regisztrálása szolgál. A már regisztrált eszközöket nem érinti ez a folyamat.

Hajtsa végre az alábbi lépések végrehajtásával hozza létre újra a Szolgáltatásregisztrációs kulcs.

### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
1. Az a **StorSimple-Eszközkezelő** panelen lépjen a **felügyeleti &gt;**  **kulcsok**.
    
    ![Kulcsok panel](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Az a **kulcsok** panelen kattintson a **újragenerálása**.

    ![Kattintson a fiókkulcs újbóli létrehozása](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Az a **Szolgáltatásregisztrációs kulcs újragenerálása** panelen, és tekintse át a művelet kötelező, ha a kulcsok újbóli. Az ezt követő eszközök, amelyek regisztrálva vannak a szolgáltatás az új regisztrációs kulcsot használja. Kattintson a **újragenerálása** megerősítéséhez. Az újragenerálás befejezése után értesítést kap.

    ![Erősítse meg a fiókkulcs újbóli létrehozása](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Megjelenik egy új regisztrációs kulcsot.

5. Másolja ezt a kulcsot, és mentse az új eszközök regisztrálásához a szolgáltatásban.



## <a name="change-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs módosítása
Szolgáltatási adatok titkosítási kulcsai titkosíthatja a bizalmas felhasználói adatok, például a tárfiók hitelesítő adatait, a StorSimple-eszközt a StorSimple Manager szolgáltatásból küldött szolgálnak. Ezek a kulcsok rendszeresen változnak, ha a munkahelye kulcsrotálás szabályzat a tárolóeszközök kell. Lehet, hogy a kulcs módosítása folyamat némileg eltérnek attól függően, hogy nincs egyetlen vagy több, a StorSimple Manager szolgáltatás által kezelt eszközök. További információért ugorjon [StorSimple biztonsági és adatvédelmi](storsimple-8000-security.md).

A szolgáltatásadat-titkosítási kulcs módosítása – 3. lépés folyamat során a rendszer:

1. Az Azure Resource Manager használatával a Windows PowerShell-parancsfájlok, engedélyezze egy eszközön módosítani a szolgáltatásadat-titkosítási kulcs.
2. A szolgáltatási adatok titkosítási kulcsváltozás storsimple-höz készült Windows PowerShell használatával kezdeményeznek.
3. Ha egynél több StorSimple-eszköz, frissítse a szolgáltatásadat-titkosítási kulcs az eszközökön.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. lépés: A szolgáltatásadat-titkosítási kulcs módosítása eszköz Windows PowerShell-parancsfájl használatával
Általában az eszköz-rendszergazdai kérni fogja, hogy a szolgáltatás-rendszergazda engedélyezi-e az eszközöket, hogy a szolgáltatási adatok titkosítási kulcsainak módosítása. A szolgáltatás-rendszergazda segítségével hitelesíti majd módosíthatja a kulcsot az eszközön.

Ebben a lépésben az Azure Resource Manager-alapú szkript használatával történik. A szolgáltatás rendszergazdája választhat, amely számára engedélyezni egy eszköz. Az eszköz ezután jogosult a szolgáltatási adatok titkosítási kulcsváltozás folyamat elindításához. 

A parancsfájl használatával kapcsolatos további információkért látogasson el [engedélyezés – ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Mely eszközöket jogosult-e a szolgáltatási adatok titkosítási kulcsainak módosítása?
Egy eszköz a következő feltételeknek kell megfelelnie, mielőtt kezdeményezheti a szolgáltatási adatok titkosítási kulcs módosításokat is engedélyezhető:

* Az eszköz szolgáltatási adatok titkosítási kulcs módosítása engedélyezési jogosult legyen online állapotban kell lennie.
* Engedélyezheti ugyanazon az eszközön újra 30 perc után, ha a kulcs módosítása nem lett inicializálva.
* Engedélyezheti egy másik eszközön, feltéve, hogy a kulcs módosítása a korábban jogosult eszközre nem lett inicializálva. Miután az új eszköz engedélyezett, a régi eszköz a módosítás nem kezdeményezhető.
* Egy eszköz nem lehet engedélyezni, amíg folyamatban van az alkalmatlanság a szolgáltatásadat-titkosítási kulcs.
* Egy eszköz során néhány eszközt a szolgáltatásban regisztrált kell leváltani a titkosítást, míg mások nem engedélyezhető. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. lépés: Használható Windows PowerShell-bővítménye kezdeményezni a szolgáltatásadat-titkosítási kulcs módosítása
Ez a lépés a Windows PowerShellben, az arra jogosult a StorSimple eszközön a StorSimple adapter történik.

> [!NOTE]
> Nincs művelet elvégezhető az Azure Portalon a StorSimple Manager szolgáltatás a kulcshoz kapcsolódó kulcsváltás befejeződéséig.


Ha az eszköz soros konzoljához való csatlakozáshoz a Windows PowerShell felületet használ, hajtsa végre az alábbi lépéseket.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>A szolgáltatási adatok titkosítási kulcsváltozás kezdeményezése
1. Jelentkezzen be a teljes körű hozzáféréssel az 1. lehetőséget válassza.
2. A parancssorba írja be a következőt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Miután a parancsmag sikeresen befejeződött, megjelenik egy új szolgáltatásadat-titkosítási kulcs. Másolja és mentse ezt a kulcsot használja a 3. lépésében ezt a folyamatot. Ezt a kulcsot a StorSimple Manager szolgáltatásban regisztrált összes többi eszköz frissítéséhez használható.
   
   > [!NOTE]
   > Ez a folyamat engedélyezése a StorSimple eszköz négy órán belül kell kezdeményezni.
   > 
   > 
   
   Ez az új kulcs kell leküldeni a szolgáltatásban regisztrált összes eszközt, majd küldendő a szolgáltatást. A szolgáltatás irányítópultján egy figyelmeztetés fog megjelenni. A szolgáltatás le fogja tiltani, hogy a regisztrált eszközökön a műveleteket, és az eszköz-rendszergazdai majd a szolgáltatásadat-titkosítási kulcs az egyéb eszközein futó frissítenie kell. Azonban a i (a gazdagép a felhőbe történő adatküldés) nem megszakad.
   
   Ha egy adott eszköz a szolgáltatáshoz regisztrált, a helyettesítő folyamat befejeződött, és továbbléphet a következő lépéssel. Ha a szolgáltatáshoz regisztrált több eszköze van, folytassa a 3. lépés.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. lépés: Frissítés a szolgáltatásadat-titkosítási kulcs a más StorSimple eszközökhöz
A Windows PowerShell felületét a StorSimple eszköz ezeket a lépéseket kell végrehajtani, ha több eszközt regisztrálni a StorSimple Manager szolgáltatáshoz. A kulcs a 2. lépésben beszerzett kell használni minden a fennmaradó a StorSimple eszköz frissítése regisztrálva a StorSimple Manager szolgáltatásban.

A következő lépésekkel frissítse a szolgáltatásadat-titkosítási az eszközön.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>A szolgáltatásadat-titkosítási kulcs fizikai eszközöknek a frissítéséhez
1. Storsimple-höz készült Windows PowerShell használatával kapcsolódni a konzolhoz. Jelentkezzen be a teljes körű hozzáféréssel az 1. lehetőséget válassza.
2. A parancssorba írja be:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Adja meg a szolgáltatásadat-titkosítási kulcs beolvasott [2. lépés: Storsimple-höz készült Windows PowerShell használatával kezdeményezheti a szolgáltatási adatok titkosítási kulcsváltozás](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>A szolgáltatásadat-titkosítási kulcs az összes 8010/8020-as felhőalapú berendezések frissítése
1. Letöltési és telepítési [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-parancsfájlt. 
2. Nyissa meg a Powershellt, és írja be a parancssorba:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ez a szkript biztosítja a szolgáltatásadat-titkosítási kulcs alatt az Eszközkezelő 8010/8020-as felhőalapú berendezések van beállítva.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Támogatott műveletek az 5.0-s frissítéssel előtti verziót futtató eszközök
Az Azure Portalon csak az 5.0-s vagy újabb frissítést futtató StorSimple-eszközök támogatottak. A régebbi verzióit futtató eszközök csak korlátozottan támogatják. Miután áttelepítette az Azure Portalon, az alábbi táblázat segítségével megismerheti, hogy mely műveletek 5.0-s frissítéssel előtti verziót futtató eszközök támogatottak.

| Művelet                                                                                                                       | Támogatott      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Eszköz regisztrálása                                                                                                               | Igen            |
| Például az általános beállítások, a hálózat és a biztonság konfigurálása                                                                | Igen            |
| Vizsgálat, töltse le és telepítse a frissítéseket                                                                                             | Igen            |
| Eszköz inaktiválása                                                                                                               | Igen            |
| Eszköz törlése                                                                                                                   | Igen            |
| Hozzon létre, módosítani és törölni a kötettárolót                                                                                   | Nem             |
| Létrehozása, módosítása és a egy kötet törlése                                                                                             | Nem             |
| Létrehozása, módosítása és a egy biztonsági mentési szabályzat törlése                                                                                      | Nem             |
| Manuális biztonsági mentés készítése                                                                                                            | Nem             |
| Ütemezett biztonsági mentés készítése                                                                                                         | Nem alkalmazható |
| Visszaállítás a biztonságimásolat-készlet                                                                                                        | Nem             |
| Az Update 3.0-s és újabb verzióit futtató eszközökre klónozása <br> A forráseszközt előtt Update 3.0-s verzióját futtatja.                                | Igen            |
| Az Update 3.0 előtti verziót futtató eszközökre klónozása                                                                          | Nem             |
| Forrás eszközként feladatátvétel <br> (egy eszközről verziót futtat, 3.0-s frissítést futtató eszközről egy korábbi Update 3.0-s és újabb verziók)                                                               | Igen            |
| Cél eszközként feladatátvétel <br> (a szoftververzió előtt Update 3.0-es eszközök)                                                                                   | Nem             |
| Riasztás törlése                                                                                                                  | Igen            |
| Biztonsági mentési szabályzatok, a biztonságimásolat-katalógus, kötetek, kötettárolók, figyelési diagramok, feladatok és a klasszikus portálon létrehozott riasztások megtekintése | Igen            |
| Be- és eszközvezérlők kikapcsolása                                                                                              | Igen            |


## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [StorSimple üzembehelyezési folyamat](storsimple-8000-deployment-walkthrough-u2.md).
* Tudjon meg többet [kezelése a StorSimple tárfiók](storsimple-8000-manage-storage-accounts.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).
