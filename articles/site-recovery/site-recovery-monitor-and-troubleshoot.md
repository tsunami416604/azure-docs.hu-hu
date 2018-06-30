---
title: Figyelése és hibaelhárítása az Azure Site Recovery |} Microsoft Docs
description: Figyelésére és hibaelhárítására az Azure Site Recovery replikálási problémák megoldásához és a műveletek a portál használatával
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
ms.date: 02/22/2018
ms.author: bsiva
ms.openlocfilehash: 9a979cc940e2133e3dff3bb7dd4c5f2ec53fea7a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109501"
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Figyelés és hibaelhárítás céljából az Azure Site Recovery

Ebből a cikkből megismerheti, hogyan használható az Azure Site Recovery beépített felügyeleti funkciói figyelés és hibaelhárítás céljából. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> - Az Azure Site Recovery irányítópultot (tároló áttekintése lapon)
> - Figyelheti és replikációjával kapcsolatos problémák elhárítása
> - Figyelő az Azure Site Recovery feladatok/műveletek
> - Előfizetés az e-mail értesítésekre

## <a name="using-the-azure-site-recovery-dashboard"></a>Az Azure Site Recovery irányítópulttal

Az Azure Site Recovery irányítópult tároló Áttekintés lap összesíti a tároló egyetlen helyen összes figyelési adatot. Indítsa el a tároló irányítópult és a további információkhoz juthat az irányítópult részei a mélyebben elmerülhet az adatkészletekben. Az Azure Site Recovery irányítópult azon fontosabb részei a következők:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Azure biztonsági mentési és az Azure Site Recovery közötti váltáshoz irányítópultok

Az Áttekintés oldal tetején a váltógomb kapcsoló lehetővé teszi a Site Recovery irányítópult-lapok és a biztonsági mentés közötti váltáshoz. Ez a választás, létrehozásukat követően már megjegyzett és választotta az alapértelmezésnek megfelelően, a tároló a áttekintése lapon a következő megnyitásakor az. A Site Recovery beállítással láthatja a Site Recovery az irányítópultot. 

Az Azure Site Recovery irányítópult-oldalon különböző részeit frissíti 10 percenként automatikusan, úgy, hogy az irányítópult tükrözi a legfrissebb elérhető információkat.

