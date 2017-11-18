---
title: "Az Azure-ban a StorSimple Device Manager szolgáltatás üzembe helyezése |} Microsoft Docs"
description: "Azt ismerteti, hogyan hozhat létre, és törölje a StorSimple Device Manager szolgáltatást, az Azure portálon, és a szolgáltatás regisztrációs kulcsának kezelését ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: alkohli
ms.openlocfilehash: 5f31e32bb7cbd747af2e03699cfb2c6418828f8d
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>A StorSimple Device Manager szolgáltatás a StorSimple 8000 sorozat eszközeire üzembe helyezése

## <a name="overview"></a>Áttekintés

A StorSimple Device Manager szolgáltatás a Microsoft Azure-ban és több StorSimple eszközt csatlakozik. Miután létrehozta a szolgáltatást, ezáltal az adminisztratív terhek minimalizálása egységes, központi helyről, a StorSimple eszköz Manager szolgáltatáshoz csatlakozó eszközök kezelésére használhatja.

Ez az oktatóanyag azt ismerteti, hogyan létrehozása, törlés, a szolgáltatás és a szolgáltatás regisztrációs kulcsának kezelése. Az ebben a cikkben szereplő információ is alkalmazható, csak a StorSimple 8000 sorozat eszközeire. További információ a StorSimple virtuális tömbök Ugrás [a StorSimple virtuális tömb StorSimple Device Manager szolgáltatás telepítése](storsimple-virtual-array-manage-service.md).

## <a name="create-a-service"></a>Szolgáltatás létrehozása
Hozzon létre a StorSimple Device Manager szolgáltatást, akkor kell rendelkeznie:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Egy aktív Microsoft Azure storage-fiók
* A kezelési használt számlázási adatokat

Csak egy nagyvállalati szerződéssel rendelkező előfizetések engedélyezettek. A klasszikus Azure portálon rendelkező Microsoft Sponsorship előfizetések nem támogatottak az Azure portálon. A következő üzenet jelenik meg egy nem támogatott előfizetés használatakor:

![Nem érvényes előfizetés](./media/storsimple-8000-manage-service/subscription-not-valid.jpg)

Választhatja azt is, a szolgáltatás létrehozásakor egy alapértelmezett tárfiók létrehozásához.

Egyetlen szolgáltatás több eszközöket kezelheti. Azonban egy eszköz nem terjedhetnek ki több szolgáltatásra. A nagyvállalatok különböző előfizetések, szervezetek, vagy még a központi telepítési helyek több szolgáltatás példánya is lehet. 

> [!NOTE]
> Kezelheti a StorSimple 8000 sorozat eszközeire és a StorSimple virtuális tömbök StorSimple Device Manager szolgáltatás külön példányát kell.

A következő lépésekkel szolgáltatás létrehozása.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Minden egyes StorSimple Device Manager szolgáltatás található a következő attribútumokat:

* **Név** – a nevét, a StorSimple eszköz Manager szolgáltatás rendelt létrehozásakor. **A szolgáltatás neve nem lehet módosítani, a szolgáltatás létrehozása után. Ez akkor is igaz, az egyéb entitások, például az eszközök, a kötetek, a kötettárolók és a biztonsági mentési házirendek, amelyek nem nevezhető át az Azure portálon.**
* **Állapot** – a szolgáltatás, amely lehet állapotának **aktív**, **létrehozása**, vagy **Online**.
* **Hely** – az a földrajzi hely, amely a StorSimple eszköz telepítve lesz.
* **Előfizetés** – a számlázási előfizetés, amely kapcsolódik a szolgáltatáshoz.

