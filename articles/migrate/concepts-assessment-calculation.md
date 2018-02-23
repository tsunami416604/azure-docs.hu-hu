---
title: "Azure áttelepítése Assessment számításokban |} Microsoft Docs"
description: "Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: db09ff30ff9f3852e84162b8400572e76515230f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="assessment-calculations"></a>Értékelési számítások

[Az Azure áttelepítése](migrate-overview.md) értékelésére a helyszíni munkaterhelések Azure való áttelepítésre. Ez a cikk értékelése kiszámítási módját ismerteti.


## <a name="overview"></a>Áttekintés

Egy Azure áttelepítése-nek három fázisból áll. Értékelés méretezési, majd egy alkalmasságát elemzés kezdődik, és végül a havi költségeket becslése. A gép csak áthelyezése később, ha az előzőre adja át. Például a gép az Azure Alkalmazhatóság ellenőrzése sikertelen, ha meg van jelölve, céljaira nem használható Azure, és a méretezés és a költségszámítás nem történik meg. 


## <a name="azure-suitability-analysis"></a>Az Azure alkalmasságát elemzés

Nem minden gépek felhő rendelkezik saját korlátozások és a felhőben futó alkalmasak. Az Azure áttelepítése értékeli az egyes áttelepítési alkalmas az Azure-bA a helyi számítógépen, és a gépek kategorizálja a következő kategóriába oszthatók:
- **Készen áll az Azure-** -a gép áttelepíthető-módosítás nélkül Azure-hoz. Teljes Azure-támogatással rendelkező indul az Azure-ban.
- **Azure feltételes kész** -a gép előfordulhat, hogy indítsa el az Azure-ban, de nem rendelkezik teljes körű Azure-támogatás. Például olyan gépeken, amelyek a Windows Server operációs rendszer régebbi verziója nem támogatott az Azure-ban. Ezek a gépek áttelepítése az Azure-bA előtt ügyeljen, követve a szervizelés a során a készültségi problémáinak megoldásával kapcsolatban áttelepítése előtt javasolt kell.
- **Nem áll készen az Azure-** – a gép nem indul el az Azure-ban. Például ha egy a helyi számítógépen több, mint 4 TB-os nem csatlakoztatható ekkora lemezt, azt nem kell Azure-platformon futó. A során a a készültségi probléma az Azure-bA áttelepítése előtt javasolt szervizelési útmutatását kell. Megfelelő méretének kiválasztását és költségű becslés nem történik meg, amely nem áll készen az Azure-megjelölve gépek.
- **Ismeretlen készültségi** -Azure áttelepítése nem található a gép, mert nincs elég adat érhető el a vCenter Server készültségét. 

Ellenőrzi, hogy az Azure áttelepítése a virtuálisgép-tulajdonságokat és a vendég operációs rendszer a helyi gép Azure készültségét azonosításához.

### <a name="machine-properties"></a>Virtuálisgép-tulajdonságokat
Az Azure áttelepítése ellenőrzi, hogy a következő tulajdonságok a helyszíni virtuális gép annak megállapítására, hogy a virtuális gépek Azure is futtathatók.
 
