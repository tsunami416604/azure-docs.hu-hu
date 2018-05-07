---
title: Azure áttelepítése Assessment számításokban |} Microsoft Docs
description: Értékelési számítások az Azure áttelepítése szolgáltatás áttekintése.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: f3ac9c328db1130ea25ac63170ee7de35fb67d16
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
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
Windows Server 2016 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2012 & összes szervizcsomag-verzió | Azure teljes támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 minden szervizcsomag-verzió | Azure teljes támogatást nyújt.| Készen áll az Azure-beli használatra
Windows Server 2003-2008 R2 | Az említett operációs rendszerektől átadott támogatási dátum és a szükséges a záró egy [egyéni támogatja a szerződés (CSA)](https://aka.ms/WSosstatement) támogatásához az Azure-ban. | Feltételesen készen áll az Azure-ba, fontolja meg az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Az említett operációs rendszerektől átadott a támogatási dátum végét, a gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Feltételesen készen az Azure-ba, javasoljuk, hogy az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
A Windows ügyfél 7, 8, 10 | Azure csak a Visual Studio előfizetői támogatást nyújt. | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Az említett operációs rendszerektől átadott a támogatási dátum végét, a gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Feltételesen készen az Azure-ba, javasoljuk, hogy az operációs rendszer frissítése az Azure-bA áttelepítése előtt.
Linux | Azure hozzá támogatást, ezek [Linux operációs rendszerek](../virtual-machines/linux/endorsed-distros.md). Más Linux operációs rendszerek lehet, hogy indítsa el az Azure-ban, de javasoljuk, hogy az Azure-bA áttelepítése előtt frissítse az operációs rendszer egy hitelesített verzióra. | Készen áll az Azure-ba, ha a verziót támogatja.<br/><br/>Feltételesen kész, ha a verzió nem támogatja.
Más operációs rendszerekkel<br/><br/> például Oracle Solaris Apple Mac OS stb., freebsd rendszerű, stb. | Azure hitelesíti az említett operációs rendszerektől. A gép előfordulhat, hogy indítsa el az Azure-ban, de nem az operációs rendszer támogatott Azure által biztosított. | Azure feltételes kész, javasoljuk, hogy egy támogatott operációs rendszer telepítése az Azure-bA áttelepítése előtt.  
Az operációs rendszer megadott *más* a vCenter-kiszolgáló | Az Azure áttelepítése ebben az esetben az operációs rendszer nem tudja azonosítani. | Ismeretlen készültségi. Győződjön meg arról, hogy a virtuális Gépen belül jelenleg futó operációs rendszer támogatja-e az Azure-ban.
32 bites operációs rendszerek | A gép előfordulhat, hogy indítsa el az Azure-ban, de Azure előfordulhat, hogy nem támogatja teljes. | Feltételesen készen áll az Azure-ba, fontolja meg az áttérést az operációs rendszer, a gép a 32 bites operációs rendszer 64 bites operációs rendszer az Azure-bA áttelepítése előtt.

## <a name="sizing"></a>Méretezése

Miután a számítógép készen áll az Azure-van megjelölve, Azure áttelepítése méretezi a a virtuális gép és a lemezek, az Azure. Ha a méretezési feltételt értékelés tulajdonságai között megadott ehhez méretezési teljesítmény-alapú, Azure áttelepítése tekinti a előzményeinek a gép azonosításához az Azure-ban egy Virtuálisgép-méretet. Ez a módszer akkor hasznos, forgatókönyvekben, ahol túlzott foglalt le a helyszíni virtuális gép, de a használat alacsony, és szeretné megfelelő méretének Azure költség menti a virtuális gépek.

> [!NOTE]
> Azure áttelepítése a helyszíni virtuális gépek előzményeinek gyűjti a vCenter Server alkalmazásból. Ahhoz, hogy pontos megfelelő méretének kiválasztását, győződjön meg arról, hogy a vCenter-kiszolgáló statisztikák beállítás értéke 3. szint, és várjon, amíg legalább egy napot megelőző kicking ki a helyszíni virtuális gépek felderítése. A statisztika beállítás a vCenter Server 3. szint alatt van, lemez és hálózat teljesítményadatait nem történik gyűjtés.

Ha nem szeretné, hogy figyelembe venni a előzményeinek a Virtuálisgép-méretezési, és szeretné átvenni a virtuális Gépet állítsanak-van az Azure-ba, adja meg, a méretezési kritériumot *, a helyszíni* és Azure áttelepítése majd átméreteződik-e a virtuális gépeket, a helyszíni alapján konfigurációs figyelembe véve a kihasználtsági adatok nélkül. Lemez átméretezése, ebben az esetben továbbra is alapjául a teljesítményadatokat.

### <a name="performance-based-sizing"></a>Teljesítmény-alapú méretezése

A teljesítmény-alapú méretezési Azure áttelepítése kezdődik, a virtuális gép csatlakoztatott lemezei hálózati adapterek követi, és majd maps egy Azure virtuális gép számítási követelményei alapján a helyszíni virtuális gép.

- **Tárolási**: Azure áttelepítése megkísérli minden a gép az Azure-ban a lemez csatlakoztatott lemez.

    > [!NOTE]
    > Azure áttelepítése támogatja csak felügyelt assessment lemezeket.

    - Ahhoz, hogy a hatékony lemez i/o / másodperc (IOPS) és az átviteli sebesség (MB/s), Azure áttelepítése szorozza meg a lemez IOPS, az átviteli sebesség a megerősítő tényezővel. A hatékony IOPS és átviteli értékek alapján, ha a lemez kell rendelni egy standard vagy prémium lemezt az Azure-ban Azure áttelepítése azonosítja.
    - Ha Azure áttelepítése nem található a szükséges IOPS & átviteli lemezzel, az Azure-jelöli céljaira nem használható gépként. [További](../azure-subscription-service-limits.md#storage-limits) kapcsolatos Azure korlátozza a lemez és virtuális gép.
    - Ha úgy találja, hogy a megfelelő lemezek egy készlete, Azure áttelepítése kiválasztja a meglévők közül, amelyek támogatják a tárolási redundancia módját, valamint a assessment beállításokban megadott helyre.
    - Ha több jogosult lemezek vannak, a másikat a legalacsonyabb költséget választja ki.
    - Ha a lemezek teljesítményadatokat nem érhető el, a lemezek vannak leképezve az Azure-ban a standard lemezek.

- **Hálózati**: Azure áttelepítése próbál meg megtalálni egy Azure virtuális Gépen, amely támogatja a hálózati adapterek a helyi géphez csatlakozik, és ezek a hálózati adapterek szükséges teljesítményt száma.
    - Ahhoz, hogy a helyszíni virtuális gép hatékony hálózati teljesítményét, Azure át összesíti az összes hálózati adapteren másodpercenként (MBps) kívül a géphez (hálózati kimenő), továbbított adatok, és a megerősítő tényező vonatkozik. Ez a szám egy Azure virtuális Gépen, amely képes támogatni a szükséges hálózati teljesítmény kereséséhez használt.
    - Hálózati teljesítmény, valamint azt is úgy ítéli meg, ha az Azure virtuális gép is támogatja a szükséges hálózati adapterek száma.
    - Ha hálózati teljesítményadatai nem áll rendelkezésre, csak a hálózati adapterek száma a Virtuálisgép-méretezési tekinthető meg.

- **Számítási**: után tárolási és hálózati követelményei kiszámítása, Azure át úgy ítéli meg CPU és memória követelményeknek megfelelő Virtuálisgép-méretet az Azure-ban található.
    - Azure áttelepítése ellenőrzi, hogy az a túlterhelt maggal és a memória, és a hatékony maggal és a memória a megerősítő tényező vonatkozik. Ez a szám alapján, megkísérli annak az Azure-ban található a megfelelő Virtuálisgép-méretet.
    - Ha található nem megfelelő méretű, a gép van megjelölve céljaira nem használható az Azure-bA.
    - Ha egy megfelelő méretű található, a tárolási és hálózatkezelési számítások Azure áttelepítése vonatkozik. Ezt követően végrehajtja, helyét és árképzési szint beállításait, a végső virtuális gép mérete javaslat.
    - Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

### <a name="as-on-premises-sizing"></a>Mint a helyszíni méretezése
Ha a méretezési kritériumot *regisztrációja, mivel a helyszíni méretezési*, Azure át nem veszi figyelembe a virtuális gépek előzményeinek és a helyszíni lefoglalt méretétől függ VMs foglal le. Azonban az lemez méretezés, azt a lemezek a Standard vagy prémium szintű lemezek javasolt teljesítményelőzményei megfontolandó.  
- **Tárolási**: Azure át rendeli minden a gép az Azure-ban a lemez csatlakoztatott lemez.

    > [!NOTE]
    > Azure áttelepítése támogatja csak felügyelt assessment lemezeket.

    - Ahhoz, hogy a hatékony lemez i/o / másodperc (IOPS) és az átviteli sebesség (MB/s), Azure áttelepítése szorozza meg a lemez IOPS, az átviteli sebesség a megerősítő tényezővel. A hatékony IOPS és átviteli értékek alapján, ha a lemez kell rendelni egy standard vagy prémium lemezt az Azure-ban Azure áttelepítése azonosítja.
    - Ha Azure áttelepítése nem található a szükséges IOPS & átviteli lemezzel, az Azure-jelöli céljaira nem használható gépként. [További](../azure-subscription-service-limits.md#storage-limits) kapcsolatos Azure korlátozza a lemez és virtuális gép.
    - Ha úgy találja, hogy a megfelelő lemezek egy készlete, Azure áttelepítése kiválasztja a meglévők közül, amelyek támogatják a tárolási redundancia módját, valamint a assessment beállításokban megadott helyre.
    - Ha több jogosult lemezek vannak, a másikat a legalacsonyabb költséget választja ki.
    - Ha a lemezek teljesítményadatokat nem érhető el, a lemezek vannak leképezve az Azure-ban a standard lemezek.
- **Hálózati**: egyes hálózati adaptereken, az Azure-ban a hálózati adapter használata javasolt.
- **Számítási**: Azure áttelepítése magok száma és memória mérete, a helyszíni virtuális gép és egy Azure virtuális Gépen azonos konfigurációjú javasolja. Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja. A CPU és memória kihasználtsági adatok akkor nem tekinthető a helyszíni méretezése.

### <a name="confidence-rating"></a>Megbízhatósági minősítés

Az Azure Migrate minden értékelése olyan megbízhatósági minősítéssel van társítva, amely 1 csillagtól az 5 csillagig terjed (az 1 csillag a legalacsonyabb, az 5 csillag pedig a legmagasabb). A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve. Az értékelés megbízhatósági minősítése segít megbecsülni az Azure Migrate által nyújtott méretjavaslatok megbízhatóságát.

A virtuális gép teljesítményalapú méretezéséhez az Azure Migrate-nek szüksége van a processzor és a memória kihasználtsági adataira. Emellett az egyes lemezek, a virtuális Géphez csatlakozik, a méretezés, kell az olvasási/írási iops-érték és a teljesítményt. Ugyanígy az Azure Migrate-nek a virtuális géphez csatlakoztatott összes hálózati adapter esetén szüksége van a hálózati bejövő és kimenő forgalom mértékére a teljesítményalapú méretezés elvégzéséhez. Ha a fenti kihasználtsági számok valamelyike nem érhető el a vCenter Serveren, lehet, hogy az Azure Migrate által adott méretjavaslat nem megbízható. Attól függően, hogy a rendelkezésre álló adatpontok százaléka a megbízhatóság besorolása értékelési formájában érhető el az alábbi:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0%–20% | 1 csillag
   21%–40% | 2 csillag
   41%–60% | 3 csillag
   61%–80% | 4 csillag
   81%–100% | 5 csillag

Az értékelésekben a következő okok miatt nem lehet elérhető az összes adatpont:
- A statisztika beállítás a vCenter Server értéke 3. szint. Ha a vCenter Server statisztikai beállítása a 3. szintnél alacsonyabb, akkor a lemez és a hálózat teljesítményadatai nem lesznek begyűjtve a vCenter Serverről. Ebben az esetben az Azure Migrate által a lemezhez és a hálózathoz nyújtott javaslat nem a kihasználtságon alapul. Annak eldöntéséhez, hogy az IOPS/átviteli sebessége a lemezen, nélkül Azure áttelepítése nem lehet megállapítani, hogy a lemez kell az Azure-ban egy prémium szintű lemez, ezért, ebben az esetben Azure telepítse át azt javasolja, hogy minden lemezek a Standard lemezek.
- A vCenter Server statisztikai beállítása rövidebb időre a 3. szintre lett állítva a felderítés megkezdése előtt. Vegyünk például egy olyan forgatókönyvet, ahol ma 3. szintre módosítja a statisztikai beállítást, és holnap elindítja a felderítést a gyűjtőberendezéssel (24 óra eltelte után). Ha egy nap értékelését hozza létre, az összes adatponttal rendelkezik, az értékelés megbízhatósági minősítése pedig 5 csillagos lesz. Ha azonban egy hónapra változtatja az értékelésben a teljesítmény időtartamát, a megbízhatósági minősítés csökken, mert nem lennének elérhetők az utolsó egy hónap lemezzel és hálózati teljesítménnyel kapcsolatos adatai. Ha az utolsó egy hónap teljesítményadatait szeretné figyelembe venni, a felderítés megkezdése előtt egy hónapig ajánlott a 3. szinten tartani a vCenter Server statisztikai beállítását.
- Néhány virtuális gép le lett állítva abban az időszakban, amelyhez az értékelést számította. Ha valamely virtuális gép egy ideig ki lett kapcsolva, a vCenter Server nem rendelkezik az adott időszak teljesítményadataival.
- Néhány virtuális gép létrejött abban az időszakban, amelyhez az értékelést számította. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépeknek nincsenek teljesítményelőzményei a teljes időtartamhoz.

> [!NOTE]
> Ha valamely értékelés megbízhatósági minősítése 4 csillag alatt van, ajánlott a 3. szintre állítani a vCenter Server statisztikai beállításait, megvárni az értékeléshez használni kívánt időt (1 nap/1 hét/1 hónap), majd elvégezni a felderítést és az értékelést. Ha ez nem végezhető el, akkor lehet, hogy a teljesítményalapú méretezés nem megbízható, és azt javasoljuk, hogy váltson *helyszíni méretezésre* az értékelés tulajdonságainak módosításával.

## <a name="monthly-cost-estimation"></a>Havi költség becslése

Méretezési javaslatok befejezését követően a Azure áttelepítése az áttelepítés utáni számítási és tárolási költségek számítja ki.

- **Költség számítási**: a javasolt Azure Virtuálisgép-méretet használó, Azure telepítse át a számlázási API-t használja havi költségének kiszámítására, a virtuális gép számára. A számítási az operációs rendszer, frissítési garancia, helyét és pénznem beállítások figyelembe vesz igénybe. A költségeket von gépeire, teljes havi számítási költségének kiszámítására.
- **Tárolási költségek**: A havi tárolási költség, a gép összes lemeze havi költségét összesítésével számítható a géphez csatlakozik. Az Azure áttelepítése a teljes havi tárolási költségek a tárolási költségeket az összes gépet összesítésével számítja ki. A számítási jelenleg nem veszi figyelembe assessment beállításaiban megadott ajánlatokat.

Az értékelés beállításaiban megadott pénznemben költségek jelennek meg.


## <a name="next-steps"></a>További lépések

[Hozzon létre egy értékelési helyszíni VMware virtuális gépek esetén](tutorial-assessment-vmware.md)
