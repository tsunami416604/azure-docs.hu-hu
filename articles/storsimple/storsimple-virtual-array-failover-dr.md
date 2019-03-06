---
title: A StorSimple Virtual Array katasztrófa utáni helyreállítás és az eszköz feladatátvételi |} A Microsoft Docs
description: Tudjon meg többet történő feladatátvételt mutatja a StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be3d98dc0b3a8119fb853493440c6fc78d65c5a2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454238"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>A StorSimple Virtual Array az Azure Portalon keresztül vész helyreállítási és az eszköz feladatátvétele

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a vész-helyreállítási a Microsoft Azure StorSimple Virtual Array többek között a részletes lépéseket a feladatátvételt egy másik virtuális tömbbe. A feladatátvétel lehetővé teszi, hogy helyezze át az adatokat egy *forrás* eszközt az adatközpontban, egy *cél* eszköz. A céleszköz azonos, vagy egy másik földrajzi hely található. Az eszköz feladatátvételi van a teljes eszköz számára. A feladatátvétel során a felhőbeli adatok az eszköz tulajdonjogát módosítja a céleszközön a.

Ez a cikk csak akkor alkalmazható, a StorSimple Virtual Arrayt. A 8000-es sorozatú eszköz feladatainak átadása, lépjen a [eszközön feladatátvétel és vészhelyreállítás helyreállítási a StorSimple eszköz](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Mit jelent a katasztrófa utáni helyreállítás és az eszköz feladatátvételi?

A vész-helyreállítási helyzetekre az elsődleges eszköz leáll a működése. Ebben a forgatókönyvben áthelyezheti egy másik eszközre a sikertelen eszközhöz társított felhőalapú adatokat. Az elsődleges eszköz akkor is használhatja a *forrás* és a egy másik eszköz, akkor adja meg a *cél*. Ezt a folyamatot nevezzük a *feladatátvételi*. A feladatátvétel során minden kötetet vagy a megosztások a forráseszközről származó tulajdonjogai módosulnak, és átkerülnek a céleszközön. Az adatok szűrése nem engedélyezett.

DR van modellezve a teljes eszköz visszaállítás a heat map-alapú rétegezés segítségével, és nyomon követését. Egy megadott hőtérképrészlet értéket rendel az adatai alapján határozza meg a hőtérkép olvasási és írási. A megadott hőtérképrészlet leképezése majd Rétegek a legalacsonyabb megadott hőtérképrészlet adattömbök felhőbeli először miközben megőrzi a nagy meleg (leggyakrabban használt) adattömbök a helyi rétegen. A Vészhelyreállítás során a StorSimple és az adatok a felhőből rehidratálási visszaállítása az hőtérkép. Az eszköz lekéri a kötetek/megosztások utolsó legutóbbi biztonsági mentés (a cégen belül), és hajtja végre a visszaállítást a biztonsági másolatból. A virtuális tömb összehangolja a teljes Vészhelyreállítási folyamatot.

> [!IMPORTANT]
> A forráseszközt törlődik az eszköz feladatátvételi végén, és ezért a feladat-visszavétel nem támogatott.
> 
> 

Vész-helyreállítási az eszköz feladatátvételi funkciójával vezényelt van, és kezdeményezi a **eszközök** panelen. Ezen a panelen vannak a StorSimple-Eszközkezelő szolgáltatáshoz csatlakozó összes StorSimple eszközt. Minden eszköz mellett láthatja a rövid név, állapot, kiépített és maximális kapacitását, típusa és modell.

## <a name="prerequisites-for-device-failover"></a>Az eszköz feladatátvételéhez Előfeltételek

### <a name="prerequisites"></a>Előfeltételek

Eszköz feladatátvétel esetén győződjön meg arról, hogy a következő előfeltételek teljesülését:

* A forrás-eszköznek ahhoz van szüksége, kell lennie egy **inaktív** állapota.
* Az eszköznek meg kell megjelenjen **beállításra kész** az Azure Portalon. Virtuális tömb létrehozása cél megegyező vagy annál nagyobb kapacitás. A helyi webes felhasználói felületének használatával konfigurálhatja, és regisztrálta sikeresen a cél virtuális tömb.
  
  > [!IMPORTANT]
  > Ne kísérelje meg a szolgáltatásban regisztrált virtuális eszköz konfigurálása. Nincs eszközkonfiguráció kell végezni a szolgáltatáson keresztül.
  > 
  > 
* A céleszköz nem lehet a neve megegyezik a forráseszközt.
* Az azonos típusú a forrás- és eszköz kell. Csak átveheti egy másik fájlkiszolgálóhoz fájlkiszolgálóként konfigurált virtuális tömböt. Ugyanez érvényes iSCSI-kiszolgálót.
* Egy fájlkiszolgáló DR azt javasoljuk, hogy a céleszköz csatlakoztat ugyanabban a tartományban, mint a forrás. Ez a konfiguráció biztosítja, hogy a megosztási engedélyek automatikus feloldása. Csak a feladatátvételt, hogy a céleszköz ugyanabban a tartományban.
* A rendelkezésre álló terület a Vészhelyreállításhoz tartozó eszközök, amelyek az eszköz képest ugyanolyan vagy nagyobb kapacitású eszközök. Az eszközök csatlakoznak a szolgáltatáshoz, de nem felelnek meg a feltételeknek megfelelő terület cél eszközök nem érhetők el.

### <a name="other-considerations"></a>Egyéb szempontok

* Egy tervezett feladatátvétel 
  
  * Azt javasoljuk, hogy tegye meg a kötetek vagy megosztások kapcsolat nélküli módban a forrás eszközön.
  * Azt javasoljuk, hogy biztonsági másolatot készít az eszközön, és folytassa a feladatátvétel minimálisra csökkentésre érdekében. 
* A nem tervezett feladatátvételt az eszköz az adatok helyreállításához használja a legutolsó biztonsági másolatán.

### <a name="device-failover-prechecks"></a>Eszköz-feladatátvétel Eszközfrissítések

Mielőtt elkezdi a Vészhelyreállítás, az eszköz Eszközfrissítések hajt végre. Az ellenőrzések biztosíthatja, hogy a nem fordult elő hiba, amikor a DR megkezdése. Az Eszközfrissítések a következők:

* A storage-fiók ellenőrzése.
* Az Azure-ban a felhő közötti kapcsolat ellenőrzése.
* Ellenőrzi a szabad terület a céleszközön.
* Ellenőrzi, hogy rendelkezik-e az iSCSI-kiszolgáló forrás eszköz kötet
  
  * ACR neve érvénytelen.
  * érvényes IQN-t (legfeljebb lennie 220 karakternél).
  * érvényes CHAP-jelszó (12 és 16 karakter).

Ha bármelyik az előző prechecks sikertelen, a Vészhelyreállítás nem folytatódhat. Oldja meg ezeket a hibákat, és próbálkozzon újra a Vészhelyreállítás.

A DR sikeres befejezése után a tulajdonjogát, a forrás-eszközökön a felhőbeli adatok átkerülnek a céleszközön. A forráseszközt már majd nem érhető el a portálon. Az összes a kötetek/megosztások a forrás-eszközön való hozzáférés le lesz tiltva, és a céleszköz aktívvá válik.

> [!IMPORTANT]
> Ha az eszköz már nem érhető el, a rendszer a gazdagép kiépített virtuális gépet továbbra is fogyassza az erőforrásokat. A DR sikeres végrehajtása után törölheti a virtuális gép a gazdagép rendszerről.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Átadja a feladatokat a virtuális tömb

Azt javasoljuk, hogy kiépítése, konfigurálása, és egy másik, a StorSimple Virtual Array regisztrálni a StorSimple-Eszközkezelő szolgáltatással, mielőtt elkezdi a műveletet.

> [!IMPORTANT]
> 
> * Nem lehet átadja a feladatokat a StorSimple 8000 sorozatú eszköz az 1200-as virtuális eszközre.
> * Is átadja a feladatokat a Federal Information feldolgozása Standard (FIPS) engedélyezve van a virtuális eszköz FIPS engedélyezve van egy másik eszközt vagy a FIPS eszköz a Government Portalon üzembe helyezve.


A következő lépésekkel állíthatja vissza az eszközt a cél a StorSimple virtuális eszköz.

1. Hozzon létre és konfiguráljon egy céleszközt, amely megfelel a [eszközön feladatátvétel előfeltételeinek](#prerequisites). Az eszköz konfigurálása a helyi webes felhasználói felületen, és regisztrálja a StorSimple-Eszközkezelő szolgáltatáshoz. Ha létrehoz egy fájlkiszolgálón, nyissa meg az 1. lépés [fájlkiszolgálóként beállítása](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Ha létrehoz egy iSCSI-kiszolgálót, nyissa meg az 1. lépés [állítsa be az iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Kötetek/megosztások kapcsolat nélküli módban is a gazdagépen. A kötetek/megosztások offline, tekintse meg az operációsrendszer-specifikus utasításokat a gazdagép számára. Ha nincs már offline állapotban van, meg kell tennie a kötetek/megosztások kapcsolat nélküli az eszközön az alábbiak szerint.
   
    1. Lépjen a **eszközök** panelen, és válassza ki az eszközt.
   
    2. Lépjen a **beállítások > kezelése > megosztások** (vagy **beállítások > kezelése > kötetek**). 
   
    3. Válassza ki a megosztás/kötet, kattintson a jobb gombbal, és válassza ki **offline állapotba**. 
   
    4. Amikor a rendszer megerősítést kér, ellenőrizze **megértettem a megosztás offline állapotba helyezése hatását.** 
   
    5. Kattintson a **offline állapotba**.

3. A StorSimple-Eszközkezelő szolgáltatáshoz, lépjen a **felügyeleti > eszközök**. Az a **eszközök** panelen válassza ki, majd kattintson a forráseszközt.

4. Az a **irányítópultját** panelen kattintson a **inaktiválás**.

5. Az a **inaktiválás** panelen megerősítést kér. Az eszköz inaktiválása egy *állandó* folyamat, amely nem vonható vissza. Is figyelmezteti, a megosztások vagy kötetek offline érvénybe a gazdagépen. Írja be az eszköz nevét, és kattintson a **inaktiválás**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Elindul az inaktiválást. Az inaktiválást sikeres végrehajtása után kapni fog egy értesítést.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Az eszközök lapon az eszköz állapota most változik **inaktív**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Az a **eszközök** panelen válassza ki, majd kattintson a feladatátvételi inaktivált eszköz. 
9. Az a **irányítópultját** panelen kattintson a **átadja a feladatokat**. 
10. Az a **eszköz feladatainak átadása** panelen tegye a következőket:
    
    1. A forrás eszköz mezőt automatikusan kitölti a rendszer. Megjegyzés: az eszköz teljes adatméretével. Lehet, hogy az adatok mérete kisebb, mint az elérhető kapacitás a céleszközön. Tekintse át a részleteket, például az eszköz neve, teljes kapacitás és a megosztásokat, melyek feladatai át nevei eszköz társítva.

    2. Válassza ki a legördülő listából az elérhető eszközök, egy **céleszköz**. Csak azokat az eszközöket, amelyek elegendő kapacitással rendelkeznek a legördülő listában jelennek meg.

    3. Ellenőrizze, hogy **megértettem, hogy ez a művelet átadja az adatokat a céleszköznek**. 

    4. Kattintson a **átadja a feladatokat**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Egy feladatátvételi feladatot kezdeményez, és értesítést kap. Lépjen a **eszközök > feladatok** figyelése a feladatátvételt.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Az a **feladatok** egy feladatátvételi feladat létrehozása a forrás-eszköz panelen láthatók. Ez a feladat a DR Eszközfrissítések végzi.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Után az DR Eszközfrissítések sikeres, a feladatátvételi feladatot fog spustit novou kopii minden megosztás/kötet megtalálható a forrás-eszközön a visszaállítási feladat.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. A feladatátvétel befejezése után nyissa meg a **eszközök** panelen.
    
    1. Válassza ki, majd kattintson a StorSimple-eszköz, amely az eszköznek a feladatátvételi folyamat használták.
    2. Lépjen a **beállítások > kezelés > megosztások** (vagy **kötetek** Ha iSCSI-kiszolgáló). Az a **megosztások** panelen tekintheti meg a régi eszközről minden megosztás (kötetek).
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Kell [hozzon létre egy DNS-alias](https://support.microsoft.com/kb/168322) úgy, hogy csatlakozni próbál alkalmazásokat is első átirányítja az új eszköz.

## <a name="errors-during-dr"></a>A Vészhelyreállítás során hibák

**Felhőalapú kapcsolat szolgáltatáskimaradás a Vészhelyreállítás során**

Ha a cloud-kapcsolat megszakad, miután DR elindult, és az eszközön a visszaállítás befejeződött, a DR sikertelen lesz. Hiba értesítést kap. A céleszközön a Vészhelyreállításhoz van megjelölve *használhatatlan lesz.* Az azonos céloldali eszköz jövőbeli DRs-hez nem használható.

**Nem kompatibilis a Céleszközök**

A rendelkezésre álló terület eszközök nem rendelkeznek elég, ha a célból, hogy nincsenek-e nem kompatibilis a Céleszközök hibaüzenet jelenik meg.

**Precheck hibák**

Ha az egyik az Eszközfrissítések nem teljesül, akkor tekintse meg precheck hibák.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzleti folytonosság – vészhelyreállítás (BCDR)

Egy üzleti folytonossági vészhelyreállítási (BCDR) forgatókönyv akkor fordul elő, amikor a teljes Azure-adatközpont leáll a működése. Ez befolyásolhatja a StorSimple-Eszközkezelő szolgáltatás és az ahhoz tartozó StorSimple-berendezések.

Ha nincsenek regisztrált előtt vészhelyzet történt, akkor előfordulhat, hogy törölni kell ezeket az eszközöket a StorSimple a StorSimple-eszközök. A vészhelyzet után hozza létre újra, és konfigurálja azokat az eszközöket.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [felügyelheti a helyi webes felhasználói Felületet a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