**Tulajdonság** | **Részletek** | **Az Azure készültségi állapotát**
--- | --- | ---
**Indítási típus** | Azure virtuális gépek támogatja a BIOS, és nem UEFI rendszerindítási típusát. | Ha rendszerindító típusa UEFI feltételesen kész az Azure-bA. 
**Magok** | A gépek magok száma magok (32) maximális száma egy Azure virtuális gép támogatott kisebbnek vagy azzal egyenlőnek kell lennie.<br/><br/> Korábbi teljesítmény érhető el, ha az összehasonlításhoz használt magok Azure áttelepítése tekinti. Ha egy megerősítő tényező assessment beállításaiban van megadva, a rendszer megszorozza túlterhelt magok száma a megerősítő tényező.<br/><br/> Ha nincs teljesítményelőzményei, a megerősítő Authentication alkalmazása nélkül Azure áttelepítéséhez használja a lefoglalt magok. | Nem áll készen. Ha magok száma nagyobb, mint 32. 
**Memória** | A memória mérete kisebb, mint az Azure virtuális gép számára engedélyezett maximális memóriaméretet (448 GB), vagy azzal egyenlőnek kell lennie. <br/><br/> Korábbi teljesítmény érhető el, ha az összehasonlításhoz használt memória Azure áttelepítése tekinti. Ha egy megerősítő tényező van megadva, a rendszer megszorozza a használt memória a megerősítő tényező.<br/><br/> Ha nincsenek előzményei a lefoglalt memória használata esetén a megerősítő Authentication alkalmazása nélkül.<br/><br/> | Nem áll készen. Ha memória mérete 448 GB-nál nagyobb.
**Tároló lemez** | A lemez mérete lefoglalt kell lennie 4 TB-os (4096 GB) vagy kisebb.<br/><br/> A géphez csatolt lemezek száma 65 vagy kevesebb, beleértve az operációsrendszer-lemezképet kell lennie. | Nem áll készen. Ha a lemez mérete nagyobb, mint 4 TB-os, vagy ha több mint 65 a géphez csatlakoztatott lemezek vannak. 
**Hálózat** | A gép 32 kell lennie, vagy kevesebb hálózati adapter nem csatlakoztatható. | Nem áll készen. Ha a gép legfeljebb 32 hálózati adaptert

### <a name="guest-operating-system"></a>Vendég operációs rendszer 
Virtuális gép tulajdonságai, valamint is Azure áttelepítése megvizsgálja a vendég operációs rendszer, ha a virtuális gép futtatható Azure a helyszíni virtuális gép.

> [!NOTE]
> Azure áttelepítése úgy véli, hogy az operációs rendszer megadott a vCenter-kiszolgáló a következő elemzést. Mivel a felderítés Azure áttelepítése végezhető el készülék-alapú, nem rendelkezik egy módszerre, amellyel ellenőrizhető, hogy ugyanaz, mint egy a vCenter-kiszolgáló-e az operációs rendszer fut a virtuális Gépen belül.

A következő logika által Azure áttelepítése azonosítására szolgál a virtuális gép az operációs rendszer alapján Azure készültségét.

