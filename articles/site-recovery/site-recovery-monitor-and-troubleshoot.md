---
title: Az Azure site recovery figyelése | Microsoft dokumentumok
description: Az Azure Site Recovery replikációs problémáinak és műveleteinek figyelése és elhárítása a portál használatával
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: aa9d776df50306ab1705426c923413b5a5d545a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68717350"
---
# <a name="monitor-site-recovery"></a>A Site Recovery monitorozása

Ebben a cikkben megtudhatja, hogyan figyelheti az Azure [Site Recovery](site-recovery-overview.md), a Site Recovery beépített figyelés használatával.  A következőket figyelheti:

- A Site Recovery által replikált gépek állapota és állapota
- A gépek feladatátvételi állapotának tesztelése.
- A konfigurációt és a replikációt érintő problémák és hibák.
- Infrastruktúra-összetevők, például helyszíni kiszolgálók.


## <a name="before-you-start"></a>Előkészületek

Előfordulhat, hogy a kezdés előtt át szeretné tekinteni a [gyakori figyelési kérdéseket.](monitoring-common-questions.md)

## <a name="monitor-in-the-dashboard"></a>Monitor az irányítópulton

1. A tárolóban kattintson az **Áttekintés gombra.** A Helyreállítási szolgáltatások irányítópultja egyetlen helyen egyesíti a tároló összes figyelési információját. Vannak lapok mind a Site Recovery és az Azure Backup szolgáltatáshoz, és válthat közöttük.

    ![Webhely-helyreállítási irányítópult](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. Az irányítópultról részletezze a különböző területeket. 

    ![Webhely-helyreállítási irányítópult](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. A **Replikált elemek alkalmazásban**kattintson az Összes **megtekintése** elemre a tároló összes kiszolgálójának megtekintéséhez.
4. A részletezéshez kattintson az egyes szakaszok állapotrészleteire.
5. Infrastruktúra **nézetben**rendezze a figyelési adatokat a replikáló gépek típusa szerint.

## <a name="monitor-replicated-items"></a>Replikált elemek figyelése

A **Replikált elemek alkalmazásban**figyelje a tárolóban lévő összes olyan gép állapotát, amelyen engedélyezve van a replikáció.

**Állapot** | **Részletek**
--- | ---
Kifogástalan | A replikáció normálisan halad. A rendszer nem észlel hiba- vagy figyelmeztető tüneteket.
Figyelmeztetés | A rendszer egy vagy több olyan figyelmeztető tünetet észlel, amely hatással lehet a replikációra.
Kritikus | A rendszer egy vagy több kritikus replikációs hibajelenséget észlelt.<br/><br/> Ezek a hibajelenségek általában azt jelzik, hogy a replikáció beragadt, vagy nem halad olyan gyorsan, mint az adatok változási sebessége.
Nem alkalmazható | Azok a kiszolgálók, amelyek várhatóan nem replikálódnak. Ez magában foglalhatja a feladatátvételt leadott gépeket is.

## <a name="monitor-test-failovers"></a>Tesztfeladat-átvételek figyelése

A **feladatátvételi teszt sikeres,** figyelheti a feladatátvételi állapotát a tárolóban lévő gépek.

- Azt javasoljuk, hogy a replikált gépeken legalább hathavonta egyszer futtasson egy tesztfeladat-átvételt. Ez egy módja annak ellenőrzésére, hogy a feladatátvétel a várt módon működik, anélkül, hogy megzavarná az éles környezetben. 
- A tesztfeladat-átvétel csak akkor tekinthető sikeresnek, ha a feladatátvétel és a feladatátvétel utáni karbantartás sikeresen befejeződött.

**Állapot** | **Részletek**
--- | ---
Ajánlott vizsgálat | Azok a gépek, amelyek a védelem engedélyezése óta nem történt tesztfeladat-átvétel.
Sikeresen végrehajtva | Sikeres vagy sikeresebb tesztfeladat-felvevőfeladatokkal rendelkező gépek.
Nem alkalmazható | Gépek, amelyek jelenleg nem jogosultak a teszt feladatátvételre. Például a feladatátvételt megvevő gépek kezdeti replikációs/teszt feladatátvétel/feladatátvétel folyamatban van.

## <a name="monitor-configuration-issues"></a>Konfigurációs problémák figyelése

A **konfigurációs problémák**ban figyelje azolyan problémákat, amelyek hatással lehetnek a sikeres feladatátvételi képességre.

- A konfigurációs problémákat (a szoftverfrissítések rendelkezésre állása kivételével) egy 12 óránként 12 óránként futó rendszeres érvényesítő művelet észleli. A validátorműveletet kényszerítheti az azonnali futtatásra, ha a **Konfigurációs problémák** szakasz fejléce melletti frissítésikonra kattint.
- További részletekért kattintson a hivatkozásokra. Az adott gépeket érintő problémák esetén kattintson a **Szükséges figyelem** a **Célkonfigurációk** oszlopban. A részletek közé tartoznak a javítási javaslatok.

**Állapot** | **Részletek**
--- | ---
Hiányzó konfigurációk | Hiányzik egy szükséges beállítás, például egy helyreállítási hálózat vagy egy erőforráscsoport.
Hiányzó erőforrások | Egy adott erőforrás nem található, vagy nem érhető el az előfizetésben. Például az erőforrást törölték vagy áttelepítették. Figyelt erőforrások közé tartozik a cél erőforráscsoport, cél Virtuálishálózat/alhálózat, log/target storage fiók, cél rendelkezésre állási készlet, cél IP-cím.
Előfizetési kvóta |  A rendelkezésre álló előfizetési erőforrás kvóta egyenlege összehasonlítja a tárolóban lévő összes gép feladatátvételhez szükséges egyenleg.<br/><br/> Ha nincs elegendő erőforrás, a rendszer nem jelenti a kvótaegyenleget.<br/><br/> A kvóták figyelik a virtuális gépek magszámát, a virtuális gépek és a virtuális kártya magszámát, a hálózati kártya (NIC) számát.
Szoftverfrissítések | Az új szoftverfrissítések elérhetősége és a lejáró szoftververziókra vonatkozó információk.


## <a name="monitor-errors"></a>Monitor hibák

A **Hibaösszegzés**ben figyelje a jelenleg aktív hibajelenségeket, amelyek hatással lehetnek a tárolóban lévő kiszolgálók replikációjára, és figyelheti az érintett gépek számát.

- A helyszíni infrastruktúra-összetevőket érintő hibák a szakasz kezdetét jelentik. Például nem kap egy szívverést az Azure Site Recovery Provider a helyszíni konfigurációs kiszolgálón, vagy a Hyper-V állomás.
- Ezután a replikált kiszolgálókat érintő replikációs hibajelenségek jelennek meg.
- A táblabejegyzések rendezése a hiba súlyosságának csökkenő sorrendje, majd az érintett gépek számlálási sorrendje szerint történik.
- Az érintett kiszolgálók száma hasznos módja annak megértéséhez, hogy egy mögöttes probléma hatással lehet-e több gépre. Például egy hálózati hiba potenciálisan hatással lehet az Azure-ba replikáló összes gépre. 
- Egyetlen kiszolgálón több replikációs hiba is előfordulhat. Ebben az esetben minden hibatünet megszámolja, hogy a kiszolgáló szerepel-e az érintett kiszolgálók listájában. A probléma kijavítása után javulnak a replikációs paraméterek, és a hiba törlődik a gépről.

## <a name="monitor-the-infrastructure"></a>Figyelje az infrastruktúrát.

Az **Infrastruktúra nézetben**figyelheti a replikációban részt vevő infrastruktúra-összetevőket, valamint a kiszolgálók és az Azure-szolgáltatások közötti kapcsolat állapotát.

- A zöld vonal azt jelzi, hogy a kapcsolat kifogástalan.
- A becsukott hiba ikonnal ellátott piros vonal egy vagy több olyan hibajelenség meglétét jelzi, amelyek hatással vannak a kapcsolatra.
-  Vigye az egérmutatót a hiba ikonra a hiba és az érintett entitások számának megjelenítéséhez. Kattintson az érintett entitások szűrt listájának ikonjára.

    ![Hely-helyreállítási infrastruktúra nézete (tároló)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>Tippek az infrastruktúra figyeléséhez

- Győződjön meg arról, hogy a helyszíni infrastruktúra-összetevők (konfigurációs kiszolgáló, folyamatkiszolgálók, VMM-kiszolgálók, Hyper-V-állomások, VMware-gépek) a Site Recovery Provider és/vagy az ügynökök legújabb verzióját futtatják.
- Az infrastruktúra nézet összes szolgáltatásának használatához ezen összetevők [22.](https://support.microsoft.com/help/4072852)
- Az infrastruktúra nézet használatához válassza ki a megfelelő replikációs forgatókönyvet a környezetben. További részletekért részletezhet a nézetben. Az alábbi táblázat bemutatja, hogy mely esetek jelennek meg.

    **Forgatókönyv** | **Állapot**  | **Elérhető megtekintés?**
    --- |--- | ---
    **Replikáció a helyszíni helyek között** | Minden állam | Nem 
    **Az Azure virtuális gép replikációja az Azure-régiók között**  | Replikáció engedélyezett/kezdeti replikáció folyamatban | Igen
    **Az Azure virtuális gép replikációja az Azure-régiók között** | Feladatátvétel/feladat-visszavétel | Nem   
    **VMware–Azure replikálás** | Replikáció engedélyezett/kezdeti replikáció folyamatban | Igen     
    **VMware–Azure replikálás** | Feladatátvétel/feladat-visszavétel | Nem      
    **Hyper-V–Azure replikálás** | Feladatátvétel/feladat-visszavétel | Nem

- Egyetlen replikálógép infrastruktúranézetének megtekintéséhez kattintson a tároló **menüreplikált elemek parancsára,** és jelöljön ki egy kiszolgálót.  




## <a name="monitor-recovery-plans"></a>Helyreállítási tervek figyelése

A **Helyreállítási tervek**alkalmazásban figyelje a tervek számát, hozzon létre új terveket, és módosítsa a meglévőket.  

## <a name="monitor-jobs"></a>Feladatok figyelése

A **Feladatok alkalmazásban**figyelje a Hely-helyreállítási műveletek állapotát.

- Az Azure Site Recovery legtöbb művelete aszinkron módon történik, és egy nyomon követési feladat jön létre, és a művelet előrehaladásának nyomon követésére szolgál. 
- A feladatobjektum rendelkezik a művelet állapotának és előrehaladásának nyomon követéséhez szükséges összes információval. 

A feladatok figyelése az alábbiak szerint:

1. Az irányítópult > **feladatok** szakaszban láthatja az elmúlt 24 órában befejezett, folyamatban lévő vagy a bemenetre váró feladatok összegzését. Bármelyik állapotra kattintva további információkat kaphat a megfelelő feladatokról.
2. Kattintson **az Összes megtekintése** gombra az elmúlt 24 óra összes feladatának megtekintéséhez.

    > [!NOTE]
    > A feladatadatokat a **webhely-helyreállítási feladatok**> tároló menüből is elérheti. 

2. A **Hely-helyreállítási feladatok** listában megjelenik a feladatok listája. A felső menüben egy adott feladathiba részleteit kaphatja meg, szűrheti a feladatlistát adott feltételek alapján, és exportálhatja a kiválasztott feladatadatokat az Excelbe.
3. A feladatra kattintva részletezheti a feladatot. 

## <a name="monitor-virtual-machines"></a>Virtuális gépek figyelése

A **Replikált elemek alkalmazásban**lekell szereznie a replikált gépek listáját. 
    ![Hely-helyreállítási replikált elemek listájának nézete](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. Megtekintheti és szűrheti az információkat. A műveletmenü tetején műveleteket hajthat végre egy adott gépen, beleértve a tesztfeladat-átvételt vagy bizonyos hibák megtekintését.
3. Kattintson **az Oszlopok gombra** további oszlopok megjelenítéséhez, például az RPO megjelenítéséhez, a célkonfigurációs problémák és a replikációs hibák megjelenítéséhez.
4. Kattintson **a Szűrő** gombra az információk megtekintéséhez adott paraméterek alapján, például a replikáció állapota vagy egy adott replikációs házirend alapján.
5. Kattintson a jobb gombbal egy gépre a műveletek, például a teszt feladatátvétel kezdeményezéséhez, vagy a hozzá társított konkrét hibarészletek megtekintéséhez.
6. Kattintson a gépre, hogy további részleteket részletezz. Részletek a következők:
   - **Replikációs információ**: A számítógép aktuális állapota és állapota.
   - **RPO** (helyreállítási pont célja): A virtuális gép aktuális RPO-ja és az RPO utolsó kiszámításának időpontja.
   - **Helyreállítási pontok**: A gép legfrissebb rendelkezésre álló helyreállítási pontjai.
   - **Feladatátvételi készenlét**: Azt jelzi, hogy a teszt feladatátvétel futott-e a gépen, a számítógépen futó ügynökverzió (a Mobility szolgáltatást futtató gépekhez) és a konfigurációs problémák.
   - **Hibák**: A számítógépen jelenleg megfigyelt replikációs hibajelenségek listája, valamint a lehetséges okok/műveletek.
   - **Események**: A gépre gyakorolt legutóbbi események időrendi listája. A hiba részletei a jelenleg megfigyelhető hibajelenségeket mutatják, míg az események a gépet érintő problémák előzményrekordja.
   - **Infrastruktúra nézet:** Az infrastruktúra állapotát jeleníti meg a forgatókönyvhöz, amikor a gépek replikálnak az Azure-ba.

     ![A hely-helyreállítási replikált elem részletei/áttekintése](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>Feliratkozás az e-mailértesítésekre

Feliratkozhat az alábbi kritikus eseményekről szóló e-mail-értesítésekre:
 
- A replikált gép kritikus állapota.
- Nincs kapcsolat a helyszíni infrastruktúra-összetevők és a Site Recovery szolgáltatás között. A site recovery és a tárolóban regisztrált helyszíni kiszolgálók közötti kapcsolatot szívveréses mechanizmus sal észleli a rendszer.
- Feladatátvételi hibák.

Iratkozzon fel az alábbiak szerint:

A tároló> **figyelése** csoportban kattintson a **Hely-helyreállítási események**elemre.
1. Kattintson az **E-mail-értesítések** elemre.
1. Az **E-mail értesítés**ben kapcsolja be az értesítéseket, és adja meg, hogy kinek küldjön. Elküldheti az összes előfizetés-rendszergazdának értesítést és opcionálisan konkrét e-mail címeket.

    ![E-mail-értesítések](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>További lépések

[Ismerje meg a](monitor-log-analytics.md) Site Recovery figyelését az Azure Monitorsegítségével.
