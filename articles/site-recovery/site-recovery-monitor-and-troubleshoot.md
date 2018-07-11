---
title: Figyelése és hibaelhárítása az Azure Site Recovery |} A Microsoft Docs
description: Figyelése és hibaelhárítása az Azure Site Recovery replikációs problémákat és a műveletek a portál használatával
services: site-recovery
documentationcenter: ''
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 84b5bf3be09083a69216802fc7f557de1a7f0ee6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917533"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Figyelése és hibaelhárítása az Azure Site Recovery

Ebből a cikkből elsajátíthatja, hogyan használhatja az Azure Site Recovery monitorozási és hibaelhárítási beépített figyelési szolgáltatásokat. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> - Az Azure Site Recovery irányítópulttal (tároló Áttekintés lapján)
> - Monitorozás és hibaelhárítás replikációjával kapcsolatos problémák
> - A figyelő az Azure Site Recovery-feladatok/műveletek
> - Előfizetés az e-mail értesítésekre

## <a name="using-the-azure-site-recovery-dashboard"></a>Az Azure Site Recovery irányítópulttal

A tároló Áttekintés lapján az Azure Site Recovery irányítópultot egyetlen helyen, a tároló összes figyelési információkat összesíti. Indítsa el a tároló irányítópultjának és mélyebben további részletekért lépjen az irányítópult részei között. Az Azure Site Recovery irányítópult azon fontosabb részei a következők:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Váltás az Azure Backup és az Azure Site Recovery irányítópultok

Az Áttekintés oldal tetején a váltógomb lehetővé teszi a Site Recovery az irányítópultokon és a biztonsági mentés közötti váltáshoz. A választott ki, egyszer, megjegyezze őket, és a tároló áttekintés oldalán a következő megnyitásakor az alapértelmezett értékeket használ. Válassza a Site Recovery a Site Recovery-irányítópult megtekintéséhez. 

Az Azure Site Recovery irányítópult-oldalon különböző részeivel automatikusan frissítse 10 percenként, úgy, hogy az irányítópult tükrözi a legfrissebb elérhető információkat.

