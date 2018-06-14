---
title: A StorSimple virtuális tömb vész helyreállítási és eszköz feladatátvételi |} Microsoft Docs
description: További tudnivalókat a feladatátvétel a StorSimple virtuális tömbjét.
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
ms.openlocfilehash: 12079f8dbc409afe5acc274fa08bda878c90b76e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927767"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Katasztrófa utáni helyreállítás és eszköz feladatátvevő a StorSimple virtuális tömb Azure-portálon

## <a name="overview"></a>Áttekintés
Ez a cikk a Microsoft Azure StorSimple virtuális tömb többek között a részletes lépéseket egy másik virtuális tömb átvehetné a vész-helyreállítási ismerteti. A feladatátvétel lehetővé teszi az adatok áthelyezése az egy *forrás* számára az adatközpontban található eszköz egy *cél* eszköz. A céleszközön ugyanabban vagy egy különböző földrajzi helyen található. Az eszköz feladatátvétel során a teljes eszköz. A feladatátvételi felhő változnak az adatok a forrás eszköz tulajdonjogát, valamint a céleszközön.

Ez a cikk csak akkor alkalmazható, a StorSimple virtuális tömbök. Feladatok átadása egy 8000 sorozat-eszközön, keresse fel [eszköz feladatátvételi és katasztrófa-helyreállítás a StorSimple eszköz](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Mi az a vész-helyreállítási és eszköz feladatátvevő?

Egy vész-helyreállítási forgatókönyv esetén az elsődleges eszköz nem működik. Ebben a forgatókönyvben áthelyezheti egy másik eszközre sikertelen eszköz társított felhő adatokat. Az elsődleges eszköz, használhatja a *forrás* , és adja meg egy másik eszköz, a *cél*. Ezt a folyamatot nevezzük a *feladatátvételi*. A feladatátvételi minden kötet vagy a megosztásokat a forráseszközről származó tulajdonjogai módosulnak, és átkerülnek a céleszközt. Az adatok szűrése nem engedélyezett.

A teljes eszköz visszaállítása a tűz térkép-alapú rétegezéséhez használatával, és nyomon követni a vész-Helyreállítási van modellezve. Határozza meg a hőtérkép tűz érték hozzárendelése az adatokat az írási és olvasási kombinációját. A tűz leképezése majd Rétegek a legalacsonyabb tűz adattömbök a felhő először a helyi rétegen a magas tűz (használat gyakorisága) adattömbök megtartásával. StorSimple egy Vészhelyreállítás során a hőtérkép használja a visszaállítás és rehydrate az adatokat a felhőből. Az eszköz lekéri az összes a kötetek vagy megosztások utolsó legutóbbi biztonsági mentés (a belső), és a visszaállítás végez, hogy biztonsági másolatból. A virtuális tömb koordinálja a teljes vész-Helyreállítási folyamatot.

> [!IMPORTANT]
> A forráseszközt eszköz feladatátvételi végén törlődik, és ezért a feladat-visszavétel nem támogatott.
> 
> 

Vész-helyreállítási van összehangolva. az eszköz feladatátvételi szolgáltatás segítségével, és kezdeményezi a **eszközök** panelen. Ezen a panelen a StorSimple eszköz Manager szolgáltatás csatlakoztatott StorSimple eszközök vannak. Az egyes eszközök láthatja, hogy a rövid név, a állapotát, a kiépített és a maximális kapacitás, a típus és a modell.

## <a name="prerequisites-for-device-failover"></a>Eszköz feladatátvételi előfeltételei

### <a name="prerequisites"></a>Előfeltételek

Eszköz feladatátvevő győződjön meg arról, hogy a következő előfeltételek teljesülését:

* A forráseszközt kell lennie egy **inaktív** állapotát.
* A céleszközön kell megjelennek, **már beállíthat** az Azure portálon. Egy virtuális céltömb azonos vagy nagyobb kapacitású kiépítéséhez. A helyi webes felhasználói felület segítségével konfigurálása és regisztrálása sikeres volt a virtuális céltömb.
  
  > [!IMPORTANT]
  > Ne próbálja meg beállítani a regisztrált virtuális eszközt a szolgáltatáson keresztül. Nincs eszközkonfiguráció kell végezni a szolgáltatáson keresztül.
  > 
  > 
* A figyelt eszköz nem lehet a neve megegyezik a forráseszközt.
* A forrás és cél eszköz kell ugyanarra a típusra. Csak átveheti egy másik fájlkiszolgálóhoz fájlkiszolgálóként konfigurált virtuális tömb. Ugyanez érvényes iSCSI-kiszolgáló.
* Fájlkiszolgáló vész-Helyreállítási azt javasoljuk, hogy csatlakozik-e a céleszközt ugyanahhoz a tartományhoz, mint a forrás. Ez a konfiguráció biztosítja, hogy a rendszer automatikusan feloldja a megosztási engedélyeket. Csak a feladatátvételt a ugyanabban a tartományban a céleszközön.
* Az elérhető célkiszolgálók Dr tartoznak eszközökre, amelyeken a ugyanolyan vagy nagyobb kapacitású, a forráseszközt képest. Az eszközöket, amelyek a szolgáltatáshoz való kapcsolódás, de nem a feltételeknek megfelelő terület cél eszközök nem érhetők el.

### <a name="other-considerations"></a>Egyéb szempontok

* A tervezett feladatátvételre 
  
  * Azt javasoljuk, hogy szánjon a kötetek vagy megosztások offline állapotba a forrás eszközön.
  * Azt javasoljuk, hogy az eszköz biztonsági mentés készítése, és folytathatja a feladatátvételi adatveszteség minimálisra. 
* Egy nem tervezett feladatátvétel az eszköz a legutóbbi biztonsági mentés használ az adatok helyreállítását.

### <a name="device-failover-prechecks"></a>Eszköz feladatátvételi prechecks

Mielőtt elkezdi a vész-Helyreállítási, az eszköz prechecks hajt végre. Az ellenőrzések biztosíthatja, hogy nincs hiba fordulhat elő, ha a vész-Helyreállítási megkezdése. A prechecks a következők:

* A tárolási fiók ellenőrzése.
* Az Azure-bA a felhő közötti kapcsolat ellenőrzése.
* A céleszközön rendelkezésre álló lemezterület ellenőrzése.
* Ellenőrzi, hogy rendelkezik-e az iSCSI server eszköz forráskötet
  
  * érvényes ACR neve.
  * érvényes IQN (legfeljebb 220 karakter).
  * érvényes CHAP jelszavak (12-16 karakter).

Ha az előző bármelyikét prechecks sikertelen, a vész-Helyreállítási nem folytatódhat. Ezek a problémák megoldásához, majd próbálkozzon újra a vész-Helyreállítási.

A vész-Helyreállítási sikeres befejezése után a tulajdonjogát, a forrás-eszközökön a felhő adatok átvitele történik meg a céleszközt. A forráseszközt majd nem áll rendelkezésre a portálon. Minden kötet vagy megosztás a forrás eszközön való hozzáférés le van tiltva, és a céleszköz aktívvá válik.

> [!IMPORTANT]
> Ha az eszköz már nem érhető el, a rendszer a gazdagép kiépített virtuális gép továbbra is fogyassza az erőforrásokat. Ha a vész-Helyreállítási sikeresen befejeződött, a gazdarendszer törölheti ezt a virtuális gépet.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Feladatok átadása a virtuális tömb

Javasoljuk, hogy kiépíteni, konfigurálása, és egy másik, a StorSimple virtuális tömb regisztrálása a StorSimple Device Manager szolgáltatásban, mielőtt elkezdi a műveletet.

> [!IMPORTANT]
> 
> * Nem utasíthat el keresztül egy StorSimple 8000 series eszközről 1200-as virtuális eszközre.
> * Átveheti a Federal Information Processing Standard (FIPS) engedélyezve van a virtuális eszköz egy másik FIPS-kompatibilis eszköz vagy a kormányzati portál telepítése a FIPS eszközre.


A következő lépésekkel visszaállíthatja az eszköz célja a StorSimple virtuális eszköz.

1. Felkészítse és konfigurálja a céleszközön, amely megfelel a [eszköz feladatátvételi Előfeltételek](#prerequisites). Az eszköz konfigurálása a helyi webes felhasználói felületen keresztül, és regisztrálja a StorSimple eszköz Manager szolgáltatáshoz. Ha hoz létre egy fájlkiszolgálón, folytassa a 1. lépésében [fájlkiszolgálóként beállítása](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Ha az iSCSI-kiszolgáló létrehozása, folytassa a 1. lépésében [állítsa be az iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Kötetek vagy megosztások offline igénybe a gazdagépen. A kötetek vagy megosztások offline állapotba, tekintse meg a gazdagép operációsrendszer-specifikus utasításokat. Ha nem már offline, meg kell tennie minden a kötetek vagy megosztások offline állapotba az eszközön az alábbi lépésekkel.
   
    1. Lépjen **eszközök** panelen válassza ki az eszközt.
   
    2. Ugrás a **beállítások > kezelése > megosztások** (vagy **beállítások > kezelése > kötetek**). 
   
    3. Jelöljön ki egy megosztást/kötetet, kattintson a jobb gombbal, és válassza ki **offline állapotba**. 
   
    4. Megerősítést kér, hogy nem **tudomásul veszem, hogy a megosztás offline állapotba helyezése hatását.** 
   
    5. Kattintson a **offline állapotba**.

3. A StorSimple eszköz Manager szolgáltatást, lépjen **felügyeleti > eszközök**. Az a **eszközök** panelen jelölje ki, majd kattintson a forrás eszköz.

4. Az a **eszköz irányítópult** panelen kattintson a **Deactivate**.

5. Az a **Deactivate** panelen megerősítést kér. Eszköz inaktiválása egy *állandó* folyamat, amely nem vonható vissza. Is figyelmezteti, a megosztások vagy kötetek offline érvénybe a gazdagépen. Írja be az eszköz nevét, és kattintson a **Deactivate**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Elindul az inaktiválást. Az inaktiválást sikeres végrehajtása után egy értesítést fog kapni.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Az eszközök lapon az eszköz állapotát most módosul **inaktív**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Az a **eszközök** panelen jelölje ki, majd kattintson a deaktivált forráseszközt feladatátvételhez. 
9. Az a **eszköz irányítópult** panelen kattintson a **feladatátvételt**. 
10. Az a **eszköz feladatátvételt** panelen tegye a következőket:
    
    1. A forrás eszköz mező automatikusan feltöltődik értékkel. Vegye figyelembe a forráseszközt teljes adattároló mérete. Az adattároló mérete lehet kisebb, mint a rendelkezésre álló kapacitásból a céleszközön. Tekintse át a forrás eszköz, például eszköz neve, a teljes kapacitás és a feladatátvétel történt a megosztások nevei társított.

    2. A legördülő listából a rendelkezésre álló eszközök, válassza ki a **céleszközön**. Csak azokat az eszközöket, amely rendelkezik elegendő kapacitással a legördülő listában jelennek meg.

    3. Ellenőrizze, hogy **tudomásul veszem, hogy ez a művelet sikertelen lesz-e keresztül a céleszközt adatokat**. 

    4. Kattintson a **feladatátvételt**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. A feladatátvételi feladatban elindul, és értesíti. Nyissa meg a **eszközök > feladatok** figyelése a feladatátvételt.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Az a **feladatok** panelen megjelenik a feladatátvételi feladatban a forrás rendszerhez létrehozott. Ez a feladat a vész-Helyreállítási prechecks végzi.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Követően a vész-Helyreállítási prechecks sikeres, a feladatátvételi feladatban fogja nem tudott származtatni visszaállítási feladat minden megosztás/kötet létezik-e az adatforrás-eszközön.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Miután a feladatátvétel befejeződött, lépjen a **eszközök** panelen.
    
    1. Válassza ki, és válassza ki a céleszközt, a feladatátvételi folyamat volt megadva a StorSimple eszközt.
    2. Nyissa meg a **beállítások > felügyeleti > megosztások** (vagy **kötetek** Ha iSCSI-kiszolgálókkal). Az a **megosztások** panelen megtekintheti a régi eszközről minden megosztás (kötetek).
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Szüksége lesz a [DNS-alias létrehozása](https://support.microsoft.com/kb/168322) , hogy csatlakozni alkalmazásokat is átirányítja az új eszköz.

## <a name="errors-during-dr"></a>A Vészhelyreállítás során hibák

**Felhő kapcsolat kimaradás során vész-Helyreállítási**

Ha a felhő kapcsolat megszakad, miután vész-Helyreállítási megkezdődött, és az eszközön visszaállítás befejeződött, a vész-Helyreállítási sikertelen lesz. Failore értesítést kaphat. A céleszközt Dr jelölésű *használható.* A cél szoftverdefiníciók jövőbeli drs nem használható.

**Nem kompatibilis a Céleszközök számára**

Ha az elérhető célkiszolgálók eszközök nem rendelkeznek elegendő lemezterülettel, a célból, hogy vannak-e nem kompatibilis a Céleszközök hibaüzenet jelenik meg.

**Precheck hibák**

Ha a prechecks valamelyik nem teljesül, majd megjelenik precheck hibák.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzleti folytonosság vészhelyreállítási (BCDR)

Üzleti folytonossági (BCDR) vészhelyreállítás akkor fordul elő, ha a teljes Azure-adatközpontban leáll a működése. Ez befolyásolhatja a StorSimple eszköz Manager szolgáltatás és a kapcsolódó StorSimple eszközökhöz.

Ha nincsenek StorSimple volt regisztrált eszközök előtt történt egy olyan vészhelyzet esetén, majd a StorSimple eszközökhöz is szükség lehet. A katasztrófa utáni hozza létre újra, és konfigurálja az eszközöket.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a [felügyelete a StorSimple virtuális tömb, a helyi webes felhasználói felületen](storsimple-ova-web-ui-admin.md).

