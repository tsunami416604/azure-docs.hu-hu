---
title: Értékelési számítások a Azure Migrateban | Microsoft Docs
description: Áttekintést nyújt az értékelési számításokról a Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 87aa48c992b7887ce86c43cac29910dcc57b3e91
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234293"
---
# <a name="assessment-calculations"></a>Értékelési számítások

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések felderítésének, értékelésének és áttelepítésének nyomon követéséhez, valamint a magán-és nyilvános Felhőbeli példányok Azure-ba való áthelyezéséhez. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

A kiszolgáló értékelése Azure Migrate eszköz, amely a helyszíni kiszolgálókat értékeli át az Azure-ba való áttelepítéshez. Ez a cikk Az értékelések kiszámításának módját ismerteti.

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/> A kiszolgáló értékelése jelenleg a következő célcsoportokat támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, Közép-India, USA középső régiója, Kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti régiója, Kelet-RÉGIÓJA, Közép-Németország, Északkelet-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Észak USA középső régiója, Észak-Európa, az USA déli középső régiója, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, US Gov Arizona, US Gov Texas, US Gov Virginia, az USA nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és Nyugat-RÉGIÓJA.
**Tárolás típusa** | Ezzel a tulajdonsággal adhatja meg, hogy milyen típusú lemezeket szeretne áthelyezni az Azure-ban. <br/><br/> A helyszíni méretezéshez a célként megadott tárolási típust prémium szintű felügyelt lemezként, standard SSD felügyelt lemezként vagy standard HDD által felügyelt lemezként is megadhatja. A teljesítmény-alapú méretezéshez megadhatja a céllemez típusát automatikus, prémium szintű felügyelt lemezként, standard HDD felügyelt lemezként vagy standard SSD által felügyelt lemezként.<br/><br/> Ha a tárolási típust automatikusként adja meg, a lemezre vonatkozó javaslat a lemezek teljesítményi adatai (IOPS és átviteli sebesség) alapján történik. Ha a tárterületet prémium/standard szintűként adja meg, akkor az értékelés a kiválasztott tárolási típuson belül egy lemez SKU-t javasol. Ha az Egypéldányos VM 99,9%-os SLA-t kívánja elérni, érdemes lehet a tárolási típust prémium szintű felügyelt lemezként megadni. Ez biztosítja, hogy az értékelés összes lemeze prémium szintű felügyelt lemezként legyen ajánlott. Vegye figyelembe, hogy az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
**Fenntartott példányok (RI)** | Ennek a tulajdonságnak a segítségével megadhatja, hogy az Azure-ban [foglalt példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) rendelkeznek-e, majd az értékelésben szereplő KÖLTSÉGBECSLÉS az ri-kedvezményekbe kerül. A fenntartott példányok jelenleg csak az utólagos elszámolású ajánlatokhoz használhatók Azure Migrateban.
**Méretezési feltétel** | Az Azure-hoz megfelelő méretű virtuális gépekhez használandó feltétel. A teljesítmény *-alapú* méretezés vagy a virtuális gépek mérete a *helyszínen*is végezhető, a teljesítmény előzményeinek figyelembevétele nélkül.
**Teljesítményelőzmények** | A gépek teljesítményi adatai értékelésének időtartama. Ez a tulajdonság csak akkor alkalmazható, ha a méretezési feltétel *teljesítmény-alapú*.
**Százalékos kihasználtság** | A teljesítményminta-halmaz megfelelő méretezéshez figyelembe veendő százalékos értéke. Ez a tulajdonság csak akkor alkalmazható, ha a méretezés *teljesítmény-alapú*.
**Virtuálisgép-sorozatok** |     Megadhatja, hogy melyik virtuálisgép-sorozatot szeretné figyelembe venni a megfelelő méretezéshez. Ha például olyan éles környezettel rendelkezik, amelyet nem szeretne áttelepíteni az Azure-beli sorozatú virtuális gépekre, kizárhatja a-sorozatokat a listából vagy adatsorozatból, és a jobb oldali méretezés csak a kiválasztott adatsorozatban végezhető el.
**Kényelmi faktor** | Azure Migrate kiszolgáló értékelése az értékelés során egy puffert (komfort faktor) tekint. Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2\.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha a virtuális gépek nem fognak nonstop futni az Azure-ban, megadhatja az időtartamot (a havonta megjelenő napok számát és a napi óraszámot), és ennek megfelelően elvégezheti a költségbecslést.<br/> Az alapértelmezett érték havi 31 nap, és naponta 24 óra.
**Azure Hybrid Benefit** | Megadhatja, hogy rendelkezik-e frissítési garanciával, és jogosult-e a [Azure Hybrid Benefitre](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. Az alapértelmezett érték az Igen.

## <a name="how-are-assessments-calculated"></a>Hogyan számítják ki az értékeléseket?

Az Azure Migrate Server Assessment értékelését a helyszíni kiszolgálókkal gyűjtött metaadatok alapján számítjuk ki. Az értékelés kiszámítása három lépésben történik; az értékelési számítás az egyes kiszolgálók esetében az Azure-megfelelőségi elemzéssel kezdődik, majd a méretezés, végül pedig a havi költségbecslés. A kiszolgálók csak akkor haladnak át egy későbbi fázisban, ha az előzőre kerül. Ha például egy kiszolgáló meghibásodik az Azure alkalmassági ellenőrzésének, az nem megfelelőként van megjelölve az Azure-hoz, és a méretezés és a költségszámítás nem történik meg ugyanezen.

## <a name="azure-suitability-analysis"></a>Azure-megfelelőségi elemzés

Nem minden gép alkalmas az Azure-ban való futtatásra. Azure Migrate kiszolgáló értékelése az Azure-ba való áttelepítéshez az egyes helyszíni gépeket értékeli ki, és kategorizálja az értékelt gépeket a következő alkalmassági kategóriák valamelyikébe:
- **Készen áll az Azure-ra** – a gép a következőképpen telepíthető át az Azure-ba: módosítás nélkül. Az Azure-ban teljes körű Azure-támogatással fog indulni.
- **Feltételesen készen áll az Azure-ra** – a gép az Azure-ban is elindítható, de nem rendelkezik teljes körű Azure-támogatással. Például a Windows Server operációs rendszer korábbi verzióját futtató gépek nem támogatottak az Azure-ban. A gépek Azure-ba történő áttelepítése előtt körültekintően kell eljárnia, és az értékelés során javasolt szervizelési útmutatást követve javítsa ki az áttelepítés előtt felkészültségi problémákat.
- **Nem áll készen az Azure-ra** – a gép nem fog elindulni az Azure-ban. Ha például egy helyszíni gép mérete meghaladja a 64 TB-nál nagyobb méretű lemezt, az nem üzemeltethető az Azure-ban. Az Azure-ba való Migrálás előtt az értékelés során javasolt szervizelési útmutatót kell követnie a készültségi probléma kijavításához. Az Azure-ra nem készként megjelölt gépek esetében nem végezhető el a jobb méretezés és a költségbecslés.
- A **készültség ismeretlen** – Azure Migrate nem találta meg a gép készültségét, mert nem áll rendelkezésre elegendő, a helyszíni környezetből gyűjtött metaadatok.

Azure Migrate Server Assessment a számítógép tulajdonságait és a vendég operációs rendszert a helyszíni gép Azure-készültségének azonosítására tekinti át.

### <a name="machine-properties"></a>Számítógép tulajdonságai

A kiszolgáló értékelése a helyszíni virtuális gép alábbi tulajdonságait tekinti át annak azonosítására, hogy futtatható-e az Azure-ban.

**Tulajdonság** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure a rendszerindítási típusú virtuális gépeket BIOS-ként, nem pedig UEFI-ként támogatja. | Feltételesen üzemkész, ha a rendszerindítás UEFI típusú.
**Mag** | A gépekben lévő magok számának egyenlőnek vagy kisebbnek kell lennie, mint az Azure-beli virtuális gép által támogatott magok maximális száma (128 mag).<br/><br/> Ha rendelkezésre áll a teljesítmény előzményei, Azure Migrate az összehasonlításhoz a felhasznált magokat veszi figyelembe. Ha az értékelési beállításokban meg van adva egy kényelmi tényező, a kihasználatlan magok számát a Comfort faktor megszorozza.<br/><br/> Ha nincsenek teljesítménybeli előzmények, Azure Migrate a lefoglalt magokat használja a komfort tényező alkalmazása nélkül. | Ha a korlát értéke kisebb vagy egyenlő, akkor készen áll.
**Memória** | A számítógép memóriájának mérete nem lehet kisebb, mint az Azure-beli virtuális gép számára engedélyezett maximális memória (3892&nbsp;GB az Azure M Series Standard_M128m<sup>2</sup>esetében). [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha elérhetők a teljesítmény előzményei, Azure Migrate az összehasonlításhoz használt memóriát veszi figyelembe. Ha meg van adva egy kényelmi tényező, a kihasznált memóriát a komfort tényező szorozza meg.<br/><br/> Ha nincs előzmény, a rendszer a lefoglalt memóriát használja a komfort tényező alkalmazása nélkül.<br/><br/> | A határértékeken belül készen áll.
**Storage-lemez** | A lemez lefoglalt méretének 32 TB TÁRTERÜLETET vagy annál kisebbnek kell lennie.<br/><br/> A géphez csatolt lemezek számának 65 vagy annál kisebbnek kell lennie, beleértve az operációsrendszer-lemezt is. | A határértékeken belül készen áll.
**Hálózat** | A számítógépnek 32 vagy annál kevesebb hálózati adapterrel kell rendelkeznie. | A határértékeken belül készen áll.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
A virtuális gép tulajdonságai mellett Azure Migrate Server Assessment a gépek vendég operációs rendszerét vizsgálja, hogy megtudja-e futtatni az Azure-on.

> [!NOTE]
> A VMware virtuális gépek esetében Azure Migrate Server Assessment a virtuális géphez megadott operációs rendszert használja vCenter Server a vendég operációs rendszer elemzéséhez. A VMware-en futó Linux rendszerű virtuális gépek esetében jelenleg nem azonosítja a vendég operációs rendszer pontos kernel-verzióját.

Az Azure Migrate Server Assessment a következő logikát használja az Azure-készültség azonosítására az operációs rendszer alapján.

**Operációs rendszer** | **Részletek** | **Azure-készültségi állapot**
--- | --- | ---
Windows Server 2016 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2012 & összes SPs | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 minden SPs-vel | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-beli használatra
Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
Windows Server 2003, 2003 R2 | Ezek az operációs rendszerek átadták a támogatási dátumot, és [egyéni támogatási szerződést (CSA)](https://aka.ms/WSosstatement) igényelnek az Azure támogatásához. | Feltételesen készen áll az Azure használatára, érdemes lehet frissíteni az operációs rendszert az Azure-ba való Migrálás előtt.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Ezek az operációs rendszerek átadták a támogatási dátumot, a gép elindíthatja az Azure-ban, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ra feltételesen készen áll, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Windows-ügyfél 7, 8 és 10 | Az Azure csak a [Visual Studio](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) -előfizetéshez nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali verzió | Az Azure támogatást nyújt a több- [bérlős üzemeltetési jogosultságokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Ezek az operációs rendszerek átadták a támogatási dátumot, a gép elindíthatja az Azure-ban, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ra feltételesen készen áll, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek is elindíthatók az Azure-ban, de javasoljuk, hogy az Azure-ba való Migrálás előtt frissítse az operációs rendszert egy támogatott verzióra. | Készen áll az Azure-ra, ha a verziót jóváhagyták.<br/><br/>Feltételesen üzemkész, ha a verzió nincs támogatva.
Egyéb operációs rendszerek<br/><br/> Például: Oracle Solaris, Apple Mac OS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. A gép elindítható az Azure-ban, de az Azure nem biztosít operációsrendszer-támogatást. | Az Azure-ban feltételesen készen áll arra, hogy az Azure-ba való Migrálás előtt telepítsen egy támogatott operációs rendszert.  
Az operációs rendszer  vCenter Serverként van megadva | A Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készültség. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | Előfordulhat, hogy a gép az Azure-ban indul el, de az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure használatára, érdemes lehet a gép operációs rendszerét 32 bites operációs rendszerről 64 bites operációs rendszerre frissíteni az Azure-ba való Migrálás előtt.

## <a name="sizing"></a>Méretezés

Miután egy gép az Azure-ra készként van megjelölve, a kiszolgáló értékelése a méretezést végzi, ami magában foglalja a megfelelő Azure-beli virtuális gép és SKU azonosítását a helyszíni virtuális géphez. A méretre vonatkozó javaslatok a megadott értékelési tulajdonságoktól függően változnak.

- Ha az értékelés *teljesítmény-alapú méretezést*használ, Azure Migrate figyelembe veszi a gép teljesítményének előzményeit, hogy azonosítsa a virtuális gép méretét és típusát az Azure-ban. Ez a módszer különösen akkor hasznos, ha felhasználta a helyszíni virtuális gépet, de a kihasználtság alacsony, és az Azure-ban a virtuális gép jobb méretét szeretné megtakarítani a költségek megtakarítása érdekében. Ez a módszer segít optimalizálni a méreteket az áttelepítés során.
- Ha nem szeretné figyelembe venni a virtuális gép méretezéséhez szükséges teljesítményadatokat, és a helyszíni gépeket az Azure-ba szeretné használni, megadhatja a méretezési feltételt, mint *a* helyszíni és a kiszolgáló értékelése, majd a helyszíni környezet alapján méretezi a virtuális gépeket. a konfigurációt a kihasználtsági adatai mérlegelése nélkül. A lemez méretezése – ebben az esetben – az értékelés tulajdonságaiban megadott tárolási típus (standard HDD lemezek, standard SSD lemezek vagy prémium lemezek) alapján lesz végrehajtva.

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezés

A teljesítmény-alapú méretezéshez Azure Migrate kiszolgáló értékelése a virtuális géphez csatolt lemezekkel kezdődik, majd a hálózati adapterek, majd a helyszíni virtuális gép számítási követelményei alapján leképezi az Azure-beli virtuálisgép-SKU-t. A Azure Migrate berendezés a helyszíni környezetet a CPU, a memória, a lemezek és a hálózati adapter teljesítményadatait gyűjti.

**Teljesítményadatok gyűjtése**

- A VMware virtuális gépek esetében a Azure Migrate berendezés minden 20 másodperces intervallumban gyűjt valós idejű mintát, a Hyper-V virtuális gépek esetében a valós idejű mintavételi pontot minden 30 másodperces intervallumban gyűjti.
- A berendezés ezután összesíti a 10 percenként összegyűjtött mintavételi pontokat, és az elmúlt 10 percben a maximális értéket küldi el Azure Migrate kiszolgáló értékeléséhez.
- Azure Migrate Server Assessment az utolsó egy hónap 10 perces mintavételi pontját tárolja, és a *teljesítmény előzményeihez* és a *percentilis kihasználtságához*megadott értékelési tulajdonságoktól függően azonosítja a megfelelő adatpontot. ezt a megfelelő méretezéshez kell használni. Ha például a teljesítmény előzményei egy napra vannak beállítva, és a percentilis kihasználtsága 95. percentilis, az elmúlt egy nap 10 perces mintavételi pontját használja, növekvő sorrendbe rendezi őket, és kiválasztja a 95. percentilis értékét a jobb méretezés érdekében.
- A fenti értéket ezután megszorozza a komforttal, hogy megkapja az egyes mérőszámok hatékony teljesítmény-kihasználtsági adatait (CPU-kihasználtság, memória kihasználtsága, lemez IOPS (olvasási és írási), lemez átviteli sebessége (olvasási és írási), hálózati átviteli sebesség (be-és kimenő)) a készülék gyűjti.
- A tényleges kihasználtsági érték azonosítása után a számítási, tárolási és hálózati méretezés a következőképpen történik:

**Tároló méretezése**: Azure Migrate megkísérli leképezni a géphez csatolt összes lemezt az Azure-beli lemezre.

> [!NOTE]
> Azure Migrate: A kiszolgáló értékelése csak a felügyelt lemezeket támogatja az értékeléshez.

  - A lemez olvasási és írási IOPS hozzá kell adni a teljes IOPS szükséges, hasonlóan olvasási és írási átviteli sebességi értékekkel az egyes lemezek teljes átviteli sebességének lekéréséhez.
  - Ha a tárolási típust automatikus értékre állította, a IOPS és az átviteli sebesség értéke alapján Azure Migrate kiszolgáló értékelése ezt követően azonosítja, hogy a lemezt szabványos HDD-re, standard SSD-re vagy prémium szintű lemezre kell-e leképezni az Azure-ban. Ha a tárolási típus értéke standard HDD/SSD/Premium, a rendszer megpróbál a kiválasztott tárolási típuson belül található SKU-t keresni (standard HDD/SSD/prémium lemez).
  - Ha a kiszolgáló értékelése nem talál a szükséges IOPS & átviteli sebességű lemezt, az az Azure számára nem megfelelőként jelöli meg a gépet.
  - Ha megfelelő lemezeket talál, akkor kiválasztja azokat, amelyek támogatják az értékelési beállításokban megadott helyet.
  - Ha több jogosult lemez van, akkor a legalacsonyabb költséggel jelöli ki az egyiket.
  - Ha valamelyik lemez teljesítményadatokat nem érhető el, a lemez konfigurációs adatai (lemez mérete) az Azure-ban szabványos SSD-lemez megtalálására szolgálnak.

**Hálózati méretezés**: Azure Migrate Server Assessment megpróbál olyan Azure-beli virtuális gépet találni, amely képes támogatni a helyszíni géphez csatlakoztatott hálózati adapterek számát és a hálózati adapterek által igényelt teljesítményt.
    - A helyszíni virtuális gép hatékony hálózati teljesítményének lekéréséhez a kiszolgáló értékelése összesíti a másodpercenként továbbított adatokat (MB/s) a gépen (kimenő hálózat), az összes hálózati adapteren, és alkalmazza a komfort tényezőt. Ez a szám egy olyan Azure-beli virtuális gép megtalálására szolgál, amely képes támogatni a szükséges hálózati teljesítményt.
    - A hálózati teljesítmény mellett azt is figyelembe kell venni, hogy az Azure-beli virtuális gép támogatja-e a hálózati adapterek számát.
    - Ha nem érhető el hálózati teljesítményadatok, a rendszer csak a hálózati adapterek darabszámát veszi figyelembe a virtuális gépek méretének növeléséhez.

**Számítási méretezés**: A tárolási és hálózati követelmények kiszámítása után Azure Migrate Server Assessment a CPU-és memória-követelményeket tekinti meg a megfelelő virtuálisgép-méret megtalálásához az Azure-ban.
    - Azure Migrate az Azure-ban egy megfelelő virtuálisgép-méret megtalálását vizsgálja a ténylegesen felhasznált magok és memória alapján.
    - Ha nem található megfelelő méret, a gép nem megfelelőként van megjelölve az Azure-hoz.
    - Ha talál megfelelő méretet, Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután alkalmazza a hely és az árképzési szintek beállításait a virtuális gép végső méretére vonatkozó javaslatra.
    - Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezés

Ha helyszíni méretezést használ, a kiszolgáló értékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményének előzményeit, és a helyszínen lefoglalt méret alapján kioszt egy VIRTUÁLISGÉP-SKU-t az Azure-ban. A lemezek méretezéséhez hasonlóan az értékelés tulajdonságainál (standard HDD/SSD/Premium) megadott tárolási típus jelenik meg, és ennek megfelelően javasolja a lemez típusát. Az alapértelmezett tárolási típus a prémium szintű lemezek.

## <a name="confidence-ratings"></a>Megbízhatósági minősítések
Azure Migrate minden teljesítmény-alapú értékelése egy olyan megbízhatósági minősítéssel van társítva, amely egy (a legalacsonyabb) és öt közötti érték (a legmagasabb) közötti tartományban van.
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- A megbízhatósági minősítés nem alkalmazható a helyszíni értékelésekhez.
- A teljesítmény-alapú méretezéshez Azure Migrate Server Assessment szükséges:
    - A processzor kihasználtsági adatai és a virtuális gép memóriája.
    - Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan.
    - Hasonlóképpen a virtuális géphez csatlakoztatott összes hálózati adapterhez hasonlóan a megbízhatósági minősítésnek a hálózati I/O-t kell tennie a teljesítmény-alapú méretezés érdekében.
    - Ha a fenti kihasználtsági számok bármelyike vCenter Serverban nem érhető el, előfordulhat, hogy a méretre vonatkozó javaslat nem megbízható.

Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%-40% | 2 csillag
   41%-60% | 3 csillag
   61%-80% | 4 csillag
   81%-100% | 5 csillag

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések

Néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem tudta felmérni a környezetét arra az időtartamra, amelyhez az értékelést létrehozza. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy napig várnia kell, hogy az adatgyűjtés megtörténjen.
- Néhány virtuális gép le lett állítva az értékelés kiszámításának időtartama alatt. Ha bármely virtuális gép ki van kapcsolva bizonyos időtartamra, Azure Migrate kiszolgáló értékelése nem tudja gyűjteni az adott időszak teljesítményadatait.
- Néhány virtuális gép az értékelés kiszámításának időtartama alatt lett létrehozva. Ha például az utolsó egy hónap teljesítmény-előzményeire vonatkozó értékelést hoz létre, de néhány virtuális gép csak egy héttel ezelőtt lett létrehozva a környezetben, az új virtuális gépek teljesítmény-előzményei nem lesznek ott a teljes időtartamra.

> [!NOTE]
> Ha az értékelés megbízhatósági minősítése öt csillag alatt van, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felkeresi a környezetet, majd számítsa ki újra az értékelést. Ha nem, akkor előfordulhat, hogy a teljesítmény-alapú méretezés nem megbízható, és javasoljuk, hogy az értékelést helyszíni méretezésként használja.

## <a name="monthly-cost-estimation"></a>Havi költségbecslés

A méretezési javaslatok befejezését követően a Azure Migrate kiszámítja a számítási és tárolási költségeket az áttelepítés után.

- **Számítási díj**: Az ajánlott Azure-beli virtuális gép méretének használatával a Azure Migrate a számlázási API-val számítja ki a virtuális gép havi költségét.
    - A számítás az operációs rendszer, a frissítési garancia, a fenntartott példányok, a virtuális gép üzemidő, a hely és a pénznem beállításait veszi figyelembe.
    - A teljes havi számítási költség kiszámításához összesíti a költségeket az összes gépen.
- **Tárolási díj**: A gép havi tárolási költségeit a rendszer a géphez csatlakoztatott összes lemez havi költségének összesítésével számítja ki.
    - Azure Migrate kiszolgáló értékelése a teljes havi tárolási költséget számítja ki az összes gép tárolási költségeinek összesítésével.
    - A számítás jelenleg nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.


## <a name="next-steps"></a>További lépések

Hozzon létre egy értékelést a [VMWare virtuális gépek](tutorial-assess-vmware.md) vagy a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)számára.