![Az Azure Site Recovery – Áttekintés lapon figyelési szolgáltatásokat](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikált elemek

A replikált elemek szakasz az irányítópult áttekintést a védett kiszolgáló replikációs állapotát a tárolóban. 

<table>
<tr>
    <td>Kifogástalan</td>
    <td>A replikáció folyik általában ezek a kiszolgálók és a hiba vagy figyelmeztetés tünetek észlelt.</td>
</tr>
<tr>
    <td>Figyelmeztetés </td>
    <td>Ezek a kiszolgálók számára, amely hatással van a replikáció, vagy azt jelzik, hogy a replikáció nem halad előre általában egy vagy több figyelmeztetést a jelenség észlelhető.</td>
</tr>
<tr>
    <td>Kritikus</td>
    <td>Egy vagy több kritikus fontosságú replikációs hiba tünetek kiszolgálókon észlelt. Ezek hiba történt a tünetek jellemzően mutatók, hogy a replikáció elakadt vagy vagy gyors adatváltozási sebesség kiszolgálókon nem halad előre.</td>
</tr>
<tr>
    <td>Nem alkalmazható</td>
    <td>Az kiszolgáló jelenleg nem várt a replikál, például a kiszolgálók, amelyek feladatátadása megtörtént.</td>
</tr>
</table>

Replikáció állapota alapján szűrt védett kiszolgálók listájának megtekintéséhez kattintson a replikációs állapot leírása a fánkdiagram mellett. A nézet az összes hivatkozás a szakasz címét közel egy parancsikont a replikált elem a tároló oldalán. A nézet az összes hivatkozás a tárolóban lévő összes kiszolgálók listájának megtekintéséhez használja.

### <a name="3-failover-test-success"></a>3. A feladatátvételi teszt sikerült

A feladatátvételi teszt sikeres szakasz az irányítópult megadja a virtuális gépek felosztása a tárolóban, a feladatátvételi teszt állapota alapján. 

<table>
<tr>
    <td>A teszt elvégzése ajánlott</td>
    <td>Virtuális gépek nem volt sikeres feladatátvételi teszt óta, elérte a védett állapotban.</td>
</tr>
<tr>
    <td>Sikeresen végbement</td>
    <td>Virtuális gépek, amelyek egy vagy több sikeres tesztek kellett volna.</td>
</tr>
<tr>
    <td>Nem alkalmazható</td>
    <td>Virtuális gépek, amelyek nem egy feladatátvételi tesztet feltételeinek jelenleg megfelelő. Példa: kiszolgálók feladatátvétele, mely kezdeti replikációs kiszolgálók van folyamatban, a kiszolgálók, amelyhez a feladatátvétel van folyamatban, a kiszolgálók számára, amely egy feladatátvételi tesztet már folyamatban van.</td>
</tr>
</table>

Kattintson a feladatátvételi teszt állapota mellett a fánkdiagram, így a védett kiszolgálókon a feladatátvételi teszt állapota alapján listájának megtekintéséhez.
 
> [!IMPORTANT]
> Ajánlott eljárásként javasoljuk, hogy egy feladatátvételi tesztet hajt végre a védett kiszolgálók legalább hat havonta. Feladatátvételi teszt végrehajtásához egy zavart nem okozó módja a kiszolgálók és a egy elkülönített környezet alkalmazások feladatátvételének teszteléséhez, és segít kiértékelni az üzleti folytonossági Felkészültségi.

 Egy teszt feladatátvételi műveletet a kiszolgálón vagy a helyreállítási terv sikeres számít, csak azután is a teszt feladatátvételi műveletet, és a tisztítás teszt feladatátvételi művelet sikeresen befejeződött.

### <a name="4-configuration-issues"></a>4. Konfigurációs problémák

A konfigurációs problémák szakaszban látható hibáit, amely hatással lehet a képes sikeresen a feladatátvételi virtuális gépekre. Az osztályok, a jelen szakaszban felsorolt problémák vannak:
 - **Hiányzik a konfiguráció:** védett kiszolgálók hiányzik a szükséges beállításokat, például a helyreállítási hálózat vagy egy helyreállítási erőforráscsoportot.
 - **Hiányzó erőforrás:** konfigurált cél/helyreállítási erőforrás nem található vagy nem érhető el az előfizetésben. Például az erőforrást törölték, vagy migrálták egy másik előfizetést vagy az erőforráscsoportot. A következő cél/helyreállítási konfigurációk kell figyelni a rendelkezésre állási: céloldali erőforráscsoportot, a cél virtuális hálózatot és alhálózatot, a naplózási/céloldali tárfiók, cél rendelkezésre állási csoport, a cél IP-címe.
 - **Előfizetési kvóta:** erőforrás kvóta érhető el előfizetés egyenlege az egyenleg átvehet szükséges a rendszer összehasonlítja a tárolóban lévő összes virtuális gépet. Ha a rendelkezésre álló egyenleg megfelelő található, a rendszer kvóta nem elegendő egyenleg jelenti. A következő Azure-erőforrások kvóták figyelt: virtuálisgép-magok száma, a virtuálisgép-családba tartozó magok száma, a hálózati adapterek (NIC) kártya száma.
 - **Szoftverfrissítések:** szoftververziók lejár az új szoftverfrissítések rendelkezésre állását.

Konfigurációs problémák (nem a rendelkezésre állás szoftverfrissítési), a rendszer egy rendszeres érvényesítő művelet, amely alapértelmezés szerint 12 óránként fut észleli. Az érvényesítő művelet azonnal futtatni a frissítés ikonra kattintva kényszerítheti a *konfigurációs problémák* szakaszfejléc.

További részletes információkat a felsorolt problémák és az azok által érintett virtuális gépek hivatkozásokra kattintva. Az adott virtuális gépek érintő problémákat, akkor további részletekért kattintson a **figyelmet** a céloszlop konfigurációk a virtuális gép alatti hivatkozásra. Hogyan javíthatja az észlelt problémákat javaslattal szerepel.

### <a name="5-error-summary"></a>5. Souhrn chyb

A hiba összefoglaló szakaszban látható a jelenleg aktív replikációs hiba jelenségeket, amely hatással lehet a tárolóban, minden egyes hiba miatt érintett entitások számával együtt-kiszolgálók replikálása.

A replikációs hiba jelenségeket a kiszolgálók replikációs kritikus vagy figyelmeztetési állapotban látható a hiba összefoglaló táblázatot. 

- A helyszíni infrastruktúra összetevők, például az Azure Site Recovery Providert a helyszíni konfigurációs kiszolgálót, VMM-kiszolgáló vagy Hyper-V-gazdagépen futó szívverést fogadását-érintő hibák jelennek meg a hiba összefoglaló elején a szakasz
- Replikációs hiba tünetek negatív hatással a védett kiszolgálók mellett látható. A hiba összefoglaló táblázatot bejegyzések a hiba súlyossága sorrendbe, majd által érintett kiszolgálók száma sorrendbe vannak rendezve.
 

> [!NOTE]
> 
>  Egyetlen kiszolgáló több replikációs hiba probléma lehet figyelni. Ha egy kiszolgálón több hiba történt a jelenség minden egyes Hiba tünet számolja a kiszolgáló, az érintett kiszolgálók listájában. Miután egy Hiba tünet eredményez a mögöttes probléma megoldódott, replikáció paramétereinek javítása, és a hibát a virtuális gép törlődik.
>
> > [!TIP]
> Érintett kiszolgálók száma hasznos módja a megértéséhez, ha egyetlen mögöttes problémára negatív hatással lehet több kiszolgálót. Például egy hálózati hiba okozhatta potenciálisan hatással lehet egy helyszíni helyről az Azure-ba történő összes kiszolgálójára. Ez a nézet gyorsan ruház, kijavítása, hogy egy alapul szolgáló probléma kijavítja az több kiszolgáló replikációját.
>

### <a name="6-infrastructure-view"></a>6. Infrastruktúra-nézet

Az infrastruktúranézet forgatókönyv szerinti ábrázolása az infrastrukturális összetevők kapnak szerepet replikációs biztosít. Vizuálisan is a különböző kiszolgálók között, és a kiszolgálók és a replikációs részt az Azure-szolgáltatások közötti kapcsolat állapotát mutatja be. 

A zöld sor azt jelzi, hogy kapcsolat kifogástalan állapotú, míg a piros vonalat az átfedett hibajelző ikon azt jelzi, hogy egy vagy több hiba jelenségeket, mely negatív hatással a következő összetevők kapnak szerepet közötti kapcsolat meglétét. Viszi az egérmutatót a sor a hiba ikon látható, a hiba- és az érintett entitások száma. 

Hiba ikonra kattintva a hibákat az érintett entitások szűrt listája látható.

![Site Recovery infrastruktúra-nézet (tár)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Győződjön meg arról, hogy a helyszíni infrastruktúra-összetevőket (konfigurációs kiszolgáló és további folyamatkiszolgálók, VMware virtuális gépek, a Hyper-V-gazdagépek, a VMM-kiszolgálók replikálása) az Azure Site Recovery szoftver legújabb verzióját futtatja. Futnia kell, hogy az infrastruktúra-nézet összes funkciójának használatához, [22-es kumulatív frissítés](https://support.microsoft.com/help/4072852) vagy az Azure Site Recovery később

Az infrastruktúra-nézet, jelölje be a megfelelő replikációs forgatókönyv (Azure-beli virtuális gépek, VMware virtuális gépek/fizikai kiszolgáló vagy Hyper-V) forrás környezettől függően. Az infrastruktúra-nézet jelenik meg a tároló Áttekintés lapján az egy összesített nézet a tárolóhoz. Áthatoló le további, az egyes összetevők a mezők kattintva.

Az infrastruktúra nézetet egy replikáló gépen kontextusában a replikált elem – Áttekintés lapon érhető el. A replikáló kiszolgáló – áttekintés oldalra lépni, nyissa meg a tároló menüjében replikált elemek, és válassza ki a kiszolgálót a részleteinek a megtekintéséhez.

### <a name="infrastructure-view---faq"></a>Infrastruktúranézet – gyakori kérdések

**K.** Miért nem jelenik meg az infrastruktúra-nézet a virtuális gépem? </br>
**V.** Az infrastruktúra megtekintése funkció csak az Azure-bA replikáló virtuális gépek számára érhető el. A funkció jelenleg nem érhető el a helyszíni helyek között replikáló virtuális gépek számára.

**K.** Miért jelenik meg a tároló infrastruktúra nézetben teljes száma a virtuális gépek száma a replikált elemek fánkdiagram?</br>
**V.** A tároló infrastruktúranézet replikációs forgatókönyvek szerint hatókörét. Részt vesz a jelenleg kiválasztott replikációs forgatókönyv csak virtuális gépek száma, az infrastruktúra nézetben látható a virtuális gépek szerepelnek. Emellett a választott forgatókönyv csak az olyan virtuális gépek, amelyeken jelenleg az Azure-bA replikálni az tartalmaznak az infrastruktúra nézetben látható a virtuális gépek száma (fő példa: replikáló virtuális gépek biztonsági virtuális gépeket a feladatátvétel egy helyszíni helyhez nem szerepelnek az infrastruktúranézet.)

**K.** Miért nem látható a teljes száma a fánkdiagramot az irányítópulton látható replikált elemek áttekintése lapon a essentials navigációs menüben található replikált elemek száma?</br>
**V.** Csak azon virtuális gépek mely kezdeti replikációja befejeződött az tartalmaznak az essentials navigációs menüben megjelenő száma. Az összes replikált elemek fánkdiagram tartalmazza az összes virtuális gép a tárolóban, beleértve a kiszolgálókat, amelyhez a kezdeti replikáció folyamatban van.

**K.** Melyik replikációs forgatókönyvek érhető el az infrastruktúra-nézet? </br>
**V.**
>[!div class="mx-tdBreakAll"]
>|Replikációs forgatókönyv  | Virtuális gép állapota  | Infrastruktúranézet érhető el  |
>|---------|---------|---------|
>|A virtuális gépek replikálnak között két helyszíni hely     | -        | Nem      |
>|Összes     | Feladatátvétel         |  Nem       |
>|Két Azure-régió között replikáló virtuális gépek     | Kezdeti replikálás folyamatban vagy védett         | Igen         |
>|VMware virtuális gépek replikálása Azure-bA     | Kezdeti replikálás folyamatban vagy védett        | Igen        |
>|VMware virtuális gépek replikálása Azure-bA     | Vissza egy helyszíni VMware-helyről a replikált virtuális gépek feladatátvétele         | Nem        |
>|Hyper-V virtuális gépek replikálása Azure-bA     | Kezdeti replikálás folyamatban vagy védett        | Igen       |
>|Hyper-V virtuális gépek replikálása Azure-bA     | Feladatátvétel és feladat-visszavétel folyamatban        |  Nem       |


### <a name="7-recovery-plans"></a>7. Helyreállítási tervek

A helyreállítási tervek szakaszban jeleníti meg. a helyreállítási terv a tárolóban. Kattintson a számra helyreállítási tervek listája, új helyreállítási terveket hozhat létre, vagy szerkesztheti a meglévőket. 

### <a name="8-jobs"></a>8. Feladatok

Az Azure Site Recovery-feladatok az Azure Site Recovery-műveletek állapotának nyomon követése. Az Azure Site Recoveryben legtöbb művelet aszinkron módon használják, hogy a művelet előrehaladását úgy követheti nyomon követési feladatok hajtja végre.  Egy művelet állapotának figyelésével kapcsolatban lásd: a [figyelése az Azure Site Recovery feladatok /-műveletek](#monitor-azure-site-recovery-jobsoperations) szakaszban.

A feladatok területén az irányítópult a következő információkat biztosítja:

<table>
<tr>
    <td>Meghiúsult</td>
    <td>Nem sikerült az Azure Site Recovery-feladatok az elmúlt 24 órában</td>
</tr>
<tr>
    <td>Folyamatban</td>
    <td>Jelenleg folyamatban van az Azure Site Recovery-feladatok</td>
</tr>
<tr>
    <td>Várakozás bevitelre</td>
    <td>Az Azure Site Recovery-feladatok Várakozás a felhasználói adatbevitel jelenleg szünetel.</td>
</tr>
</table>

A nézet az összes hivatkozás szakasz fejléc melletti egy parancsikont a feladatok listája lap megnyitásához.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorozás és hibaelhárítás replikációjával kapcsolatos problémák

A tár irányítópult-oldalon elérhető információk mellett további részletek és a virtuálisgép-lista oldalára, és a virtuális gép részletei lapon között szereplő hibaelhárítási információkat kaphat. Megtekintheti a védett virtuális gépek listáját a tárolóban kiválasztásával a **replikált elemek** beállítást a tároló menüjében. Azt is megteheti érheti el a védett elemek szűrt listája a rendelkezésre álló tár irányítópult-oldalon a hatókörbe tartozó parancsikonok kattintva.

![Site Recovery által replikált elemek listanézet](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

Szűrje a replikált elemek listáját tartalmazó lapon segítségével különféle szűrőket, például a replikációs állapotot, és a replikációs szabályzatot. 

Az oszlop választó beállítás lehetővé teszi további oszlopokat, például a helyreállítási Időkorlát, a célként megadott konfigurációs problémák és a replikációs hibák jelennek meg. Egy virtuális gép vagy gépek listáját az adott sorban kattintson a jobb gombbal a virtuális gép érintő hibák megtekintése a műveletek is kezdeményezhető.

További részletes elemzést, a kattintással válasszon ki egy virtuális gépet. Ekkor megnyílik a virtuális gép részletei lapot. A virtuális gép részletei az Áttekintés oldal tartalmaz egy irányítópultot, a gép kapcsolatos további információk találhatók. 

A replikáló gép – Áttekintés lapon találja:
- Helyreállítási Időkorlát (helyreállításipont-célkitűzés): a virtuális gép és az idő, amikor az rpo-t legutóbb kiszámított aktuális helyreállítási Időkorlát.
- A gép a legutóbbi elérhető helyreállítási pontok
- Ha bármely, amely hatással lehet a gép feladatátvételre való készültsége konfigurációs problémákat. Kattintson a hivatkozásra a további részletekért.
- A hiba részletei: replikációs hiba tünetek listája jelenleg figyelhető meg a gépen, valamint a lehetséges okok és a javasolt szervizelés
- Események: Egy negatív hatással a gép a legutóbbi események időrendi listája. A hiba részletei az jelenleg megfigyelhető hiba jelenségeket szerepelnek a gépen, események egy korábbi rekordot, amely hatással lehetett a gép, beleértve a hiba a jelenség, hogy korábban már megfigyelhet a gép különféle események.
- Az Azure-bA replikáló gépek infrastruktúra-nézet

![Site Recovery által replikált elem részleteinek/áttekintése](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

A művelet menü az oldal tetején lévő biztosít beállítások csatlakozva különféle műveleteket végezhet, mint például a virtuális gép feladatátvételi tesztje. A hiba részletei gombra a művelet menüben replikációs hibák, konfigurációs problémák és a virtuális gép konfigurációs ajánlott eljárások alapján figyelmeztetések többek között az összes jelenleg aktív hiba teszi lehetővé.

> [!TIP]
> Miben különbözik RPO vagy a helyreállítási időkorlát az utolsó elérhető helyreállítási pontot?
> 
>Az Azure Site Recovery egy többlépéses aszinkron eljárás használatával virtuális gépek replikálása az Azure-bA. Az utolsó előtti lépésben replikációs metaadatok együtt a virtuális gép a legutóbbi módosítások átkerülnek a napló-illetve gyorsítótárfiók. Ezeket a módosításokat, és a egy helyreállítható pontok azonosításához a címke a célként megadott régióban lévő tárfiókhoz lett írva, miután az Azure Site Recovery egy helyreállítható pontot a virtuális gép létrehozásához szükséges információkat tartalmaz. Az rpo-t ezen a ponton a módosítások a tárfiók eddigi feltöltött feltétele teljesült. Más szóval az rpo-t a virtuális gép ezen a ponton az idő, mértékegységben egyenlő eltelt idő a megfelelő a helyreállítható pontra történő küldés időbélyegzője legyen.
>
>Az Azure Site Recovery szolgáltatással, a háttérben működő választja ki a feltöltött adatok, a storage-fiókból, és alkalmazza őket a virtuális gép létrehozása a replika lemezekre. Ezután létrehoz egy helyreállítási pontot, és elérhetővé teszi a pont helyreállítási feladatátvétel során. A legújabb elérhető helyreállítási pont történő küldés időbélyegzője legyen a legutóbbi helyreállítási pontot, amely már feldolgozta és a replika lemezek alkalmazott megfelelő jelzi.
>> [!WARNING]
> Egy magokon óra vagy helytelen rendszeridejével a replikáló forrásgép, vagy a helyszíni infrastruktúra-kiszolgálók a számított RPO-érték lesz döntés. Annak érdekében, RPO pontos jelentés értékek ellenőrizze, hogy a rendszeróra, a replikáció részt vevő kiszolgálók pontos. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>A figyelő az Azure Site Recovery-feladatok/műveletek

Az Azure Site Recovery végrehajtja a műveleteket aszinkron módon adja meg. Néhány példa a műveletek hajthatók végre engedélyezze a replikációt, hozzon létre helyreállítási tervet, tesztelheti a feladatátvételeket, frissíteni a replikációs beállításokat stb. Minden ilyen művelet van egy megfelelő feladat nyomon követése és naplózása a művelet számára létrehozott. A feladat objektumnak az állapotot és a műveletnek az előrehaladását nyomon követéséhez szükséges összes szükséges információt. A különböző a Site Recovery-műveletek a tároló állapota a feladatok lapján követheti nyomon. 

Nyissa meg a tárolót a Site Recovery-feladatok listájának megtekintéséhez a **figyelés és jelentéskészítés** szakaszában a tároló menüjében, és válassza a feladatok > Site Recovery-feladatok. Válassza ki a feladatot a feladatok lapon kattintson rá a további részleteket tudhat meg a megadott feladat. Ha egy feladat nem sikerült, vagy hibát, megtekintheti további információkat a hiba és a lehetséges javítási (is elérhető a feladatok lista oldalára, kattintson a jobb gombbal a a sikertelen feladat részletei lap tetején, a hiba részletei gombra kattintva a feladat.) Szűrje a feladatok lista lap felső részén látható műveleti menü használatával szűrje a listát a megadott feltételek alapján, és az Exportálás gomb segítségével exportálhatja a kiválasztott Excel-feladatok részleteit. A feladatok lista nézetben is elérhető az irányítópult-oldalon a Site Recovery parancsikonra. 

 A műveletek, amelyeket elvégezhet az Azure Portalról létrehozott feladatot és annak aktuális állapota is követhetők az értesítések szakaszban (a harang ikonra a jobb felső sarokban) az Azure Portal.

## <a name="subscribe-to-email-notifications"></a>Előfizetés az e-mail értesítésekre

A beépített e-mail-értesítési szolgáltatás lehetővé teszi a kritikus eseményekre vonatkozó e-mail-értesítések feliratkozhat. Ha feliratkozott, e-mail-értesítések lesznek küldve a következő események:
- Replikáció állapota kritikusra gyorsítótárazhatók replikáló gépek.
- Nincs kapcsolat a helyszíni infrastruktúra-összetevőket és az Azure Site Recovery szolgáltatás között. A Site Recovery szolgáltatás a helyszíni infrastrukturális összetevők, például a konfigurációs kiszolgáló (VMware) vagy a System Center Virtual Machine Manager(Hyper-V) a tárolóban regisztrált összmennyiségét kapcsolatot észlel a szívverés mechanizmus segítségével.
- Feladatátvételi művelet sikertelen, ha van ilyen.

Fizessen elő az Azure Site Recovery email értesítéseket kapni, nyissa meg a **figyelés és jelentéskészítés** szakaszában a tároló menüjében és:
1. Válassza ki a riasztások és események > Site Recovery-események.
2. Az események lapról megnyitott felül a menüből válassza ki a "E-mail-értesítések".
3. Az e-mail-értesítés varázsló használja, és e-mail-értesítések bekapcsolása, és adja meg az értesítések címzettjeit. Előfordulhat, hogy adja meg, hogy minden előfizetés-Adminisztrátorok kell küldeni az értesítéseket, és/vagy szeretne értesítéseket küldeni az e-mail-címek listájának megadása. 
