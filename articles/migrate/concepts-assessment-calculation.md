---
title: Értékelési számítások a Azure Migrateban | Microsoft Docs
description: Áttekintést nyújt az értékelési számításokról a Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 4511c42514a5399d41029b61297bd4c1b0b63d9a
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827548"
---
# <a name="assessment-calculations-in-azure-migrate"></a>Értékelési számítások a Azure Migrate

[Azure Migrate](migrate-services-overview.md) központi központot biztosít a helyszíni alkalmazások és munkaterhelések felderítésének, értékelésének és áttelepítésének nyomon követéséhez. Emellett nyomon követi a privát és a nyilvános Felhőbeli példányokat az Azure-ban. Az elosztó Azure Migrate eszközöket kínál az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatait.

A kiszolgáló értékelése Azure Migrate eszköz, amely a helyszíni kiszolgálókat értékeli át az Azure-ba való áttelepítéshez. Ez a cikk Az értékelések kiszámításának módját ismerteti.

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Azt az Azure-helyet adja meg, amelyre az áttelepítést szeretné végezni.<br/><br/>A kiszolgáló értékelése jelenleg a következő célcsoportokat támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak USA középső régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, US Gov Arizona, US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
**Tárolás típusa** | Megadja az Azure-beli tároláshoz használni kívánt lemezek típusát. <br/><br/> A helyszíni méretezéshez megadhatja a célként megadott Storage-lemez típusát prémium szintű felügyelt, standard SSD által felügyelt vagy standard HDD által felügyelt módon. A teljesítmény-alapú méretezéshez megadhatja a célként megadott tároló lemezének típusát automatikus, prémium szintű felügyelt, standard HDD által felügyelt vagy standard SSD által felügyelt módon. Ha a tárolási típust automatikusként adja meg, a lemezre vonatkozó javaslat a lemezek teljesítmény-adatain alapul: a másodpercenkénti bemeneti/kimeneti műveletek (IOPS) és az átviteli sebesség. <br/><br/>Ha a tárolási típust prémium vagy standard szintűként adja meg, akkor az értékelés a kiválasztott tárolási típuson belül egy lemez SKU-t javasol. Ha az Egypéldányos VM 99,9%-os SLA-t kívánja elérni, érdemes lehet a tárolási típust prémium szintű felügyelt lemezként megadni. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott. Vegye figyelembe, hogy az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
**Fenntartott példányok (RIs)** | Ez a tulajdonság segít a [fenntartott példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) megadásában az Azure-ban. Az értékelésben szereplő költségbecslés során a rendszer figyelembe veszi az "RI-kedvezményeket". A RIs jelenleg csak az utólagos elszámolású ajánlatokhoz támogatott Azure Migrateban.
**Méretezési feltételek** | Az Azure-beli virtuális gépekhez használt feltételek beállítása. A teljesítmény *-alapú* méretezést vagy a helyszíni virtuális gépek méretének megtervezése nélkül is dönthet úgy, hogy megtekintette a teljesítményt.
**Teljesítményelőzmények** | Beállítja a gépek teljesítményi adatai kiértékeléséhez szükséges időtartamot. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*.
**Százalékos kihasználtság** | Meghatározza a jobb méretezéshez beállított teljesítményi minta százalékos értékét. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés teljesítmény-alapú.
**Virtuálisgép-sorozatok** | Itt adhatja meg azt a virtuálisgép-sorozatot, amelyet a jobb méretezés érdekében fontolóra kíván venni. Ha például olyan éles környezettel rendelkezik, amelyet nem szeretne áttelepíteni az Azure-beli sorozatú virtuális gépekre, kizárhatja a-sorozatokat a listából vagy adatsorozatból, és a jobb méretezés csak a kiválasztott adatsorozatban végezhető el.
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2\.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Megjeleníti a regisztrált [Azure-ajánlatot](https://azure.microsoft.com/support/legal/offer-details/) . Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A fiók számlázási pénznemét jeleníti meg.
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek a nap 24 órájában nem futnak, az Azure-ban hetente 7 napon belül megadhatja azt az időtartamot (a havi napok számát és a napi óraszámot). Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadja, hogy rendelkezik-e frissítési garanciával, [](https://azure.microsoft.com/pricing/hybrid-use-benefit/)és jogosult-e a Azure Hybrid Benefitre. Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett beállítás az igen.

## <a name="how-are-assessments-calculated"></a>Hogyan számítják ki az értékeléseket?

A rendszer a helyszíni kiszolgálókról összegyűjtött metaadatok használatával kiszámítja Azure Migrate kiszolgáló értékelésének értékelését. Az értékelés kiszámítása három lépésben történik. Az értékelési számítás az egyes kiszolgálók esetében egy Azure-megfelelőségi elemzéssel kezdődik, majd a méret és végül a havi költségbecslés. A kiszolgálók csak akkor mozognak egy későbbi fázisba, ha az előzőt átadja. Ha például egy kiszolgáló meghibásodik az Azure alkalmassági ellenőrzésének, az nem megfelelőként van megjelölve az Azure-hoz, és a méretezés és a költségszámítás nem történik meg az adott kiszolgálón.

## <a name="azure-suitability-analysis"></a>Azure-megfelelőségi elemzés

Nem minden gép alkalmas az Azure-ban való futtatásra. A kiszolgáló értékelése a helyszíni gépeket az Azure áttelepítésre való alkalmasságának megfelelően értékeli. Emellett az egyes mért gépeket a következő alkalmassági kategóriák valamelyikébe rendeli:
- **Készen áll az Azure-ra**: A gép a következő módon telepíthető át az Azure-ba: módosítás nélkül. Az Azure teljes körű Azure-támogatással fog indulni.
- **Feltételesen készen áll az Azure-ra**: Előfordulhat, hogy a gép az Azure-ban indul el, de nem rendelkezik teljes körű Azure-támogatással. Például a Windows Server régebbi verzióját futtató gépek nem támogatottak az Azure-ban. Ezeknek a gépeknek az Azure-ba történő áttelepítése előtt körültekintően kell eljárnia, és az értékelés során javasolt szervizelési útmutatást követve javítsa ki a készültségi problémákat.
- **Nem áll készen az Azure-ra**: A gép nem fog elindulni az Azure-ban. Ha például egy helyszíni gépen több mint 64 terabájt (TB) lemez van csatolva, akkor az nem üzemeltethető az Azure-ban. Az értékelés során javasolt szervizelési útmutatást követve javítsa ki a készültségi problémát, mielőtt áttelepíti a gépet az Azure-ba. Az Azure-ra nem készként megjelölt gépek esetében nem végezhető el a jobb méretezés és a költségbecslés.
- **Készültség ismeretlen**: A Azure Migrate nem tudta megállapítani a gép készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

A kiszolgáló értékelése ellenőrzi a számítógép tulajdonságait és a vendég operációs rendszert, hogy meghatározza a helyszíni gép Azure-készültségét.

### <a name="machine-properties"></a>Számítógép tulajdonságai

A kiszolgáló értékelése a helyszíni virtuális gép alábbi tulajdonságait tekinti át annak megállapítására, hogy futtatható-e az Azure-ban.

**Tulajdonság** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure a BIOS rendszerindítási típusával támogatja a virtuális gépeket, nem az UEFI-t. | Feltételesen üzemkész, ha a rendszerindítás UEFI típusú.
**Mag** | A gépekben lévő magok számának egyenlőnek vagy kisebbnek kell lennie, mint az Azure-beli virtuális gép által támogatott magok maximális száma (128).<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate az összehasonlításhoz a felhasznált magokat veszi figyelembe. Ha az értékelési beállításokban meg van adva egy kényelmi tényező, a kihasználatlan magok számát a Comfort faktor megszorozza.<br/><br/> Ha nincsenek teljesítménybeli előzmények, Azure Migrate a lefoglalt magokat a komfort tényező alkalmazása nélkül használja. | Ha a korlát értéke kisebb vagy egyenlő, akkor készen áll.
**Memória** | A számítógép memóriájának mérete nem lehet kisebb, mint az Azure-beli virtuális gép számára engedélyezett maximális memória (3892 gigabájt [GB]&nbsp;az Azure M Series Standard_M128m<sup>2</sup>esetében). [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha elérhetők a teljesítmény előzményei, Azure Migrate az összehasonlításhoz használt memóriát veszi figyelembe. Ha meg van adva egy kényelmi tényező, a kihasznált memóriát a komfort tényező szorozza meg.<br/><br/> Ha nincsenek előzmények, a rendszer a lefoglalt memóriát használja a komfort tényező alkalmazása nélkül.<br/><br/> | A határértékeken belül készen áll.
**Storage-lemez** | A lemez lefoglalt méretének 32 TB-nak vagy ennél kisebbnek kell lennie. Bár az Azure támogatja az 64 TB-os lemezeket ultra SSD lemezekkel, Azure Migrate: A kiszolgáló-értékelés jelenleg a 32 TB TÁRTERÜLETET korlátozza a lemez méretének korlátozását, mivel a ultra SSD még nem támogatja. <br/><br/> A géphez csatolt lemezek számának 65 vagy kevesebbnek kell lennie, beleértve az operációsrendszer-lemezt is. | A határértékeken belül készen áll.
**Hálózat** | A gépnek 32 vagy kevesebb hálózati adapterrel (NIC) kell rendelkeznie. | A határértékeken belül készen áll.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
A virtuális gép tulajdonságaival együtt a kiszolgáló értékelése a gépek vendég operációs rendszerét vizsgálja annak megállapítására, hogy futtatható-e az Azure-ban.

> [!NOTE]
> A VMware virtuális gépek esetében a kiszolgáló értékelése a virtuális géphez megadott operációs rendszert használja vCenter Server a vendég operációs rendszer elemzésének kezeléséhez. A VMware-en futó Linux rendszerű virtuális gépek esetében jelenleg nem azonosítja a vendég operációs rendszer pontos kernel-verzióját.

A kiszolgáló értékelése a következő logikát használja az Azure-készültség azonosítására az operációs rendszer alapján.

**Operációs rendszer** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
Windows Server 2016 & összes SPs | Az Azure teljes körű támogatást biztosít. | Azure-beli használatra kész
Windows Server 2012 R2 & összes SPs | Az Azure teljes körű támogatást biztosít. | Azure-beli használatra kész
Windows Server 2012 & összes SPs | Az Azure teljes körű támogatást biztosít. | Azure-beli használatra kész
Windows Server 2008 R2 minden SPs-vel | Az Azure teljes körű támogatást biztosít.| Azure-beli használatra kész
Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Azure-beli használatra kész
Windows Server 2003, 2003 R2 | Ezek az operációs rendszerek átadták a támogatásuk befejezésének dátumát, és [egyéni támogatási szerződést (CSA)](https://aka.ms/WSosstatement) igényelnek az Azure támogatásához. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes frissíteni az operációs rendszert.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Ezek az operációs rendszerek a támogatásuk befejezésének napját adták át. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Windows-ügyfél 7, 8 és 10 | Az Azure csak a [Visual Studio](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) -előfizetéshez nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali verzió | Az Azure támogatást nyújt a több- [bérlős üzemeltetési jogosultságokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Ezek az operációs rendszerek a támogatásuk befejezésének napját adták át. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket](../virtual-machines/linux/endorsed-distros.md). Az Azure-ban más linuxos operációs rendszerek is elindíthatók, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert egy támogatott verzióra. | Készen áll az Azure-ra, ha a verziót jóváhagyták.<br/><br/>Feltételesen üzemkész, ha a verzió nincs támogatva.
Egyéb operációs rendszerek<br/><br/> Például: Oracle Solaris, Apple Mac OS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem biztosít operációsrendszer-támogatást. | Feltételesen készen áll az Azure-ra. Javasoljuk, hogy telepítsen egy támogatott operációs rendszert az Azure-ba való áttelepítés előtt.  
Az operációs rendszer vCenter Serverként van megadva | A Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készültség. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | Előfordulhat, hogy a gép az Azure-ban indul el, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra. Az Azure-ba való Migrálás előtt érdemes lehet a gép operációs rendszerét 32 bites operációs rendszerről 64-bites operációs rendszerre frissíteni.

## <a name="sizing"></a>Méretezés

Miután egy gép az Azure-ra készként van megjelölve, a kiszolgáló értékelése méretezési javaslatokat tesz, amely magában foglalja a megfelelő Azure-beli virtuális gép és SKU azonosítását a helyszíni virtuális géphez. Ezek az ajánlások a megadott értékelési tulajdonságokkal függően változnak.

- Ha az értékelés *teljesítmény-alapú méretezést*használ, Azure Migrate figyelembe veszi a gép teljesítményének előzményeit, hogy azonosítsa a virtuális gép méretét és típusát az Azure-ban. Ez a módszer különösen akkor hasznos, ha felhasználta a helyszíni virtuális gépet, de a kihasználtság alacsony, és az Azure-ban a virtuális gép jobb méretét szeretné megtakarítani a költségek megtakarítása érdekében. Ez a módszer segít optimalizálni a méreteket az áttelepítés során.
- Ha nem szeretné figyelembe venni a virtuálisgép-méretezési teljesítményadatokat, és a helyszíni gépeket az Azure-ba szeretné átvenni, akkor a méretezési feltételeket a következőképpen állíthatja be *a helyszínen*:. Ezt követően a kiszolgáló értékelése a helyszíni konfiguráció alapján méretezi a virtuális gépeket a kihasználtsági adatmennyiség figyelembevétele nélkül. Ebben az esetben a lemezre méretezési tevékenységek az értékelés tulajdonságainál (standard HDD, standard SSD vagy prémium lemez) megadott tárolási típuson alapulnak.

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezés

A teljesítmény-alapú méretezés érdekében a kiszolgáló értékelése a virtuális géphez csatlakoztatott lemezekkel kezdődik, majd a hálózati adapterek. Ezután leképezi az Azure-beli virtuális gépek SKU-jának a helyszíni virtuális gép számítási követelményei alapján. A Azure Migrate berendezés a helyszíni környezetet a CPU, a memória, a lemezek és a hálózati adapter teljesítményadatait gyűjti.

**Teljesítményadatok gyűjtésének lépései:**

1. A VMware virtuális gépek esetében a Azure Migrate készülék minden 20 másodperces intervallumban gyűjt valós idejű mintavételi pontot. A Hyper-V virtuális gépek esetében a valós idejű mintavételi pont gyűjtése minden 30 másodperces intervallumban történik.
1. A készülék 10 percenként gyűjti össze a mintavételi pontokat, és az elmúlt 10 percben a kiszolgáló értékeléséhez elküldi a maximális értéket.
1. A kiszolgáló értékelése az elmúlt egy hónapban az összes 10 perces mintavételi pontot tárolja. Ezután a *teljesítmény előzményeihez* és a *percentilis kihasználtságához*megadott értékelési tulajdonságoktól függően azonosítja a megfelelő adatpontot, amelyet a jobb méretezés érdekében használ. Ha például a teljesítmény-előzmények értéke 1 nap, a percentilis kihasználtsága pedig a 95. percentilis, a Server Assessment az elmúlt egy nap 10 perces mintavételi pontját használja, növekvő sorrendbe rendezi őket, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében.
1. Ennek az értéknek a megszorozza a komforttal, hogy az egyes mérőszámok (CPU-kihasználtság, memória kihasználtsága, lemez IOPS (olvasási és írási), lemez átviteli sebessége (olvasási és írási) és hálózati átviteli sebesség ( a készülék gyűjti.

A tényleges kihasználtsági érték meghatározása után a tároló, a hálózat és a számítási méret a következőképpen lesz kezelve.

**Tároló méretezése**: Azure Migrate megkísérli leképezni a géphez csatolt összes lemezt az Azure-beli lemezre.

> [!NOTE]
> Azure Migrate Server Assessment csak a felügyelt lemezeket támogatja az értékeléshez.

  - A kiszolgáló értékelése hozzáadja a lemez olvasási és írási IOPS az összes szükséges IOPS lekéréséhez. Hasonlóképpen hozzáadja az olvasási és írási sebesség értékét az egyes lemezek teljes átviteli sebességének lekéréséhez.
  - Ha a tárolási típust automatikusként adta meg, a IOPS és az átviteli sebesség alapján, akkor a kiszolgáló értékelése meghatározza, hogy a lemezt szabványos HDD-re, standard SSD-re vagy egy prémium szintű lemezre kell-e leképezni az Azure-ban. Ha a tárolási típus értéke standard HDD/SSD/Premium, a Server Assessment megpróbál a kiválasztott tárolási típuson belül egy lemezes SKU-t keresni (standard HDD/SSD/prémium lemez).
  - Ha a kiszolgáló értékelése nem talál a szükséges IOPS és adatátviteli sebességű lemezt, az az Azure számára nem megfelelőként jelöli meg a gépet.
  - Ha a kiszolgáló értékelése megfelelő lemezeket talál, akkor kiválasztja azokat a lemezeket, amelyek támogatják az értékelési beállításokban megadott helyet.
  - Ha több jogosult lemez van, a kiszolgáló értékelése a legalacsonyabb költséggel kiválasztja a lemezt.
  - Ha valamelyik lemez teljesítményadatokat nem érhető el, a lemez konfigurációs adatai (lemez mérete) az Azure-ban szabványos SSD-lemez megtalálására szolgálnak.

**Hálózati méretezés**: A kiszolgáló értékelése megpróbál olyan Azure-beli virtuális gépet találni, amely támogatja a helyszíni géphez csatlakoztatott hálózati adapterek számát és a hálózati adapterek által igényelt teljesítményt.
- A helyszíni virtuális gép hatékony hálózati teljesítményének lekéréséhez a kiszolgáló értékelése összesíti a másodpercenként továbbított adatokat (MB/s) a gépen (kimenő hálózat), az összes hálózati adapteren, és alkalmazza a komfort tényezőt. Ezt a számot használja egy olyan Azure-beli virtuális gép megtalálására, amely támogatja a szükséges hálózati teljesítményt.
- A hálózati teljesítmény mellett a kiszolgáló értékelése azt is mérlegeli, hogy az Azure-beli virtuális gép támogatja-e a hálózati adapterek számát.
- Ha nem érhető el hálózati teljesítményadatok, a kiszolgáló értékelése csak a virtuális gép méretének megfelelő hálózati adapterek darabszámát veszi figyelembe.

**Számítási méretezés**: A tárolási és hálózati követelmények kiszámítását követően a kiszolgáló értékelése a processzor-és memória-követelményeket úgy tekinti meg, hogy megfelelő virtuálisgép-méretet találjon az Azure-ban.
- Azure Migrate az Azure-ban egy megfelelő virtuálisgép-méret megtalálását vizsgálja a ténylegesen felhasznált magok és memória alapján.
- Ha nem található megfelelő méret, a gép nem megfelelőként van megjelölve az Azure-hoz.
- Ha talál megfelelő méretet, Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután alkalmazza a hely és az árképzési szintek beállításait a virtuális gép végső méretére vonatkozó javaslatra.
- Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezés

Ha helyszíni *méretezést*használ, a kiszolgáló értékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit. Ehelyett egy virtuálisgép-SKU-t foglal le az Azure-ban a helyszínen lefoglalt méret alapján. A lemezek méretezéséhez hasonlóan a kiszolgáló értékelése az értékelés tulajdonságainál (standard HDD/SSD/prémium) megadott tárolási típust vizsgálja, és ennek megfelelően javasolja a lemez típusát. Az alapértelmezett tárolási típus a prémium szintű lemezek.

## <a name="confidence-ratings"></a>Megbízhatósági minősítések
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

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem tudta felmérni a környezetét arra az időtartamra, amelyhez az értékelést létrehozza. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy nappal meg kell várnia a begyűjtést.
- Néhány virtuális gép leállt az értékelés kiszámításának időtartama alatt. Ha bármely virtuális gép ki van kapcsolva bizonyos időtartamra, a kiszolgáló értékelése nem tudja összegyűjteni az adott időszak teljesítményadatait.
- Néhány virtuális gép az értékelés kiszámításának időtartama alatt lett létrehozva. Ha például az előző hónap teljesítmény-előzményeire vonatkozó értékelést hozott létre, de egyes virtuális gépeket csak egy héttel ezelőtt hoztak létre a környezetben, akkor az új virtuális gépek teljesítménybeli előzményei nem állnak a teljes időtartamra.

> [!NOTE]
> Ha az értékelések megbízhatósági minősítése kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felkeresi a környezetet, majd számítsa ki újra az értékelést. Ha nem, akkor előfordulhat, hogy a teljesítmény-alapú méretezés nem megbízható. Ebben az esetben javasoljuk, hogy az értékelést a helyszíni méretezésre állítsa át.

## <a name="monthly-cost-estimation"></a>Havi költségbecslés

A méretezési javaslatok befejezését követően a Azure Migrate kiszámítja a számítási és tárolási költségeket az áttelepítés után.

- **Számítási díj**: Az ajánlott Azure-beli virtuális gép méretének használatával a Azure Migrate a számlázási API-val számítja ki a virtuális gép havi költségét.
    - A számítás az operációs rendszer, a frissítési garancia, a fenntartott példányok, a virtuális gép üzemidő, a hely és a pénznem beállításait veszi figyelembe.
    - Összesíti a költségeket az összes gépen a teljes havi számítási költség kiszámításához.
- **Tárolási díj**: A gép havi tárolási költségeit a rendszer a géphez csatlakoztatott összes lemez havi költségének összesítésével számítja ki a következőképpen:
    - A kiszolgáló értékelése a teljes havi tárolási költségeket az összes gép tárolási költségeinek összesítésével számítja ki.
    - Jelenleg a számítás nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.


## <a name="next-steps"></a>További lépések

Hozzon létre egy értékelést a [VMWare virtuális gépek](tutorial-assess-vmware.md) vagy a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)számára.