**Operációs rendszer** | **Részletek** | **Az Azure készültségi állapotát**
--- | --- | ---
Windows Server 2016 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Azure-beli használatra kész
Windows Server 2012 R2 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Azure-beli használatra kész
Windows Server 2012 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Azure-beli használatra kész
Windows Server 2008 R2 minden szervizcsomag-verzió | Azure teljes támogatást nyújt.| Azure-beli használatra kész
Windows Server 2003-2008 R2 | Az említett operációs rendszerektől átadott támogatási dátum és a szükséges a záró egy [egyéni támogatja a szerződés (CSA)](https://aka.ms/WSosstatement) támogatásához az Azure-ban. | Feltételesen készen áll az Azure-ba, fontolja meg az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Az említett operációs rendszerektől átadott a támogatási dátum végét, a gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Feltételesen készen az Azure-ba, javasoljuk, hogy az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
A Windows ügyfél 7, 8, 10 | Azure csak a Visual Studio előfizetői támogatást nyújt. | Azure feltételes kész
Windows Vista, XP Professional | Az említett operációs rendszerektől átadott a támogatási dátum végét, a gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Feltételesen készen az Azure-ba, javasoljuk, hogy az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Linux | Azure hozzá támogatást, ezek [Linux operációs rendszerek](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek lehet, hogy indítsa el az Azure-ban, de javasoljuk, hogy az Azure-bA áttelepítése előtt frissítse az operációs rendszer egy hitelesített verzióra. | Készen áll az Azure-ba, ha a verziót támogatja.<br/><br/>Feltételesen kész, ha a verzió nem támogatja.
Más operációs rendszerekkel<br/><br/> például Oracle Solaris Apple Mac OS stb., freebsd rendszerű, stb. | Azure hitelesíti az említett operációs rendszerektől. A gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Azure feltételes kész, javasoljuk, hogy egy támogatott operációs rendszer telepítése az Azure-bA áttelepítése előtt.  
Az operációs rendszer megadott *más* a vCenter-kiszolgáló | Az Azure áttelepítése ebben az esetben az operációs rendszer nem tudja azonosítani. | Ismeretlen készültségi. Győződjön meg arról, hogy a virtuális Gépen belül jelenleg futó operációs rendszer támogatja-e az Azure-ban. 
32 bites operációs rendszerek | A gép előfordulhat, hogy indítsa el az Azure-ban, de Azure előfordulhat, hogy nem támogatja teljes. | Feltételesen készen áll az Azure-ba, fontolja meg az áttérést az operációs rendszer, a gép a 32 bites operációs rendszer 64 bites operációs rendszer az Azure-bA áttelepítése előtt.

## <a name="sizing"></a>Méretezése

Miután a számítógép készen áll az Azure-van megjelölve, Azure áttelepítése méretezi a a virtuális gép és a lemezek, az Azure. Ha a méretezési feltételt értékelés tulajdonságai között megadott ehhez méretezési teljesítmény-alapú, Azure áttelepítése tekinti a előzményeinek a gép azonosításához az Azure-ban egy Virtuálisgép-méretet. Ez a módszer akkor hasznos, forgatókönyvekben, ahol túlzott foglalt le a helyszíni virtuális gép, de a használat alacsony, és szeretné megfelelő méretének Azure költség menti a virtuális gépek.

> [!NOTE]
> Azure áttelepítése a helyszíni virtuális gépek előzményeinek gyűjti a vCenter Server alkalmazásból. Ahhoz, hogy pontos megfelelő méretének kiválasztását, győződjön meg arról, hogy a vCenter-kiszolgáló statisztikák beállítás értéke 3. szint, és várjon, amíg legalább egy napot megelőző kicking ki a helyszíni virtuális gépek felderítése. A statisztika beállítás a vCenter Server 3. szint alatt van, lemez és hálózat teljesítményadatait nem történik gyűjtés. 

Ha nem szeretné, hogy a előzményeinek figyelembe venni, és szeretné átvenni a virtuális Gépet állítsanak-van az Azure-ba, adja meg, a méretezési kritériumot *, a helyszíni* és Azure áttelepítése majd átméreteződik-e a virtuális gépek nélkül helyszíni konfigurációja alapján annak eldöntéséhez, hogy a kihasználtsági adatok.

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezése

A teljesítmény-alapú méretezési Azure áttelepítése kezdődik, a virtuális gép csatlakoztatott lemezei hálózati adapterek követi, és majd maps egy Azure virtuális gép számítási követelményei alapján a helyszíni virtuális gép. 
 
- **Lemezek**: Azure áttelepítése megkísérli minden a gép az Azure-ban a lemez csatlakoztatott lemez. 
    
    > [!NOTE]
    > Azure áttelepítése támogatja csak felügyelt assessment lemezeket.
    
    - Ahhoz, hogy a hatékony lemez i/o / másodperc (IOPS) és az átviteli sebesség (MB/s), Azure áttelepítése szorozza meg a lemez IOPS, az átviteli sebesség a megerősítő tényezővel. A hatékony IOPS és átviteli értékek alapján, ha a lemez kell rendelni egy standard vagy prémium lemezt az Azure-ban Azure áttelepítése azonosítja.
    - Ha Azure áttelepítése nem található a szükséges IOPS & átviteli lemezzel, az Azure-jelöli céljaira nem használható gépként. [További](../azure-subscription-service-limits.md#storage-limits) kapcsolatos Azure korlátozza a lemez és virtuális gép.
    - Ha úgy találja, hogy a megfelelő lemezek egy készlete, Azure áttelepítése kiválasztja a meglévők közül, amelyek támogatják a tárolási redundancia módját, valamint a assessment beállításokban megadott helyre.
    - Ha több jogosult lemezek vannak, a másikat a legalacsonyabb költséget választja ki.
    - Ha a lemezek teljesítményadatokat nem érhető el, a lemezek vannak leképezve az Azure-ban a standard lemezek.

- **A hálózati adapterek**: Azure áttelepítése próbál meg megtalálni egy Azure virtuális Gépen, amely támogatja a hálózati adapterek a helyi géphez csatlakozik, és ezek a hálózati adapterek szükséges teljesítményt száma.
    - Ahhoz, hogy a helyszíni virtuális gép hatékony hálózati teljesítményét, Azure át összesíti az összes hálózati adapteren másodpercenként (MBps) kívül a géphez (hálózati kimenő), továbbított adatok, és a megerősítő tényező vonatkozik. Ez a szám egy Azure virtuális Gépen, amely képes támogatni a szükséges hálózati teljesítmény kereséséhez használt.
    - Hálózati teljesítmény, valamint azt is úgy ítéli meg, ha az Azure virtuális gép is támogatja a szükséges hálózati adapterek száma.
    - Ha hálózati teljesítményadatai nem áll rendelkezésre, csak a hálózati adapterek száma a Virtuálisgép-méretezési tekinthető meg.

- **Virtuálisgép-méretet**: után tárolási és hálózati követelményei kiszámítása, Azure át úgy ítéli meg CPU és memória követelményeknek megfelelő Virtuálisgép-méretet az Azure-ban található.
    - Azure áttelepítése ellenőrzi, hogy az a túlterhelt maggal és a memória, és a hatékony maggal és a memória a megerősítő tényező vonatkozik. Ez a szám alapján, megkísérli annak az Azure-ban található a megfelelő Virtuálisgép-méretet.
    - Ha található nem megfelelő méretű, a gép van megjelölve céljaira nem használható az Azure-bA.
    - Ha egy megfelelő méretű található, a tárolási és hálózatkezelési számítások Azure áttelepítése vonatkozik. Ezt követően végrehajtja, helyét és árképzési szint beállításait, a végső virtuális gép mérete javaslat.
    - Ha több jogosult Azure Virtuálisgép-méretek, a másikat a legalacsonyabb költséget ajánlott.

### <a name="as-on-premises-sizing"></a>Mint a helyszíni méretezése
Ha a méretezési kritériumot *regisztrációja, mivel a helyszíni méretezési*, Azure át nem veszi figyelembe a virtuális gépek előzményeinek és foglal le a virtuális gépek és a lemez mérete alapján lefoglalt helyszíni.
- **Tárolási**: az egyes lemezek egy szabványos lemezt az Azure-ban a akkora, mint a helyszíni lemez ajánlott.
- **Hálózati**: egyes hálózati adaptereken, az Azure-ban a hálózati adapter használata javasolt.
- **Számítási**: Azure áttelepítése magok száma és memória mérete, a helyszíni virtuális gép és egy Azure virtuális Gépen azonos konfigurációjú javasolja. Ha több jogosult Azure Virtuálisgép-méretek, a másikat a legalacsonyabb költséget ajánlott.
 
### <a name="confidence-rating"></a>Megbízhatósági minősítése

Azure telepítse át az egyes assessment társítva, amely az 1 csillag az 5 csillagokra (1 csillag alatt legalacsonyabb és legmagasabb alatt 5 csillagos) abban, hogy minősítéssel. A megbízhatósági minősítést rendel az értékelés kiszámításához szükséges adatpontok rendelkezésre állása alapján értékelését. A program segít becsléséhez át Azure által biztosított mérete ajánlások megbízhatóságát. 

Megbízhatósági minősítés akkor hasznos, ha is *teljesítmény-alapú méretezési* , nem minden adatpontok érhetők el. A *regisztrációja, mivel a helyszíni méretezési*, az vetett bizalmat minősítés mindig 5 csillagos, ha Azure telepítse át a virtuális gép méretének szükséges összes adatot. 

A teljesítmény-alapú méretezési Azure át kell a kihasználtsági adatok CPU és memória. A virtuális Géphez csatlakozik, lemezek, az olvasási/írási IOPS kell és átviteli teljesítmény-alapú méretezési elvégzéséhez. Hasonló módon az összes hálózati adapteren a virtuális Géphez csatlakozik, Azure át kell a hálózati vagy kikapcsolása méretezési teljesítmény-alapú. Ha a fenti kihasználtsági számok nem érhető el a vCenter Server, az Azure áttelepítése végezhető el mérete ajánlás nem lehet megbízható. Attól függően, hogy a rendelkezésre álló adatpontok százaléka a megbízhatóság besorolása értékelési biztosítja:

   **Az adatpontok rendelkezésre állása** | Megbízhatósági minősítése
   --- | ---
   0%-20% | 1 csillag
   21%-40% | 2 csillag
   41%-60% | 3 csillag
   61%-80% | 4 csillag
   81%-100% | 5 csillagos

Egy értékelési nem lehet elérhető a következő okok egyikéből adódóan adatpontok:
- A statisztika beállítás a vCenter-kiszolgáló nincs beállítva a 3. és az értékelés szinten teljesítmény-alapú méretezése a méretezési feltételként rendelkezik. 3 szintje alacsonyabb a vCenter Server statisztika beállítás esetén teljesítményadatokat lemezek és a hálózati nem gyűjtenek a vCenter Server alkalmazásból. A lemez és hálózat áttelepítése Azure által biztosított javaslat a Mi lett lefoglalva a helyszíni ebben az esetben csak alapján. A tároláshoz Azure telepítse át azt javasolja, hogy standard lemezek, nincs mód, ha a lemez van a magas IOPS/teljesítmény, a prémium szintű lemezekhez.
- A statisztika vCenter Server lett beállítását egy rövid időre 3 szintű kicking a felderítés kikapcsolása előtt. Például ha módosítja a statisztika érvényes szint ma 3 és megszervezni ki használatával a gyűjtő készülék holnap (után 24 óra), a felderítés létrehozásakor egy napig értékelését, hogy az adatpontok. De ha módosítja a teljesítmény időtartama assessment tulajdonságai egy hónap, a lemeznek leáll az vetett bizalmat minősítés, és nem érhető el a hálózati teljesítmény adatok az elmúlt egy hónapig. Ha azt szeretné, figyelembe kell venni az egy hónap utolsó teljesítményadatait, javasoljuk, hogy őrizze meg a vCenter statisztika beállítást úgy, hogy 3-as szintű előtt indítsa el a felderítési egy hónapig. 
- Néhány virtuális géppel állítsa le az időtartam, amelynek az értékelést kiszámítása során. Ha a virtuális gépek van kapcsolva az egyes időtartam, vCenter-kiszolgáló nem fog teljesítményadatok adott időszakra. 
- Néhány virtuális géppel az időszakot, amelynek az értékelést számítani Between lettek létrehozva. Például az értékelést létrehozásakor az utolsó, de néhány virtuális géppel előzményeinek jöttek létre a környezetben csak egy hete. Ebben az esetben az új virtuális gépek előzményeinek csak akkor van a teljes ideje.

> [!NOTE]
> Ha bármely assessment a abban, hogy minősítése 3 csillag alatt van, várunk javasoljuk, hogy 3, módosítsa a vCenter-kiszolgáló statisztikák beállítások szintjén assessment figyelembe venni kívánt időtartama (1 nap/1 hét/1 hónap), és tegye a felderítés és az értékelés. Ha az előző nem hajtható végre, a teljesítmény-alapú méretezési nem lehet megbízható és a javasoljuk, hogy váltani *regisztrációja, mivel a helyszíni méretezési* assessment tulajdonságainak módosításával.

## <a name="monthly-cost-estimation"></a>Havi költség becslése

Méretezési javaslatok befejezését követően a Azure áttelepítése az áttelepítés utáni számítási és tárolási költségek számítja ki.

- **Költség számítási**: a javasolt Azure Virtuálisgép-méretet használó, Azure telepítse át a számlázási API-t használja havi költségének kiszámítására, a virtuális gép számára. A számítási az operációs rendszer, frissítési garancia, helyét és pénznem beállítások figyelembe vesz igénybe. A költségeket von gépeire, teljes havi számítási költségének kiszámítására.
- **Tárolási költségek**: A havi tárolási költség, a gép összes lemeze havi költségét összesítésével számítható a géphez csatlakozik. Az Azure áttelepítése a teljes havi tárolási költségek a tárolási költségeket az összes gépet összesítésével számítja ki. A számítási jelenleg nem veszi figyelembe assessment beállításaiban megadott ajánlatokat.

Az értékelés beállításaiban megadott pénznemben költségek jelennek meg. 


## <a name="next-steps"></a>További lépések

[Hozzon létre egy értékelési helyszíni VMware virtuális gépek esetén](tutorial-assessment-vmware.md)
