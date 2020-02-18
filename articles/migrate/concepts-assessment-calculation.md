---
title: Értékelések a Azure Migrate
description: Ismerje meg a Azure Migrate értékeléseit.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 0cf933dd1c8c61edfcea20ea954c5813f3848b28
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425697"
---
# <a name="about-assessments-in-azure-migrate"></a>A Azure Migrate értékelései

Ez a cikk azt ismerteti, hogyan számítják ki az értékeléseket [Azure Migrateban: kiszolgáló értékelése](migrate-services-overview.md#azure-migrate-server-assessment-tool). Az értékeléseket helyszíni gépek csoportjain futtatva kiderítheti, hogy készen állnak-e a Azure Migratere való áttelepítésre.

## <a name="how-do-i-run-an-assessment"></a>Hogyan egy értékelést?
Az értékelést Azure Migrate: Server Assessment vagy más Azure vagy harmadik féltől származó eszköz használatával futtathatja. Azure Migrate projekt létrehozása után hozzá kell adnia a szükséges eszközt. [További információ](how-to-add-tool-first-time.md)

### <a name="collect-compute-data"></a>Számítási adatok gyűjtése

A számítási beállítások teljesítményadatait a következőképpen gyűjti a rendszer:

1. A [Azure Migrate készülék](migrate-appliance.md) valós idejű mintavételi pontot gyűjt:

    - **VMWare virtuális gépek**: VMWare virtuális gépek esetén a Azure Migrate készülék minden 20 másodperces intervallumban gyűjt valós idejű mintavételi pontot.
    - **Hyper-v virtuális gépek**: a Hyper-v rendszerű virtuális gépek esetében a valós idejű mintavételi pont gyűjtése minden 30 másodperces intervallumban történik.
    - **Fizikai kiszolgálók**: a fizikai kiszolgálók esetében a valós idejű mintavételi pont gyűjtése minden öt percenként történik. 
    
2. A készülék 10 percenként összesíti a mintavételi pontokat (20 másodperc, 30 másodperc, 5 perc). Az egyetlen adatpont létrehozásához a készülék kiválasztja a csúcsérték értékét az összes mintából, majd elküldi az Azure-nak.
3. A kiszolgáló értékelése az elmúlt hónapban az összes 10 perces mintavételi pontot tárolja.
4. Az értékelés létrehozásakor a kiszolgáló értékelése azonosítja a megfelelő adatpontot, amelyet a jobb méretezés érdekében használ a *teljesítmény* -és a *percentilis kihasználtságának*százalékos értékei alapján.

    - Ha például a teljesítmény előzményei egy hétig vannak beállítva, és a percentilis kihasználtsága a 95. percentilis, a kiszolgáló értékelése az elmúlt hét 10 perces mintavételi pontjait növekvő sorrendbe rendezi, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében. 
    - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiveszi a esetek 99% percentilis értékét.
    - Ha az időszakra vonatkozó csúcsérték-használatot szeretné kihagyni, és nem szeretné lemondani a kiugró adatokat, válassza ki a esetek 99% percentilis értéket a percentilis kihasználtsága érdekében.

5. Ennek az értéknek a megszorozza a komforttal, hogy az egyes mérőszámok (CPU-kihasználtság, memória kihasználtsága, lemez IOPS (olvasási és írási), lemez átviteli sebessége (olvasási és írási) és hálózati átviteli sebesség ( a készülék gyűjti.

Ha értékeléseket szeretne futtatni a kiszolgáló értékelése során, készüljön fel a helyszíni és az Azure-beli értékelésre, és állítsa be a Azure Migrate berendezést a helyszíni gépek folyamatos felderítéséhez. A gépek felderítése után azokat csoportokba gyűjtheti az értékeléshez. Részletesebb és nagy megbízhatósági felmérések esetén a gépek közötti függőségeket ábrázolhatja és leképezheti, így kiderítheti, hogyan telepítheti át őket.

- Ismerje meg a [VMWare virtuális gépek](tutorial-prepare-vmware.md), a [Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálók](tutorial-prepare-physical.md)értékelésének futtatását.
- Tudnivalók a [CSV-fájllal importált](tutorial-assess-import.md)kiszolgálók értékeléséről.
- További információ a [függőségi vizualizáció](concepts-dependency-visualization.md)beállításáról.

## <a name="assessments-in-server-assessment"></a>Értékelések a kiszolgáló értékelése során 

Az Azure Migrate Server Assessmenttel létrehozott értékelések az adatok időpontra vonatkozó pillanatképei. A kiszolgáló-értékelési eszköz két típusú értékelést biztosít.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Az összegyűjtött teljesítményadatok alapján ajánlásokat tevő értékelések | A virtuálisgép-méretre vonatkozó javaslat a processzor-és memóriahasználat adatain alapul.<br/><br/> A lemez típusára vonatkozó javaslat (standard HDD/SSD vagy prémium szintű Managed Disks) a helyszíni lemezek IOPS és átviteli sebességén alapul.
**Helyszíni** | Az olyan értékelések, amelyek nem használnak teljesítményadatokat, ajánlásokat tesznek. | A VM-méretre vonatkozó javaslat a helyszíni virtuális gép méretétől függ.<br/><br> Az ajánlott lemez típusa az értékeléshez kiválasztott tárolási típuson alapul.

## <a name="collecting-performance-data"></a>Teljesítményadatok gyűjtése

A teljesítményadatok gyűjtése a következőképpen történik:

1. A [Azure Migrate készülék](migrate-appliance.md) valós idejű mintavételi pontot gyűjt:

    - **VMWare virtuális gépek**: VMWare virtuális gépek esetén a Azure Migrate készülék minden 20 másodperces intervallumban gyűjt valós idejű mintavételi pontot.
    - **Hyper-v virtuális gépek**: a Hyper-v rendszerű virtuális gépek esetében a valós idejű mintavételi pont gyűjtése minden 30 másodperces intervallumban történik.
    - **Fizikai kiszolgálók**: a fizikai kiszolgálók esetében a valós idejű mintavételi pont gyűjtése minden öt percenként történik. 
    
2. A készülék 10 percenként összesíti a mintavételi pontokat (20 másodperc, 30 másodperc, 5 perc). Az egyetlen adatpont létrehozásához a készülék kiválasztja a csúcsérték értékét az összes mintából, majd elküldi az Azure-nak.
3. A kiszolgáló értékelése az elmúlt hónapban az összes 10 perces mintavételi pontot tárolja.
4. Az értékelés létrehozásakor a kiszolgáló értékelése azonosítja a megfelelő adatpontot, amelyet a jobb méretezés érdekében használ a *teljesítmény* -és a *percentilis kihasználtságának*százalékos értékei alapján.

    - Ha például a teljesítmény előzményei egy hétig vannak beállítva, és a percentilis kihasználtsága a 95. percentilis, a kiszolgáló értékelése az elmúlt hét 10 perces mintavételi pontjait növekvő sorrendbe rendezi, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében. 
    - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiveszi a esetek 99% percentilis értékét.
    - Ha az időszakra vonatkozó csúcsérték-használatot szeretné kihagyni, és nem szeretné lemondani a kiugró adatokat, válassza ki a esetek 99% percentilis értéket a percentilis kihasználtsága érdekében.

5. Ennek az értéknek a megszorozza a komforttal, hogy az egyes mérőszámok (CPU-kihasználtság, memória kihasználtsága, lemez IOPS (olvasási és írási), lemez átviteli sebessége (olvasási és írási) és hálózati átviteli sebesség ( a készülék gyűjti.
## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

A Azure Migrate értékelésében az alábbiak szerepelnek: kiszolgáló értékelése.

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az áttelepíteni kívánt hely. A kiszolgáló értékelése jelenleg a következő Azure-régiókat támogatja:<br/><br/> Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak USA középső régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, US Gov Arizona, US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
*Cél Storage-lemez (méretezés)* * | Az Azure-beli tároláshoz használandó lemezek típusa. <br/><br/> A célként megadott Storage-lemezt prémium szintű felügyelt, standard SSD felügyelt vagy standard szintű HDD-ként kell megadni.
**Cél Storage-lemez (teljesítmény-alapú méretezés)** | Adja meg a célként megadott Storage-lemez típusát automatikus, prémium szintű felügyelt, standard HDD által felügyelt vagy standard SSD által felügyelt módon.<br/><br/> **Automatikus**: a lemezre vonatkozó javaslat a lemezek teljesítményadatok (a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) és az átviteli sebesség) alapján történik.<br/><br/>**Prémium/standard**: az értékelés a kiválasztott tárolási típuson belül a lemez SKU-t javasolja.<br/><br/> Ha a prémium szintű Managed Disks szolgáltatással kívánja elérni a 99,9%-os virtuális gépre vonatkozó SLA-t. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott.<br/><br/> Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
**Fenntartott példányok (RIs)** | A [fenntartott példányokat](https://azure.microsoft.com/pricing/reserved-vm-instances/) az Azure-ban adhatja meg, így az értékelésben szereplő költségbecslés a bekerülési időszakra vonatkozó kedvezményeket veszi figyelembe.<br/><br/> A RIs jelenleg csak az utólagos elszámolású ajánlatokhoz támogatott Azure Migrateban.
**Méretezési feltételek** | A virtuális gép az Azure-ban való jobb méretezésére szolgál.<br/><br/> Használat a következőképpen: méretezés vagy teljesítmény-alapú méretezés.
**Teljesítményelőzmények** | Teljesítmény-alapú méretezéssel használatos. A teljesítményadatok kiértékelése során használt időtartam megadása.
**Százalékos kihasználtság** | Teljesítmény-alapú méretezéssel használatos. Meghatározza a jobb méretezéshez használandó teljesítményi minta százalékos értékét. 
**Virtuálisgép-sorozatok** | Itt adhatja meg azt az Azure-beli virtuálisgép-sorozatot, amelyet a jobb méretezés érdekében érdemes figyelembe venni. Ha például nem rendelkezik olyan éles környezettel, amely az Azure-beli sorozatú virtuális gépeket igényli, kizárhatja a sorozatot a listából vagy adatsorozatból.
**Kényelmi faktor** | Az értékelés során használt puffer. A virtuális gépek (CPU, memória, lemez és hálózat) számítógép-kihasználtsági adataira alkalmazva. A szolgáltatás olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítményű előzmények és a jövőbeli használat valószínű növekedése.<br/><br/> A 20%-os kihasználtságú 10 Magos virtuális gép például általában egy kétmagos virtuális gépet eredményez. A 2.0 x kényelmi tényezővel az eredmény egy négy Magos virtuális gép.
**Ajánlat** | Megjeleníti azt az [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) , amelyben regisztrálva van. A kiszolgáló értékelése ennek megfelelően becsüli a költségeket.
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton felül kapott előfizetési kedvezményeket. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha az Azure-beli virtuális gépek a nap 24 órájában nem futnak, a héten 7 napon belül megadhatja az időtartamot (havonta és órában naponta), amelyet a rendszer futtat. A becsült költségeket ennek megfelelően kezeli a rendszer.<br/><br/> Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értékre van állítva (az alapértelmezett beállítás), a nem Windows Azure-árakat a Windows rendszerű virtuális gépeknek számítjuk.

[Tekintse át az](best-practices-assessment.md) értékelés létrehozásával kapcsolatos ajánlott eljárásokat a kiszolgáló értékelésével.

## <a name="how-are-assessments-calculated"></a>Hogyan számítják ki az értékeléseket? 

Azure Migrate értékelései: a kiszolgáló értékelését a helyszíni gépekkel kapcsolatban gyűjtött metaadatok használatával számítjuk ki. Ha értékelést futtat a használatával importált gépeken. CSV-fájl, megadja a számítás metaadatait. A számítások három fázisban történnek:

1. **Azure-készültség kiszámítása**: vizsgálja meg, hogy a gépek alkalmasak-e az Azure-ba való áttelepítésre.
2. **Méretezési javaslatok kiszámítása**: a számítás, a tárolás és a hálózat méretezésének becslése. 
2. **Havi költségek kiszámítása**: az áttelepítés után a gépek Azure-beli futtatásának becsült havi számítási és tárolási költségeit számítja ki.

A számítások sorrendben vannak, és a gépi kiszolgáló csak akkor halad át egy későbbi fázisban, ha az előzőt adja át. Ha például egy kiszolgáló meghibásodik az Azure-készültséggel, az az Azure-hoz nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem történik meg az adott kiszolgálón.



## <a name="calculate-readiness"></a>Készültség kiszámítása

Nem minden gép alkalmas az Azure-ban való futtatásra. A kiszolgáló értékelése minden helyszíni gépet kiértékel, és felkészültségi kategóriát rendel hozzá. 
- **Készen áll az Azure-ra**: a gép a következőképpen telepíthető át az Azure-ba, változtatás nélkül. Az Azure teljes körű Azure-támogatással fog indulni.
- **Feltételesen készen áll az Azure-ra**: a gép elindulhat az Azure-ban, de előfordulhat, hogy nem rendelkezik teljes Azure-támogatással. Például a Windows Server régebbi verzióját futtató gépek nem támogatottak az Azure-ban. Mielőtt áttelepíti ezeket a gépeket az Azure-ba, körültekintően kell eljárnia. A készültségi problémák elhárításához kövesse az értékelés során javasolt szervizelési útmutatót.
- **Nem áll készen az Azure-ra**: a gép nem indul el az Azure-ban. Ha például egy helyszíni számítógép lemeze több mint 64-TBs, nem üzemeltethető az Azure-ban. Az áttelepítés előtt javítsa ki a problémát a Szervizelési útmutató segítségével. 
- **Készültség ismeretlen**: Azure Migrate nem tudta meghatározni a gép készültségét, mert nincs elég metaadat.

A készültség kiszámításához a kiszolgáló értékelése a következő táblázatokban összefoglalt számítógép-tulajdonságokat és operációsrendszer-beállításokat tekinti át. 

### <a name="machine-properties"></a>Számítógép tulajdonságai

A kiszolgáló értékelése a helyszíni virtuális gép alábbi tulajdonságait tekinti át annak megállapítására, hogy futtatható-e az Azure-ban.

**Tulajdonság** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure a BIOS rendszerindítási típusával támogatja a virtuális gépeket, nem az UEFI-t. | Feltételesen üzemkész, ha a rendszerindítás UEFI típusú.
**Mag** | A gépekben lévő magok számának egyenlőnek vagy kisebbnek kell lennie, mint az Azure-beli virtuális gép által támogatott magok maximális száma (128).<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate az összehasonlításhoz a felhasznált magokat veszi figyelembe. Ha az értékelési beállításokban meg van adva egy kényelmi tényező, a kihasználatlan magok számát a Comfort faktor megszorozza.<br/><br/> Ha nincsenek teljesítménybeli előzmények, Azure Migrate a lefoglalt magokat a komfort tényező alkalmazása nélkül használja. | Ha a korlát értéke kisebb vagy egyenlő, akkor készen áll.
**Memória** | A számítógép memóriájának mérete nem lehet kisebb, mint a maximális memória (3892 gigabájt [GB] az Azure M sorozat Standard_M128m&nbsp;<sup>2</sup>) számára, amely egy Azure-beli virtuális gép számára engedélyezett. [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha elérhetők a teljesítmény előzményei, Azure Migrate az összehasonlításhoz használt memóriát veszi figyelembe. Ha meg van adva egy kényelmi tényező, a kihasznált memóriát a komfort tényező szorozza meg.<br/><br/> Ha nincsenek előzmények, a rendszer a lefoglalt memóriát használja a komfort tényező alkalmazása nélkül.<br/><br/> | A határértékeken belül készen áll.
**Storage-lemez** | A lemez lefoglalt méretének 32 TB-nak vagy ennél kisebbnek kell lennie. Bár az Azure támogatja a 64 TB-os lemezeket ultra SSD lemezekkel, Azure Migrate: a Server Assessment jelenleg a 32 TB-ot ellenőrzi a lemez méretének korlátozásai miatt, mivel a ultra SSD még nem támogatja. <br/><br/> A géphez csatolt lemezek számának 65 vagy kevesebbnek kell lennie, beleértve az operációsrendszer-lemezt is. | A határértékeken belül készen áll.
**Hálózat** | A gépnek 32 vagy kevesebb hálózati adapterrel (NIC) kell rendelkeznie. | A határértékeken belül készen áll.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
A virtuális gép tulajdonságaival együtt a kiszolgáló értékelése a gépek vendég operációs rendszerét vizsgálja annak megállapítására, hogy futtatható-e az Azure-ban.

> [!NOTE]
> A VMware virtuális gépek esetében a kiszolgáló értékelése a virtuális géphez megadott operációs rendszert használja vCenter Server a vendég operációs rendszer elemzésének kezeléséhez. A VMware-en futó Linux rendszerű virtuális gépek esetében jelenleg nem azonosítja a vendég operációs rendszer pontos kernel-verzióját.

A kiszolgáló értékelése a következő logikát használja az Azure-készültség azonosítására az operációs rendszer alapján.

**Operációs rendszer** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
Windows Server 2016 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 minden SPs-vel | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-beli használatra
Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2003, 2003 R2 | Ezek az operációs rendszerek átadták a támogatásuk befejezésének dátumát, és [egyéni támogatási szerződést (CSA)](https://aka.ms/WSosstatement) igényelnek az Azure támogatásához. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes frissíteni az operációs rendszert.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Ezek az operációs rendszerek a támogatásuk befejezésének napját adták át. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Windows-ügyfél 7, 8 és 10 | Az Azure csak a [Visual Studio-előfizetéshez](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali verzió | Az Azure támogatást nyújt a több- [bérlős üzemeltetési jogosultságokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Ezek az operációs rendszerek a támogatásuk befejezésének napját adták át. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket](../virtual-machines/linux/endorsed-distros.md). Az Azure-ban más linuxos operációs rendszerek is elindíthatók, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert egy támogatott verzióra. | Készen áll az Azure-ra, ha a verziót jóváhagyták.<br/><br/>Feltételesen üzemkész, ha a verzió nincs támogatva.
Egyéb operációs rendszerek<br/><br/> Például: Oracle Solaris, Apple macOS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy telepítsen egy támogatott operációs rendszert az Azure-ba való áttelepítés előtt.  
Az operációs rendszer **vCenter Serverként van megadva** | A Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készültség. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | Előfordulhat, hogy a gép az Azure-ban indul el, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes lehet a gép operációs rendszerét 32 bites operációs rendszerről 64-bites operációs rendszerre frissíteni.

## <a name="calculate-sizing-as-is-on-premises"></a>Méretezés kiszámítása (helyszíni)

Ha a gép az Azure-hoz való használatra készként van megjelölve, a kiszolgáló értékelése során az Azure-beli virtuális gép és a lemez SKU azonosítására vonatkozó javaslatokat tesz. Ha a as-helyszíni méretezést használja, a kiszolgáló értékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit.

**Számítási méretezés**: egy Azure-beli virtuális gép SKU-t foglal le a helyszínen lefoglalt méret alapján.
**Tárolás/lemez méretezése**: a kiszolgáló értékelése az értékelés tulajdonságaiban (standard HDD/SSD/prémium) megadott tárolási típust vizsgálja, és ennek megfelelően javasolja a lemez típusát. Az alapértelmezett tárolási típus a prémium szintű lemezek.
**Hálózati méretezés**: a kiszolgáló értékelése a helyi gépen található hálózati adaptert veszi figyelembe.


## <a name="calculate-sizing-performance-based"></a>Méretezés kiszámítása (teljesítmény-alapú)

Ha a gép az Azure-ban készként van megjelölve, és teljesítmény-kialakítási méretezést használ, a kiszolgáló értékelése a következő módon teszi a méretezési javaslatokat:

- A kiszolgáló értékelése a gép teljesítményének előzményeit veszi figyelembe, hogy azonosítsa a virtuális gép méretét és a lemez típusát az Azure-ban.
- Ha a kiszolgálók importálása CSV-fájllal történt, a rendszer a megadott értékeket használja. Ez a módszer különösen akkor hasznos, ha túlterhelte a helyszíni gépet, a kihasználtság valójában alacsony, és az Azure-ban a virtuális gép jobb méretét szeretné megtakarítani a költségek megtakarítása érdekében. 
- Ha nem szeretné a teljesítményadatokat használni, állítsa alaphelyzetbe a méretezési feltételeket úgy, hogy az a helyszíni legyen, az előző szakaszban leírtak szerint.

### <a name="calculate-storage-sizing"></a>A tároló méretezésének kiszámítása

A tárolás méretezése érdekében a Azure Migrate megkísérli leképezni a géphez csatolt összes lemezt az Azure-ban lévő lemezre, és a következőképpen működik:

1. A kiszolgáló értékelése hozzáadja a lemez olvasási és írási IOPS az összes szükséges IOPS lekéréséhez. Hasonlóképpen hozzáadja az olvasási és írási sebesség értékét az egyes lemezek teljes átviteli sebességének lekéréséhez.
2. Ha a tárolási típust automatikusként adta meg, az érvényes IOPS és az átviteli sebesség alapján, a kiszolgáló értékelése meghatározza, hogy a lemezt szabványos HDD-re, standard SSD-re vagy egy prémium szintű lemezre kell-e leképezni az Azure-ban. Ha a tárolási típus értéke standard HDD/SSD/Premium, a Server Assessment megpróbál a kiválasztott tárolási típuson belül egy lemezes SKU-t keresni (standard HDD/SSD/prémium lemez).
3. A lemezek a következőképpen vannak kiválasztva:
    - Ha a kiszolgáló értékelése nem talál a szükséges IOPS és adatátviteli sebességű lemezt, az az Azure számára nem megfelelőként jelöli meg a gépet.
    - Ha a kiszolgáló értékelése megfelelő lemezeket talál, akkor kiválasztja azokat a lemezeket, amelyek támogatják az értékelési beállításokban megadott helyet.
    - Ha több jogosult lemez van, a kiszolgáló értékelése a legalacsonyabb költséggel kiválasztja a lemezt.
    - Ha valamelyik lemez teljesítményadatokat nem érhető el, a lemez konfigurációs adatai (lemez mérete) az Azure-ban szabványos SSD-lemez megtalálására szolgálnak.

### <a name="calculate-network-sizing"></a>Hálózati méretezés kiszámítása

A kiszolgáló értékelése megpróbál olyan Azure-beli virtuális gépet találni, amely támogatja a helyszíni géphez csatlakoztatott hálózati adapterek számát és a hálózati adapterek által igényelt teljesítményt.
- A helyszíni virtuális gép hatékony hálózati teljesítményének lekéréséhez a kiszolgáló értékelése összesíti a másodpercenként továbbított adatokat (MB/s) a gépen (kimenő hálózat), az összes hálózati adapteren, és alkalmazza a komfort tényezőt. Ezt a számot használja egy olyan Azure-beli virtuális gép megtalálására, amely támogatja a szükséges hálózati teljesítményt.
- A hálózati teljesítmény mellett a kiszolgáló értékelése azt is mérlegeli, hogy az Azure-beli virtuális gép támogatja-e a hálózati adapterek számát.
- Ha nem érhető el hálózati teljesítményadatok, a kiszolgáló értékelése csak a virtuális gép méretének megfelelő hálózati adapterek darabszámát veszi figyelembe.


### <a name="calculate-compute-sizing"></a>Számítási méretezés kiszámítása

A tárolási és hálózati követelmények kiszámítását követően a kiszolgáló értékelése a processzor-és memória-követelményeket úgy tekinti meg, hogy megfelelő virtuálisgép-méretet találjon az Azure-ban.
- Azure Migrate az Azure-ban egy megfelelő virtuálisgép-méret megtalálását vizsgálja a ténylegesen felhasznált magok és memória alapján.
- Ha nem található megfelelő méret, a gép nem megfelelőként van megjelölve az Azure-hoz.
- Ha talál megfelelő méretet, Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután alkalmazza a hely és az árképzési szintek beállításait a virtuális gép végső méretére vonatkozó javaslatra.
- Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.


### <a name="calculate-confidence-ratings"></a>Megbízhatósági minősítések kiszámítása

Azure Migrate minden teljesítmény-alapú értékelése egy olyan megbízhatósági minősítéssel van társítva, amely egy (legalacsonyabb) és öt csillag közötti (a legmagasabb) tartományba esik.
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítések nem alkalmazhatók *a* helyszíni értékelésekhez.
- A teljesítmény-alapú méretezéshez a kiszolgáló értékeléséhez a következőket kell tennie:
    - A processzor és a virtuális gép memóriájának kihasználtsági adatai.
    - A lemez a virtuális géphez csatolt minden lemez IOPS és adatátviteli adatokkal rendelkezik.
    - A hálózati I/O-t a virtuális géphez csatlakoztatott összes hálózati adapter teljesítmény-alapú méretezésének kezeléséhez.

   Ha ezek a kihasználtsági számok nem érhetők el vCenter Serverban, előfordulhat, hogy a méretre vonatkozó javaslat nem megbízható.

Az elérhető adatpontok százalékos arányának függvényében az értékelés megbízhatósági minősítése az alábbiak szerint megy végbe.

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0-20% | 1 csillag
   21-40% | 2 csillag
   41-60% | 3 csillag
   61-80% | 4 csillag
   81-100% | 5 csillag

> [!NOTE]
> A megbízhatósági minősítések nincsenek hozzárendelve a használatával importált kiszolgálók értékeléséhez. CSV-fájl Azure Migrateba. 

#### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem tudta felmérni a környezetét arra az időtartamra, amelyhez az értékelést létrehozza. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy nappal meg kell várnia a begyűjtést.
- Néhány virtuális gép leállt az értékelés kiszámításának időtartama alatt. Ha bármely virtuális gép ki van kapcsolva bizonyos időtartamra, a kiszolgáló értékelése nem tudja összegyűjteni az adott időszak teljesítményadatait.
- Néhány virtuális gép az értékelés kiszámításának időtartama alatt lett létrehozva. Ha például az előző hónap teljesítmény-előzményeire vonatkozó értékelést hozott létre, de egyes virtuális gépeket csak egy héttel ezelőtt hoztak létre a környezetben, akkor az új virtuális gépek teljesítménybeli előzményei nem állnak a teljes időtartamra.

> [!NOTE]
> Ha az értékelések megbízhatósági minősítése kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felkeresi a környezetet, majd számítsa ki újra az értékelést. Ha nem, akkor előfordulhat, hogy a teljesítmény-alapú méretezés nem megbízható. Ebben az esetben javasoljuk, hogy az értékelést a helyszíni méretezésre állítsa át.

## <a name="calculate-monthly-costs"></a>Havi költségek kiszámítása

A méretezési javaslatok befejezését követően a Azure Migrate kiszámítja a számítási és tárolási költségeket az áttelepítés után.

- **Számítási költség**: az ajánlott Azure-beli virtuális gép méretének használata, Azure Migrate a számlázási API használatával számítja ki a virtuális gép havi költségét.
    - A számítás az operációs rendszer, a frissítési garancia, a fenntartott példányok, a virtuális gép üzemidő, a hely és a pénznem beállításait veszi figyelembe.
    - Összesíti a költségeket az összes gépen a teljes havi számítási költség kiszámításához.
- **Tárolási költség**: a gép havi tárolási költségeit a rendszer a géphez csatlakoztatott összes lemez havi költségének összesítésével számítja ki a következőképpen:
    - A kiszolgáló értékelése a teljes havi tárolási költségeket az összes gép tárolási költségeinek összesítésével számítja ki.
    - Jelenleg a számítás nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.


## <a name="next-steps"></a>Következő lépések

[Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat. 
