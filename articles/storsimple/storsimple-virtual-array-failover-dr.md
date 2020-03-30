---
title: Feladatátvétel és vészhelyreállítás a StorSimple virtuális tömbhöz
description: További információ a StorSimple virtuális tömb feladatátvételéről.
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
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467215"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Vészhelyreállítás és az eszköz feladatátvétele a StorSimple Virtual Arrayhez az Azure Portalon keresztül

## <a name="overview"></a>Áttekintés
Ez a cikk ismerteti a vész-helyreállítási a Microsoft Azure StorSimple virtuális tömb, beleértve a részletes lépéseket, hogy feladatátvétel egy másik virtuális tömb. A feladatátvétel lehetővé teszi, hogy az adatok áthelyezése a *forráseszközről* az adatközpontban a *céleszközre.* A céleszköz azonos vagy eltérő földrajzi helyen helyezkedhet el. Az eszköz feladatátvételaz egész eszközre. Feladatátvétel során a forráseszköz felhőbeli adatai a céleszköz tulajdonjogát módosítják.

Ez a cikk csak a StorSimple virtuális tömbök re vonatkozik. Egy 8000-es sorozatú eszköz sikertelensítéséhez nyissa meg [a StorSimple-eszköz eszközfeladat-átvételi és vészhelyreállítását.](storsimple-device-failover-disaster-recovery.md)

## <a name="what-is-disaster-recovery-and-device-failover"></a>Mi a vész-helyreállítási és az eszköz feladatátvétel?

Vész-helyreállítási (DR) forgatókönyv esetén az elsődleges eszköz leáll. Ebben a forgatókönyvben áthelyezheti a meghibásodott eszközhöz társított felhőadatokat egy másik eszközre. Az elsődleges eszközt *használhatja forrásként,* és megadhat egy másik eszközt *célként.* Ezt a folyamatot *feladatátvételnek*nevezzük. A feladatátvétel során a forráseszköz összes kötete vagy megosztása megváltozik a tulajdonjoga, és átkerül a céleszközre. Az adatok szűrése nem engedélyezett.

A DR teljes eszköz-visszaállításként van modellezve a hőtérkép-alapú rétegezés és nyomon követés használatával. A hőtérkép et úgy határozhatja meg, hogy az olvasási és írási minták alapján hőértéket rendel az adatokhoz. Ez a hőtérkép, majd rétegek a legalacsonyabb hőadat-adattömbök a felhőbe először, miközben a magas hő (leggyakrabban használt) adattömbök a helyi rétegben. A DR során a StorSimple a hőtérkép segítségével állítja vissza és hidratálja az adatokat a felhőből. Az eszköz lekéri az összes kötetet/megosztást a legutóbbi legutóbbi biztonsági mentésben (belsőleg meghatározva), és visszaállítja a biztonsági mentést. A virtuális tömb vezényli a teljes VÉSZ-folyamatot.

> [!IMPORTANT]
> A forráseszköz törlődik az eszköz feladatátvétel végén, és így a feladat-visszavétel nem támogatott.
> 
> 

Vész-helyreállítási az eszköz feladatátvételi szolgáltatáson keresztül van vezényelve, és az **Eszközök** panelről indul. Ez a panel tabulates az összes StorSimple eszközök csatlakozik a StorSimple Device Manager szolgáltatás. Az egyes eszközök láthatja a rövid név, állapot, kiépített és maximális kapacitás, típus és modell.

## <a name="prerequisites-for-device-failover"></a>Eszköz-feladatátvétel előfeltételei

### <a name="prerequisites"></a>Előfeltételek

Eszköz feladatátvétel esetén győződjön meg arról, hogy a következő előfeltételek teljesülnek:

* A forráseszköznek **inaktivált** állapotban kell lennie.
* A céleszköznek készen kell megjelennie az Azure Portalon **való beállításra.** Azonos vagy nagyobb kapacitású célvirtuális tömb kiépítése. A helyi webes felhasználói felület segítségével konfigurálhatja és sikeresen regisztrálhatja a célvirtuális tömböt.
  
  > [!IMPORTANT]
  > Ne próbálja meg konfigurálni a regisztrált virtuális eszközt a szolgáltatáson keresztül. A szolgáltatáson keresztül nem szabad eszközkonfigurációt végrehajtani.
  > 
  > 
* A céleszköz neve nem egyezhet meg a forráseszközzel.
* A forrás- és céleszköznek azonos típusúnak kell lennie. Csak fájlkiszolgálóként konfigurált virtuális tömbet lehet átadni egy másik fájlkiszolgálónak. Ugyanez igaz az iSCSI-kiszolgálókra is.
* A fájlkiszolgáló DR esetén azt javasoljuk, hogy a céleszközhöz csatlakozzon a forrással azonos tartományhoz. Ez a konfiguráció biztosítja a megosztási engedélyek automatikus feloldását. Csak a feladatátvétel a céleszköz ugyanabban a tartományban támogatott.
* A dr számára rendelkezésre álló céleszközök olyan eszközök, amelyek a forráseszközhöz képest azonos vagy nagyobb kapacitással rendelkeznek. Azok az eszközök, amelyek a szolgáltatáshoz csatlakoznak, de nem felelnek meg a megfelelő hely feltételeinek, nem érhetők el céleszközként.