## <a name="move-a-service-to-azure-portal"></a>Helyezze át a szolgáltatás Azure-portálon
A StorSimple 8000 series mostantól kezelhető az Azure portálon. Ha rendelkezik meglévő szolgáltatással a StorSimple eszközök kezelésére, javasoljuk, hogy a szolgáltatás helyezi át az Azure-portálon. A StorSimple Manager szolgáltatás a klasszikus Azure portálon 2017. szeptember 30 után már nem támogatott. Ha szeretné helyezni az új Azure-portálra, tekintse meg a [átmenet szempontjai](#considerations-for-transition). 

> [!NOTE]
> 2017. október 5., kezdve a hagyományos StorSimple eszköz kezelői automatikusan áthelyezi az új Azure-portálon. Ez egy fázisokra bontva történő bevezetéséhez, és meg információkat a e-mailek és a portál értesítések frissítjük. Ha kérdése van, lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

### <a name="considerations-for-transition"></a>Átmenet szempontjai

Tekintse át a hatását, hogy az új Azure-portálon való áttelepítése előtt helyezze át a szolgáltatást.

> [!NOTE]
> A meglévő Azure Service Management (ASM) PowerShell-parancsmagok használata nem támogatott, az új Azure-portálon történő áthelyezése után. Frissítse a parancsfájlok az Azure Resource Manager SDK-t az eszközök felügyeletére. További információkért látogasson el a [parancsfájlok használata Azure Resource Manager SDK-alapú StorSimple Eszközkezelés](storsimple-8000-automation-azurerm-scripts.md).
> Az új Azure-portálon 3.0-s vagy újabb frissítés rendszerű eszközöket támogatja. Ha az eszköz nem naprakész, erősen ajánlott frissítési 5 minél hamarabb alkalmazni.

#### <a name="before-you-transition"></a>Áttérés előtt

* Az eszköz fut frissítés 3.0-s vagy újabb. Ha az eszköz egy régebbi verziója fut, javasoljuk, hogy telepítse az 5. frissítésétől a gyorsjavítások a módszerrel. További információkért látogasson el [telepítése frissítés 5](storsimple-8000-install-update-5.md). Ha egy StorSimple felhő készülék (8010-es/8020-as modell) használ, egy felhőalapú készülék nem frissíthető. Hozzon létre egy új felhőalapú készülék frissítés 5.0, és ezután átveheti a létrehozott új felhő készülék szoftver legújabb verzióját használja.

* Után az új Azure-portálra átalakulnak, a klasszikus Azure portálon kezelheti a StorSimple eszköz nem használhat.

* Az átmenet nem zavaró, és az eszköz nincs leállás.

* A megadott előfizetéshez tartozó összes StorSimple eszköz kezelői átalakulnak.

#### <a name="during-the-transition"></a>Az áttérés közben

* Az eszköz nem tudja kezelni a portálról.
* Műveletek, például a rétegezési és ütemezett biztonsági mentések továbbra is megtörténik.
* Ne törölje a régi StorSimple eszköz kezelői, amíg folyamatban van az átmenet.

#### <a name="after-the-transition"></a>Az áttérés után

* Az eszközök már nem kezelheti a klasszikus portálon.

* A meglévő Azure Service Management (ASM) PowerShell-parancsmagok használata nem támogatott. Frissítse a parancsfájlok az Azure Resource Manageren keresztül eszközei kezeléséhez. Erőforrás-kezelő SDK használatával mintaparancsfájlok, tekintse meg a [storsimpledevicemgmttools github](https://github.com/anoobbacker/storsimpledevicemgmttools).

* A szolgáltatás és eszköz konfigurációs megmaradnak. A kötetek és a biztonsági másolatok is átalakulnak az Azure portálon.

### <a name="begin-transition"></a>Átmenet megkezdése

A következő lépésekkel szüntesse meg a szolgáltatás az Azure portálon.

1. Nyissa meg az új Azure-portálon a meglévő StorSimple Manager szolgáltatáshoz.
    ![További szolgáltatások](./media/storsimple-8000-manage-service/service-browse01.png) ![jelölje be az Eszközkezelő](./media/storsimple-8000-manage-service/service-browse02.png)

2. Megjelenik egy értesítés, amely tájékoztatja, hogy a StorSimple Device Manager szolgáltatás jelenleg elérhető az Azure portálon. Az Azure portálon a szolgáltatás nevezzük a StorSimple eszköz Manager szolgáltatást.
    ![Áttelepítési értesítés](./media/storsimple-8000-manage-service/service-transition1.jpg)
    
    1. Győződjön meg arról, hogy átolvasta az áttelepítés teljes hatását.
    2. Tekintse át a klasszikus portálon kerül a StorSimple-eszköz kezelők listáját.

3. Kattintson a **áttelepítése**. Az átmenet kezdődik, és néhány percet is igénybe vehet.

Az átmenet befejezése után az eszközök a StorSimple Device Manager szolgáltatás az Azure portálon keresztül is kezelheti. Ha nem látja az lehetőséget az Azure-portálon való áttelepítésre, de szeretné helyezni, akkor [igényelnie](https://aka.ms/ss8000-cx-signup).

## <a name="supported-operations-on-devices-running-versions-prior-to-update-30"></a>Támogatott műveletek előtt frissítés 3.0 verziót futtató eszközök
Az Azure portálon csak a 3.0-s és újabb rendszer Update futtatása StorSimple-eszközöket támogatja. A régebbi verzióját futtató eszközök csak korlátozottan támogatják. Miután áttelepítette az Azure portálra, az alábbi táblázat segítségével milyen műveletek előtt frissítés 3.0 verziót futtató eszközökön támogatott.

| Művelet                                                                                                                       | Támogatott      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Eszköz regisztrálása                                                                                                               | Igen            |
| Például az általános beállítások, a hálózati és a biztonság konfigurálása                                                                | Igen            |
| Vizsgálat, töltse le és telepítse a frissítéseket                                                                                             | Igen            |
| Eszköz inaktiválása                                                                                                               | Igen            |
| Eszköz törlése                                                                                                                   | Igen            |
| Létrehozása, módosítása és törlése a kötettároló                                                                                   | Nem             |
| Létrehozása, módosítása és kötet törlése                                                                                             | Nem             |
| Létrehozása, módosítása és törlése a biztonsági mentési házirend                                                                                      | Nem             |
| Manuális biztonsági mentés készítése                                                                                                            | Nem             |
| Ütemezett biztonsági mentés készítése                                                                                                         | Nem alkalmazható |
| Állítsa vissza egy Fájljelző                                                                                                        | Nem             |
| Klónozza a futtató eszközre, az Update 3.0-s és újabb verziók <br> A forráseszközt előtt frissítés 3.0-s verzióját futtatja.                                | Igen            |
| Egy korábbi frissítés 3.0 verziók futtató eszközre klónozása                                                                          | Nem             |
| Forrás eszközként feladatátvétel <br> (futtató eszközről verzióra való frissítés 3.0-t futtató eszközeiken előzetes frissítés 3.0-s és újabb verziók)                                                               | Igen            |
| Cél eszközként feladatátvétel <br> (az Update 3.0 előtt szoftververziót futtató eszköz)                                                                                   | Nem             |
| Riasztás törlése                                                                                                                  | Igen            |
| Biztonsági mentési házirendek, biztonságimásolat-katalógus, kötetek, kötettárolók, figyelési diagramokat, feladatok és a klasszikus portálon létrehozott riasztások megtekintése | Igen            |
| És eszközvezérlők bekapcsolása                                                                                              | Igen            |


## <a name="delete-a-service"></a>A szolgáltatás törlése

A szolgáltatás törlése előtt győződjön meg arról, hogy nem csatlakoztatott eszközök-k használják. Ha a szolgáltatás használja, kapcsolja ki a csatlakoztatott eszközök. Az inaktiválás művelet-kiszolgálón az eszköz és a szolgáltatás közötti kapcsolat, de a felhőben az eszköz adatok megőrzése érdekében.

> [!IMPORTANT]
> Szolgáltatás törlését követően a művelet nem vonható vissza. Minden olyan eszköz, amely a szolgáltatás használatával lett kell állítani a gyári beállításokat egy másik szolgáltatás használata előtt. Ebben a forgatókönyvben a helyi adatok az eszköz, valamint a konfigurációs elvész.

A következő lépésekkel törölni a szolgáltatást.

### <a name="to-delete-a-service"></a>A szolgáltatás törlése

1. Keresse meg a törölni kívánt szolgáltatást. Kattintson a **erőforrások** ikon és majd adjon meg a megfelelő feltételek kereséséhez. A keresési eredmények között kattintson a törölni kívánt szolgáltatást.

    ![Törölje a keresési szolgáltatást](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Ezzel megnyitná a StorSimple Device Manager szolgáltatás panelre. Kattintson a **Törlés** gombra.

    ![Szolgáltatás törlése](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kattintson a **Igen** a megerősítési értesítés. A szolgáltatás a törlendő néhány percig is eltarthat.

    ![Törlés megerősítése](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése

Miután sikeresen létrehozott egy szolgáltatást, szüksége lesz a StorSimple eszköz regisztrálása a szolgáltatásban. Az első StorSimple eszköz regisztrálása, szüksége lesz a szolgáltatás regisztrációs kulcsának. További eszközök regisztrálása egy meglévő StorSimple szolgáltatásban, mind a regisztrációs kulcs, és a szolgáltatásadat-titkosítási kulcs (amely a regisztráció során van az első eszközön létre) van szükség. A szolgáltatásadat-titkosítási kulcs kapcsolatos további információkért lásd: [StorSimple biztonsági](storsimple-8000-security.md). A regisztrációs kulcs kaphat elérése **kulcsok** a StorSimple Device Manager panelen.

A következő lépésekkel Szolgáltatásregisztrációs kulcs lekérése.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

A szolgáltatás regisztrációs kulcsának tartsa biztonságos helyen. Ezt a kulcsot, valamint a szolgáltatásadat-titkosítási kulcs, ez a szolgáltatás további eszközöket regisztrálni kell. Miután beszerezte a szolgáltatás regisztrációs kulcsának, konfigurálnia kell az eszköz StorSimple felület a Windows PowerShell segítségével.

A regisztrációs kulcs használatával, lásd: [3. lépés: konfigurálja, és a Windows PowerShell segítségével az eszközt regisztrálni kell a StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Kell egy Szolgáltatásregisztrációs kulcs újbóli létrehozása, ha kulcs elforgatás végrehajtásához szükséges, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. Amikor újragenerálja a kulcsot, az új kulccsal csak a következő eszközök regisztrálása. A már regisztrált eszközöket nem érinti ez a folyamat.

A következő lépésekkel egy Szolgáltatásregisztrációs kulcs újragenerálása.

### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcs újragenerálásához
1. Az a **StorSimple Device Manager** paneljén lépjen **felügyeleti &gt;**  **kulcsok**.
    
    ![Kulcsok panel](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Az a **kulcsok** panelen kattintson a **újragenerálja**.

    ![Kattintson a újragenerálása](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Az a **Szolgáltatásregisztrációs kulcs újragenerálása** panelt, tekintse át a művelet megadása kötelező, ha a kulcsok újragenerálása vannak. Minden a további regisztrált eszközök ezzel a szolgáltatással az új regisztrációs kulcsot használja. Kattintson a **újragenerálja** megerősítéséhez. Értesítés jelenik meg a újragenerálása befejeződése után.

    ![Regenerate megerősítése](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Egy új Szolgáltatásregisztrációs kulcs jelenik meg.

5. Másolja ki ezt a kulcsot, és mentse az új eszköz regisztrálása ezt a szolgáltatást.



## <a name="change-the-service-data-encryption-key"></a>A szolgáltatásadat-titkosítási kulcs módosítása
Szolgáltatás adattitkosítási kulcsokat bizalmas felhasználói adatok, például a tárfiók hitelesítő adatait, a StorSimple eszközt a StorSimple Manager szolgáltatás a küldött titkosításához használt. Ezek a kulcsok rendszeresen módosítható, ha az IT-szervezet egy kulcs Elforgatás szabályzattal rendelkezik a tárolóeszközök kell. A kulcs változási folyamat kis mértékben eltérhet attól függően, hogy van egy egyetlen vagy több eszközön, a StorSimple Manager szolgáltatás kezeli. További információkért látogasson el [StorSimple biztonság és adatvédelem](storsimple-8000-security.md).

A szolgáltatásadat-titkosítási kulcs módosítása egy 3 lépéses folyamat:

1. Az Azure Resource Manager használatával a Windows PowerShell-parancsfájlok, engedélyezéséhez módosítsa a szolgáltatásadat-titkosítási kulcs az eszköz.
2. A Windows PowerShell használatával a StorSimple, indítsa el a szolgáltatás titkosítási kulcs változását.
3. Ha egynél több StorSimple eszközt, a más eszközökön a szolgáltatásadat-titkosítási kulcs frissítése

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1. lépés: A Windows PowerShell parancsfájl módosítása a szolgáltatásadat-titkosítási kulcs az eszköz hitelesítése
Általában az eszköz-rendszergazdai kérni fogja, hogy a szolgáltatás-rendszergazda engedélyezi-e az eszközöket, hogy módosítsa a szolgáltatás adattitkosítási kulcsokat. A szolgáltatás-rendszergazda fog majd engedélyezni az eszközt, hogy a kulcs módosítása.

Ebben a lépésben az Azure Resource Manager-alapú parancsfájl használatával történik. A szolgáltatás rendszergazda kiválaszthat olyan eszköz, amely jogosult engedéllyel. Az eszköz majd engedélyezve az szolgáltatás titkosítási kulcsváltozás folyamat elindításához. 

A parancsfájl használatával kapcsolatos további információkért látogasson el [engedélyezés-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Mely eszközök is engedélyezni kell, hogy módosítsa a szolgáltatás adattitkosítási kulcsokat?
Egy eszköz a következő feltételeknek kell megfelelnie, mielőtt szolgáltatás titkosítási kulcs adatváltozásokat kezdeményezésére is engedélyezhető:

* Lehet, hogy az eszköz online jogosult legyen a szolgáltatás adatokat titkosítási kulcsváltozás engedélyezése.
* 30 perc elteltével újra ugyanarra az eszközre engedélyezhető, ha a kulcs változás nem lett inicializálva.
* Egy másik eszközön, feltéve, hogy a kulcsváltozás nem kezdeményezte a korábban meghatalmazott eszköz engedélyezhető. Után az új eszköz engedélyezve van-e, a régi eszköz nem indítható el a módosítást.
* Egy eszköz nem lehet engedélyezni, amíg folyamatban van az a szolgáltatás adattitkosítási kulcshoz kapcsolódó kulcsváltás.
* Ha egyes a szolgáltatásban regisztrált eszközöket rendelkezik tanúsítványváltást a titkosítási míg mások számára nem engedélyezhető a eszköz. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2. lépés: Használja a Windows PowerShell-lel a szolgáltatás titkosítási kulcs változását kezdeményezése
Ez a lépés StorSimple felületet a jogosult StorSimple eszközön a Windows PowerShell történik.

> [!NOTE]
> Nincsenek műveletek végrehajtható az Azure-portálon a StorSimple Manager szolgáltatás a kulcshoz kapcsolódó kulcsváltás után befejezéséig.
> 
> 

Ha az eszköz soros konzoljához való csatlakozáshoz a Windows PowerShell-felületet használnak, a következő lépésekkel.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>A szolgáltatás titkosítási kulcs változását kezdeményezése
1. A beállításnak 1 teljes hozzáféréssel rendelkező bejelentkezni.
2. A parancssorba írja be:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Miután a parancsmag sikeresen befejeződött, kap egy új szolgáltatásadat-titkosítási kulcs. Másolja ki és mentse a kulcs használható a 3. lépésben a folyamat során. Ezt a kulcsot a StorSimple Manager szolgáltatásban regisztrált összes fennmaradó eszköz frissítéséhez használható.
   
   > [!NOTE]
   > Ez a folyamat engedélyezése a StorSimple eszköz négy órán belül kell kezdeményezni.
   > 
   > 
   
   Ez az új kulcs majd kerül a szolgáltatás kerül át a szolgáltatásban regisztrált összes eszközt. A szolgáltatás irányítópultján egy figyelmeztetés fog megjelenni. A szolgáltatás le fogja tiltani a regisztrált eszközökön a műveleteket, és az eszköz-rendszergazdai majd a szolgáltatásadat-titkosítási kulcs az egyéb eszközein futó frissítenie kell. Azonban az i/o műveletek (gazdagép adatokat küldeni a felhőben) fog nem működni.
   
   Ha a szolgáltatáshoz regisztrált egyetlen eszközt, a helyettesítő folyamat befejeződött, és ugorjon a következő lépéssel. Ha a szolgáltatáshoz regisztrált több eszközön, ugorjon a 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3. lépés: A más StorSimple eszközökhöz a szolgáltatásadat-titkosítási kulcs frissítése
A Windows PowerShell-felületet a StorSimple eszköz ezeket a lépéseket kell végrehajtani, ha több eszközt regisztrálni a StorSimple Manager szolgáltatásban. A 2. lépésben beszerzett kulcsot kell használni minden a fennmaradó StorSimple eszköz frissítése regisztrálni a StorSimple Manager szolgáltatással.

Frissítéséhez hajtsa végre a következő lépéseket a szolgáltatásadat-titkosítási az eszközön.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>A fizikai eszközök a szolgáltatásadat-titkosítási kulcs frissítése
1. Használja a Windows PowerShell-lel a konzolhoz való csatlakozáshoz. A beállításnak 1 teljes hozzáféréssel rendelkező bejelentkezni.
2. A parancssorba írja be:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Adja meg a szolgáltatásadat-titkosítási kulcs beolvasott [2. lépés: használja a Windows PowerShell-lel kezdeményezheti a szolgáltatás titkosítási kulcs változását](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Az a 8010-es/8020-as modellt felhő készülékek a szolgáltatásadat-titkosítási kulcs frissítése
1. Letöltési és telepítési [frissítés-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell-parancsfájlt. 
2. Nyissa meg a Powershellt, és írja be a parancssorba:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ezt a parancsfájlt biztosítja, hogy szolgáltatásadat-titkosítási kulcs az Eszközkezelőben a 8010-es/8020-as modellt felhő készülékek be van állítva.

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple telepítési folyamatának](storsimple-8000-deployment-walkthrough-u2.md).
* További információ [tárfiók felügyelete a StorSimple](storsimple-8000-manage-storage-accounts.md).
* További tudnivalók a [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).
