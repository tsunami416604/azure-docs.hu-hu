---
title: Értékelési számítások az Azure Migrate |} A Microsoft Docs
description: Értékelési számítások az Azure Migrate szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: a328549307772cbdf470160cc1ad713fe1ee5e05
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805986"
---
# <a name="assessment-calculations"></a>Értékelési számítások

Az Azure Migrate Server Assessment felméri a helyszíni számítási feladatokat az Azure-ba való migrálásra. Ez a cikk ismerteti, értékelések számításával.


[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és számítási feladatok és példányok privát és nyilvános felhő, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.

## <a name="overview"></a>Áttekintés

Az Azure Migrate Server Assessment értékelés három fázisa van. Értékelés egy alkalmasságát elemzés, a méretezés, kiegészítve előtaggal kezdődik, és végül egy-egy havi költségbecsléshez. A gép csak áthelyezi egy későbbi szakaszban Ha átadja az előzőre. Például egy gép az Azure-megfelelőségi ellenőrzés meghiúsul, ha meg van jelölve, nem alkalmasak az Azure, és a méretezési és költségszámítási nem történik meg.


## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/> Az Azure Migrate jelenleg ezek célrégiók támogatja: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Kanada közép-India, kelet-Kanada, közép-India, USA középső RÉGIÓJA, kelet-Kína, Észak-Kína, Kelet-Ázsia, USA keleti RÉGIÓJA, 2 USA keleti régiója, közép-India, Németország északkelet-Németország, japán keleti régiójában, Nyugat-japán, Korea középső régiója, Korea déli régiója, Észak USA középső RÉGIÓJA, Észak-Európa, USA déli középső RÉGIÓJA, Délkelet-Ázsia, Dél-India, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója, USA beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, USA-beli államigazgatás – Virginia, USA nyugati középső RÉGIÓJA, Nyugat-Európa, Nyugat-India, USA nyugati RÉGIÓJA és USA 2. nyugati régiója.<br/> Az alapértelmezetten beállított célrégió az USA 2. nyugati régiója.
**Tárolás típusa** | Standard HHD lemezek/Standard SSD lemez/Premium.<br/><br/> A tárolási típust tartalmaz egy értékelés automatikus ad meg, ha a lemez javaslatot a teljesítményadatokat a lemezek (IOPS és átviteli sebesség) alapul.<br/><br/> Ha prémium vagy Standard a tárolási típust ad meg, az értékelés javasolni a felhasználóknak egy lemezt Termékváltozat belül a tárolási típust kiválasztva.<br/><br/> Ha azt szeretné, 99,9 %-os virtuális gép SLA-t egyetlen példány eléréséhez, beállíthatja a tárolási típust, prémium szintű managed disks. Az értékelésben az összes lemez majd javasolni, prémium szintű managed disks. <br/> Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.<br/> 
**Reserved Instances (RI)** | Adja meg ezt a tulajdonságot, ha Ön fenntartott példányok az Azure-ban. Költség becsléseket az értékelés során figyelembe kell vennie a fenntartott példányok kedvezmények. A fenntartott példányok jelenleg csak akkor támogatott, a használatalapú fizetéssel az Azure Migrate kínál.
**Méretezési feltétel** | Jobb méretű virtuális gépek segítségével. Méretezési lehet a teljesítmény-alapú, vagy a következőképpen – a helyszínen, teljesítményelőzmények figyelembe vétele nélkül.
**Teljesítményelőzmények** | A virtuális gépek teljesítményének értékelésekor figyelembe veendő időtartam. Ez a tulajdonság csak akkor alkalmazható, teljesítményalapú méretezés esetén.
**Százalékos kihasználtság** | A teljesítmény-minta a megfelelő virtuális gépekhez használt PERCENTILIS értéke. Ez a tulajdonság csak akkor alkalmazható, teljesítményalapú méretezés esetén.
**Virtuálisgép-sorozatok** | Méretbecslésekhez használt virtuálisgép-sorozatok. Például egy olyan éles környezetben, amelyet nem szeretne A-sorozatú virtuális gépekre migrálni az Azure-ban, kizárhatja a listából vagy sorozatból az A-sorozatot. Ilyenkor a méretezés csak a kiválasztott sorozatokat veszi figyelembe.
**Kényelmi faktor** | Az Azure Migrate Server Assessment értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2\.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Pénznem** | A számlázás pénzneme. 
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények.<br/> Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha nem tervezi a virtuális gépek 24 x 7 fut az Azure-ban, amely azok kellene futnia adhat meg az időtartamot (havonta napok száma) és napi óraszám, és a költségbecslések elkészítését ennek megfelelően kell végrehajtania.<br/> Az alapértelmezett értéke 31 nap / hó és napi 24 órában.
**Azure Hybrid Benefit** | Itt adhatja meg e rendelkezik frissítési garanciával, és jogosult [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Ha az Igen értéket állítja be, a nem Microsoft Azure-alapú árazás érvényes a windowsos virtuális gépekre. 



## <a name="azure-suitability-analysis"></a>Azure-megfelelőségi elemzés

Nem minden gépek kiválóan alkalmasak az Azure-ban. Az Azure Migrate Server Assessment értékeli az egyes helyszíni gépek migrálásra, és kategorizálja a gépek a következő alkalmasságát kategóriák valamelyikébe:
- **Azure-beli használatra kész** – a gép is telepíthető-módosítás nélkül az Azure-ban van. Teljes Azure-támogatással az Azure-ban indul.
- **Feltételesen készen áll az Azure-ban** – a gép elindulhat az Azure-ban, de nem rendelkezik teljes körű Azure-támogatás. Például olyan gépeken, Windows Server operációs rendszer régebbi verziója nem támogatott az Azure-ban. Ezek a gépek Azure-ba való migrálás előtt legyen óvatos, és kövesse a szervizelési az értékelésben készültségi problémák megoldásához áttelepítése előtt ajánlott útmutatás szerint kell.
- **Nem áll készen az Azure-ban** – a gép nem indul el az Azure-ban. Például ha egy helyszíni gép méretű több mint 4 TB-os csatlakoztatott lemez rendelkezik, akkor nem futhat az Azure-ban. Az értékelésben a készültségi probléma megoldásához az Azure-ba való migrálás előtt javasolt szervizelés kövessék kell. Megfelelő méretezéséhez és költségbecslés nem történik a gépekhez, amely nem áll készen az Azure-hoz vannak megjelölve.
- **A kompatibilitás ismeretlen** – Azure Migrate nem található a gépen, mert nincs elég adat érhető el a vCenter Server készen áll-e.



### <a name="machine-properties"></a>Virtuálisgép-tulajdonságokat

Az Azure Migrate áttekinti a következő tulajdonságok azonosítani, hogy az Azure-ban futtatható-e a helyszíni virtuális gép.

**Tulajdonság** | **Részletek** | **Azure-kompatibilitás állapota**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure virtuális gépek a BIOS-ban, és nem UEFI típusú rendszerindítást támogatja. | Feltételesen készen áll arra, ha UEFI típusú rendszerindítást.
**Processzormagok** | A magok számát a a gépek mag (128 mag), egy Azure virtuális gép támogatott maximális számának kisebbnek vagy azzal egyenlőnek kell lennie.<br/><br/> Teljesítményelőzmények érhető el, ha az Azure Migrate figyelembe veszi a túl magas kihasználtsággal rendelkező magok, az összehasonlítást. Ha egy kompatibilitási faktor értékelés beállításaiban van megadva, a rendszer megszorozza a túl magas kihasználtsággal rendelkező magok számát a kompatibilitási faktor.<br/><br/> Ha nincsenek teljesítményelőzményei, a kompatibilitási faktor alkalmazása nélkül az Azure Migrate használja a lefoglalt magok száma. | Készen áll, ha kevesebb mint vagy egyenlő korlátok.
**Memória** | A memória mérete kisebb, mint a maximális memóriát, vagy azzal egyenlőnek kell lennie. (az Azure M sorozatú Standard_M128m 3892 GB&nbsp;<sup>2</sup>) egy Azure virtuális gép engedélyezett. [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Teljesítményelőzmények érhető el, ha az Azure Migrate figyelembe veszi a magas kihasználtságú memóriát, az összehasonlítást. Ha egy kompatibilitási faktor van megadva, a rendszer megszorozza a magas kihasználtságú memória a kompatibilitási faktor.<br/><br/> Ha nincsenek előzményei a lefoglalt memória használata esetén a kompatibilitási faktor alkalmazása nélkül.<br/><br/> | Készen áll a korlátokon belül.
**Tárolólemez** | A lemez lefoglalt mérete 4 TB-ig (4096 GB) kell lennie, vagy kisebb.<br/><br/> A géphez csatolt lemezek száma 65 vagy annál kisebb, többek között az operációsrendszer-lemez kell lennie. | Készen áll a korlátokon belül.
**Hálózat** | Egy gép 32 kell rendelkeznie, vagy kevesebb hálózati adapter csatlakozik. | Készen áll a korlátokon belül.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
Virtuális gép tulajdonságait, valamint az Azure Migrate Server Assessment megvizsgálja a vendég operációs rendszer a gépek annak megállapítására, hogy az Azure-ban futtatható.

> [!NOTE]
> Az Azure Migrate Server Assessment a virtuális Géphez a vCenter Server elemzés céljából megadott operációs rendszert használ. Az Azure Migrate Server Assessment a virtuális gépek felderítésének készülék-alapú, és azt nem lehet ellenőrizni, hogy a virtuális gépen futó operációs rendszer ugyanaz, mint a megadott a vCenter Serverben.

Az alábbi logika szerint az Azure Migrate Server Assessment azonosítására szolgál az operációs rendszer alapján Azure-kompatibilitását.

**Operációs rendszer** | **Részletek** | **Azure-kompatibilitás állapota**
--- | --- | ---
A Windows Server 2016 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2012 R2 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2012 és az összes szervizcsomagok | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2008 R2 minden szervizcsomagok | Az Azure teljes körű támogatást biztosít.| Készen áll az Azure-beli használatra
A Windows Server 2008 (32 bites és 64 bites) | Az Azure teljes körű támogatást biztosít. | Készen áll az Azure-beli használatra
A Windows Server 2003, Server 2003 R2 | Ezek az operációs rendszerek megfeleltek támogatása, és meg kell azok végén egy [egyéni támogatási megállapodás (CSA)](https://aka.ms/WSosstatement) támogatás az Azure-ban. | Feltételesen készen áll az Azure-hoz, fontolja meg az operációs rendszer frissítését az Azure-ba való migrálás előtt.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Az említett operációs rendszerektől azok befejezési támogatása estek át, a gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasolt az Azure-ba való migrálás előtt az operációs rendszer verziófrissítéséhez.
Windows-ügyfél 7, 8 és 10 | Az Azure-támogatást kínál [csak a Visual Studio-előfizetéssel.](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztali | Az Azure-támogatást kínál [több-Bérlős üzemeltetési jogosultságok.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Az említett operációs rendszerektől azok befejezési támogatása estek át, a gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasolt az Azure-ba való migrálás előtt az operációs rendszer verziófrissítéséhez.
Linux | Azure vannak jóváhagyva, ezek [Linux operációs rendszerek](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek elindulhat az Azure-ban, de javasoljuk, hogy az operációs rendszer frissítése egy támogatott verzióra az Azure-ba való migrálás előtt. | Készen áll az Azure-hoz, ha a verzió támogatja.<br/><br/>Feltételesen készen áll arra, ha a verzió nem támogatja.
Más operációs rendszerek<br/><br/> Példa: Oracle Solaris, az Apple Mac OS stb., a FreeBSD stb. | Az Azure nem támogat nyíltan az említett operációs rendszerektől. A gép elindulhat az Azure-ban, de nem az operációs rendszer biztosít támogatást az Azure-ban. | Feltételesen készen az Azure-hoz, javasoljuk, hogy egy támogatott operációs rendszer telepítése az Azure-ba való migrálás előtt.  
Az operációs rendszer megadott **más** a vCenter Serverben | Az Azure Migrate ebben az esetben az operációs rendszer nem tudja azonosítani. | Ismeretlen készültségi. Győződjön meg arról, hogy a virtuális gépen futó operációs rendszer támogatott az Azure-ban.
32 bites operációs rendszerek | A gép elindulhat az Azure-ban, de az Azure nem rendelkezhetnek teljes körű támogatást. | Feltételesen készen áll az Azure-hoz, érdemes lehet frissíteni a gép operációs rendszerének 32 bites operációs Rendszereken a 64 bites operációs Rendszereken az Azure-ba való migrálás előtt.

## <a name="sizing"></a>Méretezés

Után a gép van megjelölve, készen áll az Azure-hoz, az Azure Migrate úgy méretezi a virtuális gép és annak lemezeire az Azure-hoz.

- Ha az értékelés teljesítményalapú használ, az Azure Migrate teljesítményelőzményeit a gép azonosításához az Azure-beli virtuális gép méretét és a lemez típusa. Ez a módszer különösen hasznos, ha a helyszíni virtuális Gépet, hogy hozzárendelte túlterhelt, de a kihasználtság alacsony, és szeretné méretezése az Azure-ban költségek csökkentése érdekében a virtuális Gépet.
- Ha Ön használata egy, a helyszíni értékelési, az Azure Migrate Server Assessment fog méretezés a virtuális gépek, a helyszíni-beállításai alapján a kihasználtsági adatok figyelembe vétele nélkül. Lemez átméretezése, ebben az esetben alapul a tárolótípus (standard szintű lemezes vagy prémium szintű lemezt) az értékelés tulajdonságaiban megadott.

### <a name="performance-based-sizing"></a>Teljesítményalapú

Teljesítményalapú méretezéshez az Azure Migrate a a virtuális gép, hálózati adapterek, kiegészítve a csatolt lemezek kezdődik, és ezután maps egy Azure virtuális gép számítási követelményei alapján a helyszíni virtuális gép.

- **Tárolási**: Az Azure Migrate megkísérli minden, a gép az Azure-ban lemez csatlakoztatott lemez.
    - A hatékony lemez i/o másodpercenként (IOPS) és az átviteli sebesség (MB/s) lekéréséhez az Azure Migrate szorozza meg a lemez iops-t és az átviteli sebességet a kompatibilitási faktor együtt. A hatékony IOPS és átviteli sebesség értékek alapján, ha a lemez le kell képezni egy standard vagy prémium szintű lemezt az Azure-ban az Azure Migrate azonosítja.
    - Az Azure Migrate nem található a szükséges iops-t és az átviteli sebesség lemezzel, ha azt nem alkalmasak a géphez jelöli meg az Azure-hoz. [További](../azure-subscription-service-limits.md#storage-limits) Azure-ral kapcsolatos korlátozza a lemez és a virtuális gépek száma.
    - Ha megtalálta a megfelelő lemezek egy készlete, az Azure Migrate kiválasztja azokat, amelyek támogatják a storage-redundancia módját, valamint az értékelési beállítások megadott helyen.
    - Ha több jogosult lemezek vannak, a legalacsonyabb költséget rendelkezőt választja ki.
    - Teljesítményadatok lemez nem érhető el, ha az összes lemez az Azure standard szintű lemezek vannak leképezve.

- **Hálózati**: Az Azure Migrate megpróbálja megkeresni egy Azure virtuális Gépen, hogy a helyi géphez csatlakoztatott hálózati adapterek és a teljesítmény, ezek a hálózati adapterek által igényelt támogatja.
    - A helyszíni virtuális gép a tényleges hálózati teljesítmény eléréséhez, az Azure Migrate összesíti az összes hálózati adapteren másodpercenként (MBps) kívül a géphez (hálózati ki), továbbított adatokat, és a kompatibilitási faktor vonatkozik. Ez a szám egy Azure virtuális gép támogatására képes a szükséges hálózati teljesítménynek kereséséhez használt.
    - Hálózati teljesítmény, valamint azt is figyelembe veszi az Azure virtuális gép támogatják-e a szükséges hálózati adapterek száma.
    - Ha nincs hálózati teljesítménnyel adatai nem érhető el, a csak a hálózati adapterek száma a Virtuálisgép-méretezési számít.

- **COMPUTE**: Tárolási és hálózati követelményeinek kiszámítása, miután az Azure Migrate figyelembe veszi a Processzor- és követelmények található Virtuálisgép-méret az Azure-ban.
    - Az Azure Migrate megvizsgálja a túl magas kihasználtsággal rendelkező magok és a memória, és a hatékony magok és a memória a kompatibilitási faktor vonatkozik. Adott száma alapján, azt próbál meg megtalálni egy Virtuálisgép-méret az Azure-ban.
    - Ha nem megfelelő méretű található, a gép van megjelölve nem alkalmasak az Azure-hoz.
    - Ha egy megfelelő méretű található, a tárolási és hálózatkezelési számítások az Azure Migrate vonatkozik. Ezt követően végrehajtja, helyét és a tarifacsomag-beállításokat, a virtuális gép mérete végleges javaslatot.
    - Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

### <a name="as-on-premises-sizing"></a>Helyszíni méretezése

A helyszíni méretezés használatakor Server Assessment lefoglal egy Virtuálisgép-Termékváltozatra a méret a helyszíni Azure-ban. Ehhez hasonlóan az lemez méretezés, azt a tárolótípus (Standard vagy prémium) értékelés tulajdonságaiban megadott megvizsgál, és javasolja a lemez típusát ennek megfelelően. Az alapértelmezett tárolási típus prémium szintű lemezeket.

## <a name="confidence-ratings"></a>Megbízhatósági minősítés
Az Azure Migrate minden teljesítményalapú értékelése olyan megbízhatósági minősítéssel, amely az egyik (legalacsonyabb) az öt kezdődik (legmagasabb) kapcsolódik.
- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.
- Megbízhatósági minősítés nem alkalmazható a helyszíni értékeléseket.
- Teljesítményalapú méretezéshez az Azure Migrate Server Assessment kell rendelkeznie:
    - A processzor, a kihasználtsági adatok és a virtuális gép memória.
    - Emellett szükség van a lemez IOPS-értékére és az adatátviteli teljesítményre is a virtuális géphez csatlakoztatott minden lemezre vonatkozóan.
    - Hasonló módon minden virtuális Géphez csatolt hálózati adapterhez, a megbízhatósági minősítés kell teljesítményalapú ehhez a hálózati I/O.
    - Ha a fenti kihasználtsági számok bármelyike nem érhető el a vCenter Server, a méret javaslat nem megbízható. 

Az elérhető adatpontok százalékától függően meg van adva a megbízhatósági minősítés az értékeléshez az alábbiak szerint:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%-40% | 2 csillag
   41%-60% | 3 csillag
   61%-80% | 4 csillag
   81%-100% | 5 csillag

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítés

Néhány példa az okokat, miért értékelés kérhetők le egy alacsony megbízhatósági minősítés:

- A környezet, amelynek létrehozásakor az értékelés ideje alatt nem profilt. Például ha hoz létre az értékelés 1 nap, a teljesítmény időtartamát, meg kell a legalább egy napot vár az összes adatponttal gyűjtött kigyűjtése a felderítés megkezdése után.
- Néhány virtuális gépen le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha olyan virtuális gépek van kapcsolva egy ideig, az Azure Migrate Server Assessment a teljesítményadatok nem az adott időszak gyűjtése.
- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Például ha hoz létre az utolsó egy hónap teljesítményelőzményeinek értékelését, de néhány virtuális gépen a környezetben létrehozott csak egy héttel ezelőtt, a új virtuális gépek korábbi teljesítménye nem fog van a teljes időtartamhoz.

  > [!NOTE]
  > Ha valamely értékelés megbízhatósági minősítése öt csillag alatt van, azt javasoljuk, hogy várjon legalább egy napot a berendezéshez, hogy a profil a környezet, és ezután számíthatja újra az értékelést. Ha nem, teljesítményalapú lehetséges, hogy nem megbízható, és azt javasoljuk, hogy váltson át a használatára, mert a helyszíni méretezésnél az értékelést, és.
  
## <a name="monthly-cost-estimation"></a>Havi Költségbecslés

Méretezési javaslatok befejezése után az áttelepítés után az Azure Migrate kiszámítja a számítási és tárolási költségeit.

- **Számítási költségeit tartalmazza**: Használja az Azure virtuális gép ajánlott mérete, az Azure Migrate használatával a számlázási API-t a havi költségek kiszámítása a virtuális gép.
    - A kiszámítása az operációs rendszer, frissítési garanciával rendelkező, a fenntartott példányok, virtuális gép Üzemidő, helye és pénznem beállítások figyelembe vesz igénybe.
    - A költségek gyűjti az összes gépen, a teljes havi számítási költség kiszámítása.
- **Tárolási költségek**: A havi tárolási gép költségszámítás összesítésével csatlakozik a gép összes lemezének havi költség
    - Az Azure Migrate Server Assessment alapján számítja ki a teljes havi tárolási költségeket összesíti az összes gép tárolási költségeit.
    - A számítási jelenleg nem használ a felmérés beállításaiban figyelembe ajánlatok.

Az értékelési beállítások megadott pénznem költségek jelenik meg.


## <a name="next-steps"></a>További lépések

Hozzon létre egy értékelést a [VMware virtuális gépek](tutorial-assess-vmware.md) vagy [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md).