### <a name="other-considerations"></a>Egyéb szempontok

* Tervezett feladatátvétel esetén:
  
  * Azt javasoljuk, hogy a forráseszközön lévő összes kötetet vagy megosztást offline állapotba kell helyeznie.
  * Azt javasoljuk, hogy készítsen biztonsági másolatot az eszközről, majd folytassa a feladatátvételt az adatvesztés minimalizálása érdekében.
* Nem tervezett feladatátvétel esetén az eszköz a legutóbbi biztonsági mentést használja az adatok visszaállításához.

### <a name="device-failover-prechecks"></a>Eszközfeladat-átvételi előzetes ellenőrzések

A VÉSZ-KEZELÉS megkezdése előtt az eszköz előzetes ellenőrzéseket hajt végre. Ezek az ellenőrzések segítenek annak biztosításában, hogy a VÉSZ-ellenőrzés megkezdésekor ne forduljanak elő hibák. Az előellenőrzések a következőket tartalmazzák:

* A tárfiók ellenőrzése.
* Az Azure-ral való felhőbeli kapcsolat ellenőrzése.
* A céleszközön rendelkezésre álló hely ellenőrzése.
* Annak ellenőrzése, hogy van-e iSCSI-kiszolgálóforrás-eszközkötete
  
  * érvényes ACR neveket.
  * érvényes IQN (legfeljebb 220 karakter).
  * érvényes CHAP jelszavak (12-16 karakter hosszú).

Ha az előző előcsekkek bármelyike sikertelen, nem folytathatja a vész-összedr. Oldja meg a problémákat, majd próbálkozzon újra a DR-vel.

A vész-érték sikeres befejezése után a forráseszközön lévő felhőadatok tulajdonjoga átkerül a céleszközre. A forráseszköz ezután már nem érhető el a portálon. A forráseszközön lévő összes kötethez/megosztáshoz való hozzáférés le van tiltva, és a céleszköz aktívvá válik.

> [!IMPORTANT]
> Bár az eszköz már nem érhető el, a gazdarendszeren kiépített virtuális gép továbbra is erőforrásokat fogyaszt. Miután a vész-vész-dr sikeresen befejeződött, törölheti ezt a virtuális gépet a gazdarendszerből.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Feladatátvétel virtuális tömbbe

Azt javasoljuk, hogy az eljárás futtatása előtt egy másik StorSimple virtuális tömböt létesítsen, konfiguráljon és regisztráljon a StorSimple Eszközkezelő szolgáltatásával.

> [!IMPORTANT]
> 
> * A StorSimple 8000 sorozatú eszközök nem adhatók át egy 1200-as virtuális eszköznek.
> * A szövetségi információfeldolgozási szabvány (FIPS) engedélyezett virtuális eszközről átveheti a feladatát egy másik FIPS-kompatibilis eszközre vagy a kormányzati portálon telepített nem FIPS-eszközre.


Hajtsa végre a következő lépéseket az eszköz visszaállításához a cél StorSimple virtuális eszközre.