![Figyelési funkciókat az az Azure Site Recovery – áttekintés oldalra](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Replikált elemek

A replikált elemek szakasz az irányítópult áttekintést nyújt a védett kiszolgáló replikációs állapotát a tárolóban lévő állapottal. 

<table>
<tr>
    <td>Kifogástalan</td>
    <td>Replikációs általában halad ezeken a kiszolgálókon és a hiba vagy figyelmeztetés jelenségek észlelt.</td>
</tr>
<tr>
    <td>Figyelmeztetés </td>
    <td>Egy vagy több figyelmeztetés tünetként jelentkezik, amely befolyásolja a replikálást, vagy jelezheti, hogy a replikáció általában nem halad már telepítve van ezen kiszolgálók számára.</td>
</tr>
<tr>
    <td>Kritikus</td>
    <td>Legalább egy kritikus fontosságú replikációs hiba jelenségek ezekre a kiszolgálókra vonatkozóan nem észlelhetők. Hiba jelenségek jellemzően mutatók, hogy a replikáció vagy Beragadt, vagy nem halad gyors az adatok adatváltozási sebessége ezen kiszolgálók számára.</td>
</tr>
<tr>
    <td>Nem alkalmazható</td>
    <td>Az kiszolgáló jelenleg való replikálása, például a kiszolgálók, amelyek nem tudták keresztül nem várt.</td>
</tr>
</table>

Replikáció állapota alapján szűrt védett kiszolgálók listájának megtekintéséhez kattintson a replikációs állapot leírása mellett a fánk. A nézet az összes hivatkozás a címben közelében egy a tároló a replikált elemeket tartalmazó lap parancsikonja. Használja az összes hivatkozás a tárolóban lévő kiszolgálók listájának megtekintése.

### <a name="3-failover-test-success"></a>3. Feladatátvételi teszt sikeres

A feladatátvételi teszt sikeres szakasz az irányítópult a virtuális gépek felosztása a tárolóban, a teszt feladatátvételi állapot alapján mutatja be. 

<table>
<tr>
    <td>Ajánlott tesztelési</td>
    <td>Egy sikeres feladatátvételi nem volt a óta virtuális gépek akkor érhető el a védett állapotban.</td>
</tr>
<tr>
    <td>Sikeresen végbement</td>
    <td>Virtuális gépek, amelyek a rendelkezésére állt-e legalább egy sikeres feladatátvételi teszteket.</td>
</tr>
<tr>
    <td>Nem alkalmazható</td>
    <td>Virtuális gépek, amelyek nincsenek jelenleg támogatható a feladatátvételi tesztet. Példa: kiszolgálók a feladatátvételt, mely kezdeti replikációs kiszolgálók van folyamatban, a kiszolgálók, amelynek folyamatban van a feladatátvétel, a kiszolgálók, amely a feladatátvételi teszt már folyamatban van.</td>
</tr>
</table>

A teszt feladatátvételi állapot mellett a fánk gombra annak megállapításához, a teszt feladatátvételi állapotuk szerint védett kiszolgálók listáját.
 
> [!IMPORTANT]
> Ajánlott eljárásként javasoljuk, hogy Ön egy feladatátvételi teszt végrehajtása a védett kiszolgálókon legalább hat havonta. Feladatátvételi teszt végrehajtása a kiszolgálók és alkalmazások egy elkülönített környezet feladatátvételi teszt nem zavaró elvégezhető, és segítségével kiértékelheti a üzleti folytonossági felkészültség.

 A teszt feladatátvételi művelet kiszolgálón vagy a helyreállítási terv sikeres tekinthető, csak miután mind a teszt feladatátvételi művelet, és a teszt feladatátvételi törlésének művelete sikeresen befejeződött.

### <a name="4-configuration-issues"></a>4. Konfigurációs problémák

A konfigurációs problémák szakaszt, amely hatással lehet arra, hogy sikeresen megtörtént a feladatátvétel virtuális gépek problémák listáját jeleníti meg. A jelen szakaszban felsorolt problémák vannak:
 - **Hiányzó konfigurációk:** védett kiszolgálók hiányzik a szükséges beállítások, például egy helyreállítási hálózathoz vagy egy helyreállítási erőforráscsoportot.
 - **Hiányzó erőforrások:** konfigurálni target vagy helyreállítási erőforrás nem található vagy nem érhető el az előfizetéshez. Például az erőforrás törölve lett vagy át lett telepítve egy másik előfizetéshez vagy erőforráscsoporthoz. A következő cél vagy helyreállítási konfigurációk kell figyelni a rendelkezésre állási: célerőforrás-csoport, a cél virtuális hálózati és alhálózati, napló/cél tárfiók, célként megadott rendelkezésre állási csoportot, a cél IP-címet.
 - **Előfizetési kvóta:** a rendelkezésre álló előfizetés erőforrás kvótájának egyenleg átvehet szükséges egyensúlyt a rendszer összehasonlítja a tárolóban lévő összes virtuális gépet. Ha a rendelkezésre álló egyenleg elegendő található, a kvóta nem elegendő egyenleg igen. A következő Azure-erőforrások kvótái figyelt: virtuális gép magok száma, a virtuális gép termékcsalád magok száma, a hálózati illesztő adapterek (NIC) száma.
 - **Szoftverfrissítések:** új szoftverfrissítések lejáró szoftver verziója rendelkezésre állását.

Konfigurációs problémák (eltérő rendelkezésre állási szoftverfrissítések), a rendszer alapértelmezés szerint 12 óránként futó rendszeres érvényesítő művelet észleli. Beállíthatja, hogy az érvényesítő művelet mellett a frissítési ikonjára kattintva azonnal futtatni a *konfigurációs problémák* szakaszfejléc.

További részletes információkat a felsorolt problémákat és azok által érintett virtuális gépek hivatkozásokra kattintva. Az adott virtuális gépek érintő problémák, kaphat további részletekért kattintson a **figyelmet** alatt a céloszlop konfigurációk a virtuális géphez. Ajánlások hogyan javítani tudja a észlelt hibákat tartalmazza.

### <a name="5-error-summary"></a>5. Hibáinak összegzése

A hiba összefoglaló szakaszban a jelenleg aktív replikációs hiba jelenségeket, amely hatással lehet a kiszolgálót a tárolóban, és az érintett entitások minden egyes hibaállapot miatt darabszámot replikációs jeleníti meg.

A kiszolgálók replikációs kritikus vagy figyelmeztetési állapotban a replikációs hiba tünetként jelentkezik a hiba összefoglaló táblázat látható. 

- A helyszíni infrastruktúra összetevők, például az Azure Site Recovery Provider a helyszíni konfigurációs kiszolgáló, a VMM-kiszolgáló vagy a Hyper-V gazdagépen futó szívverést fogadását-érintő hibák jelennek meg a hiba összefoglaló kezdetén a szakasz
- Replikációs hiba jelenségek érintő védett kiszolgálók mellett látható. Hiba összefoglaló táblázat bejegyzéseket vannak rendezve, csökken a hiba a súlyosság, majd sorrendjét az érintett kiszolgálók számának csökkentésével.
 

> [!NOTE]
> 
>  Egy kiszolgálón több replikációs hiba probléma lehet figyelni. Ha egy kiszolgálón több hiba probléma minden egyes hiba oka az, hogy az érintett kiszolgálók listáját a kiszolgáló számolja. Amennyiben sikerült javítani a hibát okozó problémát, ami azt eredményezi, a hiba oka az, replikáció paraméterei javítása és a hiba törlődik a virtuális gépről.
>
> > [!TIP]
> Az érintett kiszolgálók számának hasznos módja a megértése, ha egy alapul szolgáló probléma lehet, hogy mely negatív hatással lehet több kiszolgálót. Például egy hálózati probléma merült potenciálisan hatással lehet minden kiszolgálók replikálása egy helyszíni hely Azure-bA. Ez a nézet gyorsan közvetíti, hogy meghatározásáról, hogy egy alapul szolgáló problémát kijavítja az több kiszolgáló replikációját.
>

### <a name="6-infrastructure-view"></a>6. Infrastruktúra nézet

Az infrastruktúra nézete forgatókönyv bölcs vizuális ábrázolását részt replikációs infrastrukturális összetevőket. Vizuálisan is a a különböző kiszolgálók között, és a kiszolgálók és az Azure-szolgáltatások részt replikációs közötti kapcsolat állapotát mutatja be. 

A zöld sor azt jelzi, hogy kapcsolat kifogástalan, amíg egy piros sor az átfedett hiba ikonnal jelzi, hogy egy vagy több hiba jelenségeket, mely negatív hatással az érintett összetevők közötti kapcsolat meglétét. Az egérmutató hiba ikon fölé a sor látható, a hiba és az érintett entitások. 

A hiba az ikonra kattintva a hibák az érintett entitások szűrt listáját tartalmazza.

![Webhely helyreállítási infrastruktúra nézet (tároló)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Győződjön meg arról, hogy a helyszíni infrastruktúra összetevőinek (a konfigurációs kiszolgálón további folyamat kiszolgálók VMware virtuális gépeket, a Hyper-V-gazdagépek, a VMM-kiszolgáló replikálása) fut az Azure Site Recovery szoftver legújabb verzióját. Fogja tudni használni a infrastruktúra nézet összes funkcióját, futnia kell [22-es kumulatív frissítés](https://support.microsoft.com/help/4072852) vagy az Azure Site Recovery később

Az infrastruktúra nézet, jelölje be a megfelelő replikációs forgatókönyv (az Azure virtuális gépek, VMware virtuális gépek/fizikai kiszolgáló vagy Hyper-V) attól függően, hogy a forrás környezetében. Az infrastruktúra nézet jelenik meg a tároló áttekintése lapon a tároló összesített nézete. Megtekintheti a mezőkbe kattintva be az egyes összetevők lefelé további.

A környezet egyetlen replikáló gépek egy infrastruktúra nézetet a replikált elemek áttekintése oldalon érhető el. Nyissa meg a replikációs kiszolgáló – áttekintés oldalra, használja a tárolóban menüből replikált elemek, és válassza ki a kiszolgálót, a részletek megtekintéséhez.

### <a name="infrastructure-view---faq"></a>Infrastruktúra nézet – gyakori kérdések

**K.** Miért nem jelenik meg az infrastruktúra nézet a virtuális géphez? </br>
**V.** Az infrastruktúra nézet funkció csak érhető el virtuális gépek Azure-bA replikál. A szolgáltatás jelenleg nem érhető el virtuális gépek számára a helyszíni helyek között replikálja.

**K.** Miért megjelennek a tároló infrastruktúra nézetben a számuk eltérő virtuális gépek száma a replikált elemek fánk?</br>
**V.** A tároló-infrastruktúra nézet által a replikáció eseteire hatókörét. Részt vesz a jelenleg kijelölt replikációs környezet csak virtuális gépek szerepelnek a infrastruktúra nézetben látható a virtuális gépek száma. Emellett a választott forgatókönyv, csak az olyan virtuális gépek replikálása Azure-bA jelenleg konfigurált szerepelni fog az infrastruktúra nézetben látható a virtuális gépek száma (fő példa: replikáló virtuális gépek biztonsági virtuális gépek a feladatátvételt egy helyszíni hely nem szerepelnek az infrastruktúra nézetben.)

**K.** Miért van az essentials-fiókot a fánk a diagramon az irányítópulton látható replikált elemek száma eltér az áttekintése lapon látható replikált elemek száma?</br>
**V.** Csak azon virtuális gépek melyik kezdeti replikáció befejeződik szerepelni fog a count látható módon az essentials-fiókot. A teljes replikált elemek fánk tartalmazza az összes virtuális gép a tároló, beleértve a kiszolgálókat, amelyhez a kezdeti replikáció folyamatban van.

**K.** Mely replikáció eseteire érhető el az infrastruktúra nézet? </br>
**V.**
>[!div class="mx-tdBreakAll"]
>|Replikációs forgatókönyv  | Virtuális gép állapota  | Infrastruktúra nézet elérhető  |
>|---------|---------|---------|
>|Virtuális gépek replikálása között két helyszíni hely     | -        | Nem      |
>|Összes     | A feladatátvételt         |  Nem       |
>|Két Azure-régiók közötti replikáló virtuális gépek     | Kezdeti replikálás folyamatban lévő vagy a védett         | Igen         |
>|VMware virtuális gépek replikálása Azure-bA     | Kezdeti replikálás folyamatban lévő vagy a védett        | Igen        |
>|VMware virtuális gépek replikálása Azure-bA     | Helyszíni VMware-hely a replikált virtuális gépek a feladatátvételt         | Nem        |
>|Hyper-V virtuális gépek replikálása Azure-bA     | Kezdeti replikálás folyamatban lévő vagy a védett        | Igen       |
>|Hyper-V virtuális gépek replikálása Azure-bA     | A feladatátvételt / folyamatban lévő feladat-visszavétel        |  Nem       |


### <a name="7-recovery-plans"></a>7. A helyreállítási terv

A helyreállítási tervek szakasz jeleníti meg. a helyreállítási terv a tárolóban lévő állapottal. Kattintson a helyreállítási tervek listája látható, hozzon létre új helyreállítási tervek, vagy módosítsa a létezőket. 

### <a name="8-jobs"></a>8. Feladatok

Az Azure Site Recovery-feladatok az Azure Site Recovery-műveleteket állapotának nyomon követését. Az Azure Site Recovery a műveleteik használják, hogy a művelet előrehaladását úgy követheti nyomon követési feladat aszinkron módon hajtja végre.  Egy művelet állapotának figyeléséről további tudnivalókért lásd: a [figyelő Azure helyreállítási feladatok/Helyműveletek](#monitor-azure-site-recovery-jobsoperations) szakasz.

Ezen az irányítópulton feladatok szakasza a következő információkat biztosítja:

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
    <td>Vár</td>
    <td>Az Azure Site Recovery-feladatok jelenleg szüneteltetve Várakozás egy felhasználói beavatkozást.</td>
</tr>
</table>

A nézet az összes hivatkozás mellett a szakaszfejléc, a feladatok lista lapra mutató hivatkozás.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Figyelheti és replikációjával kapcsolatos problémák elhárítása

A tároló irányítópult-oldalon érhető el információ mellett kaphat a további részletek és a hibaelhárítási információk a virtuális gépek listája lapon és a virtuális gép részletei lapon. Megtekintheti a védett virtuális gépek listájának a tárolóban lévő kiválasztásával a **replikált elemek** lehetőséget a tároló menüből. Alternatív megoldásként is elérheti a védett elemek szűrt listáját tároló irányítópult-oldalon érhető el a hatókörbe tartozó parancsikonok bármelyikét kattintva.

![A Site Recovery replikált elemek listája nézet](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

A szűrő lehetőséget, a replikált elemek lista lap lehetővé teszi különböző szűrőket, például a replikációs állapot és a replikációs házirend alkalmazását. 

A column selector beállítás lehetővé teszi, hogy további oszlopokat, például a helyreállítási Időkorlát, a célként megadott konfigurációs problémák és a replikációs hibák jelennek meg. A virtuális gép vagy gépek listáját az adott sorban kattintson a jobb gombbal a virtuális gép érintő nézet hibák műveletek is kezdeményezhető.

A költségrészletekig további, válassza ki azt a virtuális gép. Ekkor megnyílik a virtuális gép részletei lapon. Virtuális gép összes adatát – Áttekintés lap tartalmaz egy irányítópultot, ahol megtalálja a géphez webszolgáltatásokkal kapcsolatos további információk. 

A replikáló gépek – Áttekintés lapon találhat:
- Helyreállítási Időkorlát (helyreállításipont-célkitűzés): a virtuális gép és az idő, ahol a helyreállítási Időkorlát utolsó kiszámított aktuális RPO.
- A gép a legutóbbi elérhető helyreállítási pontok
- Ha bármely, amely hatással lehet a gép a feladatátvételt készültségi konfigurációs problémák. Kattintson a hivatkozásra kattintva további információkhoz juthat.
- Hiba legutolsó részletes adatai: jelenleg lehetséges okok és a számítógépen észlelt és szervizelt ajánlott Replikációs hiba jelenségek listája
- Események: Időrendi listáját a gép érintő legutóbbi események. Hiba legutolsó részletes adatai a jelenleg megfigyelhető hiba tüneteket a számítógép jeleníti meg, amíg a események olyan korábbi bejegyzések, előfordulhat, hogy rendelkezik a géphez, beleértve a hiba a jelenség, hogy korábban már észrevehető, a gép érintett történt különféle események.
- A gépek replikálása Azure-bA infrastruktúra nézet

![A Site Recovery replikált elemek részletei/áttekintése](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

A művelet menü a lap tetején biztosít a beállítások a különféle műveletek elvégzésére, mint a virtuális gép feladatátvételi teszt. A hiba részletei gombra a művelet menüben lehetővé teszi az összes jelenleg aktív hiba, beleértve a replikációs hibák, a konfigurációs problémák és a konfigurációs gyakorlati alapú figyelmeztetéseket a virtuális gép.

> [!TIP]
> Miben különbözik a helyreállítási Időkorlát vagy a helyreállítási időkorlát a legújabb elérhető helyreállítási pont?
> 
>Az Azure Site Recovery egy többlépéses aszinkron eljárás használatával virtuális gépek replikálása Azure-bA. Az utolsó előtti replikációs lépésben metaadatok együtt a virtuális gép legutóbbi módosítások átkerülnek a napló/gyorsítótár storage-fiók. Ha ezeket a módosításokat, és a helyreállítható pontok azonosításához a címke a tárfiók a cél-régióban van írva, Azure Site Recovery rendelkezik egy helyreállítható pontot a virtuális gép létrehozásához szükséges információkat. Ezen a ponton a helyreállítási Időkorlát teljesülnek a módosítások eddigi feltöltése a tárfiókba. Ez azt jelenti, ezen a ponton a virtuális géphez a helyreállítási Időkorlát kifejezett idő, megegyezik a timestamp, a helyreállítható pont megfelelő az eltelt idő.
>
>Az Azure Site Recovery szolgáltatásban, a háttérben működő választja ki a tárfiók a feltöltött adatokat, és alkalmazza őket a replika lemezekre létre a virtuális géphez. Ezután létrehoz egy helyreállítási pontot, és elérhetővé teszi ezt a pontot helyreállítási feladatátvétel. A legújabb elérhető helyreállítási pont azt jelzi, hogy a legújabb helyreállítási pont már feldolgozása és a replika lemezek alkalmazott megfelelő időbélyegzőjéhez viszonyítva.
>> [!WARNING]
> Egy kihasználtságot óra vagy a kiszolgálón a replikálás alatt forrásgép, vagy a helyszíni infrastruktúra-kiszolgálók helytelen rendszeridő fog döntés a számított RPO-érték. Annak érdekében, hogy a helyreállítási Időkorlát pontos jelentés értékek gondoskodjon arról, hogy a rendszer órája replikációs részt vevő kiszolgálók pontos. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Figyelő az Azure Site Recovery feladatok/műveletek

Az Azure Site Recovery végrehajtja a műveleteket aszinkron módon adja meg. Műveletek hajthatók végre többek között engedélyezze a replikálást, a helyreállítási terv létrehozása, a feladatátvételi teszt, a stb. replikációs beállításainak frissítése. Minden ilyen művelet rendelkezik a megfelelő feladat, amely nyomon követése és naplózása a művelet jön létre. A feladat objektumnak állapotát és a műveletnek az előrehaladását nyomon követéséhez szükséges összes szükséges információt. A feladatok lapján követheti nyomon a különböző Site Recovery-műveleteket a tároló állapotát. 

Nyissa meg a tároló Site Recovery-feladatok listájának megtekintéséhez a **figyelés és jelentéskészítés** tároló és a select feladatok szakasz > Site Recovery-feladatok. Válassza ki a feladatot a feladatok lapon kattintva további információkhoz juthat a megadott feladathoz. Ha egy feladat még nem fejeződött be sikeresen, vagy egy hiba tapasztalható, akkor is további információ a hiba, és a lehetséges javítási felső részén (is elérhető a feladatok lista lapján kattintson a jobb gombbal a sikertelen a feladat részleteit megjelenítő oldalon a hiba részletei gombra kattintva a feladat.) A megadott feltételek alapján lista szűréséhez használja a szűrje a feladatok lista lap tetején a művelet menüben, és az Exportálás gomb segítségével exportálása Excel-a kijelölt feladatok részleteit. Emellett a feladatok lista nézet a Site Recovery irányítópult-oldalon található parancsikonnal. 

 Az Azure-portálról végrehajtott műveletek a létrehozott feladat és aktuális állapota is követhetők az Azure-portál értesítések részéből (a harang ikonra a felső sarokban).

## <a name="subscribe-to-email-notifications"></a>Előfizetés az e-mail értesítésekre

Az a beépített e-mail értesítési szolgáltatás lehetővé teszi előfizetni a kritikus eseményekre vonatkozó e-mailt kérhet. Ha az előfizetett, e-mail értesítések küldését a következő események:
- A replikáló gépek terhelése a kritikus replikációs állapotát.
- A helyszíni infrastruktúra-összetevőihez és az Azure Site Recovery szolgáltatás közötti kapcsolat. A Site Recovery szolgáltatás a helyszíni infrastruktúra összetevői, például a konfigurációs kiszolgáló (VMware) vagy a System Center Virtual Machine Manager(Hyper-V) regisztrálva kapcsolatot észlelt a szívverés mechanizmus segítségével.
- Feladatátvételi művelet sikertelen, ha van ilyen.

E-mailt kérhet az Azure Site Recovery előfizetni, nyissa meg a **figyelés és jelentéskészítés** a tároló menü részét és:
1. Válassza ki a riasztások és események > Site Recovery események.
2. Válassza ki a "Értesítő e-mailek" lehetőséget a menüből felett az események lapról, amely meg van nyitva.
3. A varázslóval e-mail értesítési kapcsolhatja be és ki értesítő e-mailek, és adja meg az értesítések címzettjeit. Előfordulhat, hogy adja meg, hogy az összes előfizetés rendszergazdái elküldeni értesítéseket, és/vagy az értesítések küldéséhez e-mail címek listájának megadása. 
