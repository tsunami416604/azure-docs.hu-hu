---
title: Az Azure Site Recovery monitorozása |} A Microsoft Docs
description: Figyelése és hibaelhárítása az Azure Site Recovery replikációs problémákat és a műveletek a portál használatával
author: bsiva
manager: abhemra
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 07/19/2018
ms.author: bsiva
ms.openlocfilehash: dc089e29889b12a5a6d3fcb17328cfc13fe8d0c9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211808"
---
# <a name="monitor-and-troubleshoot-site-recovery"></a>Monitorozás és hibaelhárítás a Site Recovery

Ebből a cikkből elsajátíthatja, hogyan használhatja az Azure Site Recovery monitorozási és hibaelhárítási beépített figyelési szolgáltatásokat. 

## <a name="use-the-dashboard"></a>Az irányítópult használata

1. Kattintson a tárolóban **áttekintése** a Site Recovery irányítópultjának megnyitásához. A Site Recovery és a biztonsági mentés az irányítópultokon, és azok között válthat.

    ![Site Recovery-irányítópult](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2.  Az irányítópult egyetlen helyen, a tároló összes figyelési információkat összesíti. Az irányítópultról részletezhet különböző területekre. 

    ![Site Recovery-irányítópult](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. A **replikált elemek**, kattintson a **nézet összes** megtekintéséhez a tároló összes kiszolgálón.
4. Az állapotadatok az egyes szakaszokban kattintva részletes elemzést. A **infrastruktúranézet**, figyelési információkat rendezheti a típusú gépeket replikál.

## <a name="monitor-replicated-items"></a>Replikált elemek figyelése

A replikált elemek szakasz bemutatja az összes gépek, amelyeken engedélyezve van a tárolóban a replikáció állapotát.

**állapot** | **Részletek**
--- | ---
Kifogástalan | Replikáció a szokásos módon folyik. Nincs hiba vagy figyelmeztetés a jelenség a rendszer észleli.
Figyelmeztetés | Egy vagy több figyelmeztetés jelenségeket, ami hatással lehet a replikáció a rendszer észleli.
Kritikus | Egy vagy több kritikus fontosságú replikációs hiba tünetek észlelt.<br/><br/> Ezek hiba történt a tünetek jellemzően mutatók, hogy a replikáció elakadt, vagy nem lassabban haladt az adatváltozási sebessége.
Nem alkalmazható | Jelenleg nem várt lehet replikálni a kiszolgálók. Ez tartalmazhat gépek feladatátadása megtörtént.

## <a name="monitor-test-failovers"></a>A figyelő tesztek

Megtekintheti a gépeket a feladatátvételi teszt állapota a tárolóban.

- Azt javasoljuk, hogy egy feladatátvételi tesztet a replikált gépek legalább hat havonta. Olyan módon, ellenőrizze, hogy a feladatátvétel a várt módon működik az éles környezetben megszakítása nélkül. 
- Feladatátvételi teszt sikeres számít, csak a feladatátvétel és a feladatátvétel utáni tisztítás sikeres befejeződése után.

**állapot** | **Részletek**
--- | ---
A teszt elvégzése ajánlott | Feladatátvételi teszt nem volt, mert a védelem engedélyezése gépek.
Sikeresen végbement | A gépek vagy több sikeres feladatátvételi teszteket.
Nem alkalmazható | A gépeket, amelyek jelenleg jogosultak a feladatátvételi teszt nem. Gépek, melyek feladatai át, például, kezdeti replikációs és tesztelési feladatátvétel/feladatátvételi vannak folyamatban.

## <a name="monitor-configuration-issues"></a>A figyelő konfigurációs problémák

A **konfigurációs problémák** szakasz azt mutatja be, amely hatással lehet a lehetővé teszi a sikeres feladatátvételt hibáit.

- Egy rendszeres érvényesítő művelet, amely alapértelmezés szerint 12 óránként fut észleli (kivéve a software update rendelkezésre állás), a konfigurációs problémákat. Az érvényesítő művelet azonnal futtatni a frissítés ikonra kattintva kényszerítheti a **konfigurációs problémák** szakaszfejléc.
- További részletekért hivatkozásokra kattintva. Adott gépek érintő problémákat, kattintson a **figyelmet** a a **konfigurációk cél** oszlop. Szervizelési javaslatokat tartalmazza.

**állapot** | **Részletek**
--- | ---
Hiányzó konfigurációk | Hiányzik a szükséges beállítás, például a helyreállítási hálózat vagy egy erőforráscsoportot.
Hiányzó erőforrások | A megadott erőforrás nem található vagy nem érhető el az előfizetésben. Ha például az erőforrás lett törölték vagy áttelepítették. Figyelt erőforrások tartalmazza a céloldali erőforráscsoport, a cél virtuális hálózat/alhálózat, naplózási/céloldali tárfiók, cél rendelkezésre állási csoport, cél IP-címe.
Előfizetési kvóta |  A rendelkezésre álló előfizetés erőforrás kvóta egyenleg a rendszer összehasonlítja az egyenleg szükséges összes gépet a tároló feladatátvételét.<br/><br/> Ha nincs elegendő erőforrása, a kvóta nem elegendő egyenleg jelentett.<br/><br/> Kvóták Virtuálisgép-magok száma, a figyelt virtuális gép családba tartozó magok száma, a hálózati adapterek (NIC) kártya száma.
Szoftverfrissítések | A rendelkezésre állási új szoftverfrissítéseket, és a lejáró szoftververziók kapcsolatos információkat.


## <a name="monitoring-errors"></a>Ellenőrzési hibák 
A **souhrn Chyb** szakasz azt mutatja be, amely hatással lehet a tárolóhoz, és az érintett gépek száma a kiszolgálók replikálása jelenleg aktív hiba tüneteket.

- A szakasz elején a helyszíni infrastruktúra összetevőit érintő hibák jelennek meg. Ha például nem átvételét ki az Azure Site Recovery Providert a helyszíni konfigurációs kiszolgálót, VMM-kiszolgáló vagy Hyper-V-gazdagépen futó.
- Ezután replikációs hiba tünetek érintő replikált kiszolgálók jelennek meg.
- A táblabejegyzéseket az a hiba súlyossága sorrendbe, majd az érintett gépek sorrendbe száma szerint vannak rendezve.
- Az érintett kiszolgálók száma hasznos módja a megismeréséhez, hogy egyetlen mögöttes problémára negatív hatással lehet több gép. Például egy hálózati hiba okozhatta potenciálisan ronthatja az összes, az Azure-bA replikáló gépek. 
- Egyetlen kiszolgáló több replikációs hiba fordulhat elő. Ebben az esetben minden egyes Hiba tünet száma a kiszolgáló, az érintett kiszolgálók listájában. A probléma elhárítása után a replikáció paraméterei javítása, és a hiba törlődik a számítógépről.

## <a name="monitor-the-infrastructure"></a>Az infrastruktúra felügyeletéhez.

A **infrastruktúranézet** érintett látható az infrastruktúra-összetevőket a replikáció és a kiszolgálók és az Azure-szolgáltatások közötti kapcsolat állapotát.

- Zöld vonal jelzi, hogy a kapcsolat állapota kifogástalan.
- A piros vonalat verlaid hiba ikon azt jelzi, hogy létezik-e egy vagy több hiba történt a jelenség, hogy hatással lehet az összekapcsolhatóságra.
-  Vigye az egérmutatót a hibajelző ikon megjelenítése a hibát, és az érintett entitások száma. Kattintson az érintett entitások szűrt listája.

    ![Site Recovery infrastruktúra-nézet (tár)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

## <a name="tips-for-monitoring-the-infrastructure"></a>Tippek az infrastruktúra figyelése

- Győződjön meg arról, hogy a helyszíni infrastrukturális összetevők (konfigurációs kiszolgáló, folyamatkiszolgáló, VMM-kiszolgálók, Hyper-V-gazdagépek, VMware-alapú gépek) futnak-e a Site Recovery Provider és/vagy az ügynökök legfrissebb verzióit.
- Az infrastruktúra-nézet az összes szolgáltatás használatához meg kell futnia [22-es kumulatív frissítés](https://support.microsoft.com/help/4072852) ezeket az összetevőket.
- Az infrastruktúranézet használatához válassza ki a megfelelő replikációs forgatókönyv a környezetben. A nézet a további részleteket is részletezi. Az alábbi táblázat bemutatja, milyen forgatókönyvekre vannak megadva.

    **Forgatókönyv** | **állapot**  | **A nézet elérhető?**
    --- |--- | ---
    **A helyszíni helyek közötti replikáció** | Minden állapot | Nem 
    **Az Azure virtuális gép replikációja az Azure-régiók között**  | Replikáció engedélyezése és kezdeti replikáció folyamatban | Igen
    **Az Azure virtuális gép replikációja az Azure-régiók között** | Feladatátvétel / feladat-visszavételt | Nem   
    **VMware – Azure replikálás** | Replikáció engedélyezése és kezdeti replikáció folyamatban | Igen     
    **VMware – Azure replikálás** | Nem sikerült a biztonsági over/nem sikerült | Nem      
    **Hyper-V-replikáció az Azure-bA** | Nem sikerült a biztonsági over/nem sikerült | Nem

- Az infrastruktúra-nézet egyetlen replikáló gép, a tároló menüjében kattintson **replikált elemek**, és válassza ki a kiszolgálót.  

### <a name="common-questions"></a>Gyakori kérdések


**Miért jelenik meg a tároló infrastruktúra nézetben teljes száma a virtuális gépek száma a replikált elemek?**

A tároló infrastruktúranézet replikációs forgatókönyvek szerint hatókörét. A jelenleg kiválasztott replikációs forgatókönyv csak gépek szerepelnek a száma, a nézet. Ezenkívül hogy csak akkor számít virtuális gépek replikálása az Azure-bA a konfigurált. Gépek feladatátvételt, vagy egy helyszíni hely replikáló gépek nem számítanak a nézetben.

**Miért nem látható a teljes száma az irányítópulton a replikált elemek Essentials navigációs replikált elemek száma?**

Csak számítógépek, mely kezdeti replikációja befejeződött az tartalmaznak az Essentials navigációs menüben megjelenő száma. Az összes a replikált elemek összes gép tartalmazza a tárolóban, beleértve azokat, amelyhez a kezdeti replikáció folyamatban van.


## <a name="monitor-recovery-plans"></a>A figyelő a helyreállítási tervek

Az a **helyreállítási tervek szakasz** tekintse át a csomagok számát, új terveket hozhat létre, és módosíthatja a meglévőket.  

## <a name="monitor-jobs"></a>Feladatok figyelése

A **feladatok** szakasz a Site Recovery-műveletek állapotát mutatja.

- A legtöbb műveletek az Azure Site Recovery aszinkron módon jönnek létre, és a művelet előrehaladását nyomon követésére használt követési feladatok hajtja végre. 
- A feladat objektum állapotát és a műveletnek az előrehaladását nyomon kell minden adattal rendelkezik. 

Feladatok figyelése a következőképpen:

1. Az irányítópult > **feladatok** szakaszban látható, amely befejeződött, a folyamatban lévő vagy a bemeneti, Várakozás az elmúlt 24 órában feladatok összefoglalása. Kattintson a olyan állapotokat a megfelelő feladatokkal kapcsolatos további információért.
2. Kattintson a **az összes megtekintése** az összes feladat megtekintéséhez az elmúlt 24 órában.

    > [!NOTE]
    > A tároló menüjében is elérheti feladatinformációkat > **Site Recovery-feladatok**. 

2. Az a **Site Recovery-feladatok** listában feladatok listája jelenik meg. A felső menüben, megjelenik a hibaüzenet részleteiben talál egy adott feladatok szűrése a megadott feltételek alapján feladatok listája, és a kijelölt feladat részleteinek Excelbe történő exportálása.
3. Egy feladat részletezhető ehhez kattintson rá. 

## <a name="monitor-virtual-machines"></a>Virtuális gépek figyelése

Emellett az irányítópult a gépek a virtuális gépek lapján követheti nyomon. 

1. Kattintson a tárolóban **replikált elemek** replikált gépek listáját.  Azt is megteheti érheti el a védett elemek szűrt listája az irányítópult-oldalon a hatókörbe tartozó parancsikonok kattintva.

    ![Site Recovery által replikált elemek listanézet](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Az a **replikált elemek** lapon megtekintheti és szűrheti az adatokat. A művelet menü tetején, az elvégezhető műveletek egy adott gép, beleértve a feladatátvételi teszt futtatása, vagy hibaüzenetek megtekintéséhez.
3.  Kattintson a **oszlopok** további oszlopokkal, RPO megjeleníthető például megjeleníthető a cél konfigurációs problémákat, és a replikációs hibákat.
4. Kattintson a **szűrő** meghatározott paraméterek, például a replikációs állapotot, vagy egy adott replikációs házirend alapján információk megtekintéséhez.
5. Kattintson a jobb gombbal egy gép kezdeményezni, például a feladatátvételi teszt műveletek vagy társítva konkrét hiba részleteinek megtekintéséhez.
6. Kattintson a további részletek feltárásához, egy gép. Részletei a következők:
      - **Replikálási adatok**: aktuális állapotát és a gép állapotát.
      - **Helyreállítási Időkorlát** (helyreállításipont-célkitűzés): a virtuális gép és az idő, amikor az rpo-t legutóbb kiszámított aktuális helyreállítási Időkorlát.
      - **Helyreállítási pontok**: a gép a legutóbbi elérhető helyreállítási pontok.
      - **Feladatátvételre való készültsége**: azt jelzi-e egy feladatátvételi tesztet futtatott a gép, az a gép (a mobilitási szolgáltatást futtató gépek) számára, és a konfigurációs problémákat futó ügynök verzióját.
      - **Hibák**: replikációs hiba tünetek jelenleg figyelhető meg a gép, és a lehetséges okok/műveletek listája.
      - **Események**: negatív hatással a gép a legutóbbi események időrendi listáját. Hiba részletei az jelenleg megfigyelhető hiba jelenségeket jeleníti meg, a gép érintő problémák Előzményrekord események pedig.
      - **Infrastruktúranézet**: a forgatókönyv az infrastruktúra állapotát az látható, amikor az Azure-bA replikáló gépek.

    ![Site Recovery által replikált elem részleteinek/áttekintése](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)


### <a name="common-questions"></a>Gyakori kérdések

**Miben különbözik RPO az utolsó elérhető helyreállítási pontot?**


- A Site Recovery egy több lépésből álló aszinkron folyamat használja a gépek replikálása az Azure-bA.
- Az utolsó előtti replikációs lépésben a gép metaadatait, valamint a legutóbbi módosítások átkerülnek a napló-illetve gyorsítótárfiók.
- Ezeket a módosításokat a címke egy helyreállítható pontot azonosító kerüljenek a célként megadott régióban lévő tárfiókhoz.
-  A Site Recovery mostantól létrehozhat egy helyreállítható pontot a virtuális gép.
- Az rpo-t ezen a ponton a módosítások a tárfiók eddigi feltöltött feltétele teljesült. Más szóval a gép RPO ezen a ponton egyenlő eltelt idő a megfelelő a helyreállítható pontra történő küldés időbélyegzője legyen.
- A Site Recovery most választja ki a feltöltött adatok, a storage-fiókból, és alkalmazza azt a replika lemezek, a gép létrehozott.
- Ezután a Site Recovery létrehoz egy helyreállítási pontot, és elérhetővé teszi a pont helyreállítási feladatátvétel során. Így a legújabb elérhető helyreállítási pont azt jelzi, hogy a legutóbbi helyreállítási pontot, amely már feldolgozta és a replika lemezek alkalmazott megfelelő történő küldés időbélyegzője legyen.

> [!NOTE]
> Egy helytelen a replikáló forrásgépen, vagy a helyszíni infrastruktúra-kiszolgálók fog időeltérési a számított RPO-érték. Pontos RPO jelentéskészítéshez, győződjön meg arról, hogy a rendszeróra pontos minden kiszolgálók és gépek. 

## <a name="subscribe-to-email-notifications"></a>Előfizetés az e-mail értesítésekre

Iratkozhat fel ezeket az eseményeket kritikus email értesítéseket kapni:
 
- A replikált gép állapota kritikus.
- Nincs kapcsolat a helyszíni infrastruktúra-összetevőket és a Site Recovery szolgáltatás között. Regisztrálva az adott tárolóban, a Site Recovery és a helyszíni kiszolgálók közötti kapcsolatot észlel a szívverés mechanizmus segítségével.
- Feladatátvételi hibák.

A következőképpen iratkozhat fel:

A tárolóban > **figyelés és jelentéskészítés** területén kattintson **Site Recovery-események**.
2. Kattintson a **E-mail-értesítések**.
3. A **e-mailes értesítés**, bekapcsolja az értesítéseket, és adja meg, akik küldéséhez. Minden előfizetés-Adminisztrátorok küldeni az értesítéseket, és igény szerint adott e-mail-címeket is elküldheti.

    ![E-mail-értesítések](./media/site-recovery-monitor-and-troubleshoot/email.png)