1. Olyan céleszköz kiépítése és konfigurálása, amely megfelel az [eszközfeladat-átvétel előfeltételeinek.](#prerequisites) Töltse ki az eszköz konfigurációját a helyi webes felhasználói felületen keresztül, és regisztrálja azt a StorSimple Eszközkezelő szolgáltatásban. Fájlkiszolgáló létrehozásaesetén folytassa a [fájlkiszolgálóként beállítás első lépésével.](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device) ISCSI-kiszolgáló létrehozásakor folytassa az [iSCSI-kiszolgálóként beállítás első lépésével.](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)

2. Kötetek/megosztások offline állapotba helyezése az állomáson. A kötetek/megosztások offline állapotba hozásához olvassa el az operációs rendszerre vonatkozó utasításokat az állomáshoz. Ha még nem offline, az összes kötetet/megosztást offline állapotba kell helyeznie az eszközön az alábbi módon.
   
    1. Nyissa meg az **Eszközök** panelt, és válassza ki a készüléket.
   
    2. Nyissa meg **a Beállítások > > megosztások kezelése** (vagy a Beállítások > > **kötetek kezelése**). 
   
    3. Jelöljön ki egy megosztást/kötetet, kattintson a jobb gombbal, és válassza **az Offline mód parancsot.** 
   
    4. Amikor megerősítést kér, ellenőrizze, hogy **tisztában van-e a megosztás offline állapotba hozásának hatásával.** 
   
    5. Kattintson **a Kapcsolat nélküli mód**ra .

3. A StorSimple Eszközkezelő szolgáltatásban nyissa meg **a Felügyeleti > eszközök**lehetőséget. Az **Eszközök** panelen jelölje ki és kattintson a forráseszközre.

4. Az **Eszköz irányítópultján** kattintson az **Inaktiválás gombra.**

5. A **Kikapcsolás identifikáció** panelen megerősítést kér. Az eszköz inaktiválása olyan *állandó* folyamat, amely et nem lehet visszavonni. A rendszer arra is emlékezteti, hogy a megosztásokat/köteteket offline állapotba kell helyeznie a gazdagépen. Írja be a megerősítéshez az eszköz nevét, majd kattintson **az Inaktiválás gombra.**
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Megkezdődik az inaktiválás. Az inaktiválás sikeres befejezése után értesítést fog kapni.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Az Eszközök lapon az eszköz állapota **inaktivált**lesz.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Az **Eszközök** panelen jelölje ki és kattintson az inaktivált forráseszközfeladat-átvételhez. 
9. Az **Eszköz irányítópultpanelen** kattintson a **Feladatátvétel**gombra. 
10. A **Feladatátvétel eszköz** panelen tegye a következőket:
    
    1. A forráseszköz mezője automatikusan kitöltődik. Jegyezze fel a forráseszköz teljes adatméretét. Az adatok méretének kisebbnek kell lennie, mint a céleszközön rendelkezésre álló kapacitás. Tekintse át a forráseszközhöz társított részleteket, például az eszköz nevét, a teljes kapacitást és a feladatátvételre felkapott megosztások nevét.

    2. Az elérhető eszközök legördülő listájából válasszon egy **céleszközt**. Csak a megfelelő kapacitású eszközök jelennek meg a legördülő listában.

    3. Ellenőrizze, **hogy tudomásom szerint ez a művelet átfogja-e adni az adatokat a céleszköznek**. 

    4. Kattintson **a Feladatátvétel gombra.**
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. A feladatátvételi feladat kezdeményezi, és értesítést kap. A feladatátvétel figyeléséhez nyissa meg **az eszközök > feladatok.**
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. A **Feladatok** panelen megjelenik egy a forráseszközhöz létrehozott feladatátvételi feladat. Ez a feladat végrehajtja a DR-előellenőrzéseket.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     A VÉSZ-előellenőrzések sikeresek, a feladatátvételi feladat a forráseszközön található megosztások/kötetek visszaállítási feladatait hozza létre.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. A feladatátvétel befejezése után lépjen az **Eszközök** panelre.
    
    1. Jelölje ki, és kattintson a StorSimple-eszköz, amely a feladatátvételi folyamat céleszközeként használt.
    2. Nyissa meg **a Beállítások > a kezelés > megosztások** (vagy **kötetek** iSCSI-kiszolgáló esetén). A **Megosztások** panelen megtekintheti az összes megosztást (kötetet) a régi eszközről.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Létre kell [hoznia egy DNS-aliast,](https://support.microsoft.com/kb/168322) hogy a csatlakozni próbáló összes alkalmazás átirányítható az új eszközre.

## <a name="errors-during-dr"></a>Hibák a vész-és kézbesítés során

**Felhőbeli kapcsolat kimaradás a dr.**

Ha a felhőbeli kapcsolat megszakad a VÉSZ indítása után, és az eszköz visszaállítása befejezése előtt, a VÉSZ-vész-vész-a'ni sikertelen lesz. Hibaüzenetet kap. A DR céleszköze *használhatatlanként van megjelölve.* Nem használhatja ugyanazt a céleszközt a jövőbeli DR-ekhez.

**Nincskompatibilis céleszköz**

Ha a rendelkezésre álló céleszközök nem rendelkeznek elegendő hellyel, hibaüzenet jelenik meg, amely szerint nincsenek kompatibilis céleszközök.

**Előzetes ellenőrzés hibái**

Ha az egyik előcsekk nem teljesül, akkor előre csekkek hibák jelennek meg.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzletmenet-folytonossági vészhelyreállítás (BCDR)

Az üzletmenet-folytonossági vész-helyreállítási (BCDR) forgatókönyv akkor fordul elő, amikor a teljes Azure-adatközpont működése leáll. Ez hatással lehet a StorSimple Eszközkezelő szolgáltatás és a kapcsolódó StorSimple eszközök.

Ha vannak StorSimple-eszközök, amelyek közvetlenül a katasztrófa bekövetkezése előtt regisztráltak, akkor előfordulhat, hogy ezeket a StorSimple-eszközöket törölni kell. A katasztrófa után újra létrehozhatja és konfigurálhatja ezeket az eszközöket.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről a helyi webes felhasználói felület használatával.](storsimple-ova-web-ui-admin.md)

