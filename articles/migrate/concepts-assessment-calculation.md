---
title: Értékelések az Azure Áttelepítési kiszolgáló értékelésében
description: További információ az Azure Áttelepítési kiszolgáló értékelésének felméréseiről
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: d1f32eea0ec6a8a4877fd1dc134344cfe68dcaba
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537763"
---
# <a name="assessments-in-azure-migrateserver-assessment"></a>Értékelések az Azure áttelepítése:Kiszolgáló felmérése

Ez a cikk áttekintést nyújt az [Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz felméréseiről. A kiszolgálóértékelés eszköz felmérheti a helyszíni VMware virtuális gépeket, a Hyper-V VM-eket és a fizikai kiszolgálókat az Azure-ba való áttelepítéshez.

## <a name="whats-an-assessment"></a>Mi az az értékelés?

A Kiszolgálóértékelési eszközzel végzett értékelés méri a helyszíni kiszolgálók Azure-ba való áttelepítésének készenlétét és becslését.

> [!NOTE]
> Az Azure Government tekintse át a [támogatott célértékelési](migrate-support-matrix.md#supported-geographies-azure-government) helyek. Vegye figyelembe, hogy a virtuális gép méretére vonatkozó javaslatok at értékelések fogja használni a virtuális gép sorozat kifejezetten a kormányzati felhő régiók. [További információ](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) a virtuálisgép-típusokról.

## <a name="types-of-assessments"></a>Az értékelések típusai

A kiszolgálóértékeléssel létrehozott értékelések az adatok időponthoz helyszíni pillanatképét teszik ki. A Kiszolgálófelmérés kétféle értékelést biztosít.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Az összegyűjtött teljesítményadatokon alapuló ajánlásokat fogalmazó értékelések | A virtuális gép méretre vonatkozó javaslata processzor- és memóriakihasználtsági adatokon alapul.<br/><br/> A lemeztípus-ajánlás (szabványos HDD/SSD vagy prémium szintű felügyelt lemezek) a helyszíni lemezek IOPS-on és átviteli keresztülértékén alapul.
**A hogy van a helyszíni** | Olyan értékelések, amelyek nem használnak teljesítményadatokat javaslatok megfogalmazásához. | A virtuális gép méretére vonatkozó javaslat a helyszíni virtuális gép méretén alapul<br/><br> Az ajánlott lemeztípus a kiválasztott tárolási típuson alapul.

## <a name="how-do-i-run-an-assessment"></a>Hogyan futtathatok értékelést?

Az értékelés többféleképpen is lefut:

- Gépek értékelése egy könnyű Azure Migrate készülék által gyűjtött kiszolgálói metaadatok használatával. A készülék felderíti a helyszíni gépeket, és elküldi a gép metaadatait/teljesítményadatait az Azure Migrate szolgáltatásnak.
- A gépeket vesszővel tagolt értékekkel (CSV) importált kiszolgálói metaadatokkal mérheti fel.

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan kell felmérni a készüléket?

Ha egy Azure Migrate-berendezést telepít a helyszíni kiszolgálók felderítésére, tegye a következőket:

1. Beállíthatja az Azure-t és a helyszíni környezetet, hogy működjön együtt a Kiszolgálói értékeléssel.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a Kiszolgálóértékelés eszközt.
3. Egy könnyű Azure Migrate-berendezés üzembe helyezése. A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásnak. A készülék virtuális gépként vagy fizikai gépként van telepítve. Nem kell semmit telepíteni olyan gépekre, amelyeket értékelni szeretne.
4. Miután a készülék megkezdi a gépfelderítést, összegyűjtheti a felmérni kívánt gépeket egy csoportba, és lefuttathatja a csoport értékelését.

Tudod követ -unk konzultáció részére [VMware](tutorial-prepare-vmware.md), [Hyper-V](tutorial-prepare-hyper-v.md), vagy [fizikai szolgál](tutorial-prepare-physical.md) -hoz kipróbál ezek lép.

## <a name="how-do-i-assess-with-imported-data"></a>Hogyan értékelhetem az importált adatokat?

Ha CSV-fájl használatával értékeli a kiszolgálókat, nincs szüksége készülékre. Ehelyett tegye a következőket:

1. Az Azure-t úgy állítja be, hogy együttműködjön a Kiszolgálóértékelés szolgáltatással.
2. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a Kiszolgálóértékelés eszközt.
3. Letölt egy CSV-sablont, és hozzáadja a kiszolgálóadatait.
4. Importálja a sablont a Kiszolgálóértékelés programba.
5. Felderítheti az importálással hozzáadott kiszolgálókat, összegyűjtheti egy csoportba, és lefuttathatja a csoport értékelését.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Ha az Azure Migrate készüléket használja az értékeléshez, ismerje meg a [VMware](migrate-appliance.md#collected-data---vmware) és a [Hyper-V](migrate-appliance.md#collected-data---hyper-v)számára gyűjtött metaadatokat és teljesítményadatokat.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hogyan számítja ki a készülék a teljesítményadatokat?

Ha felderítésre használja a készüléket, a számítási beállítások teljesítményadatait a következőképpen gyűjti a rendszer:

1. A készülék valós idejű mintapontot gyűjt:

    - **VMware VM-ek**: A készülék 20 másodpercenként valós idejű mintavételi pontot gyűjt.
    - **Hyper-V virtuális gépek:** A valós idejű mintavételi pont gyűjti minden 30 másodperces időközönként.
    - **Fizikai kiszolgálók**: A valós idejű mintavételi pont ötpercenkéntgyűjtésre kerül. 
    
2. A készülék feltekeri a mintapontokat (20 másodperc, 30 másodperc, 5 perc), hogy 10 percenként egyetlen adatpontot hozzon létre. Az egyetlen pont létrehozásához a készülék kiválasztja a csúcsértéket az összes mintából, majd elküldi azt az Azure-ba.
3. A Server Assessment az elmúlt hónap összes 10 perces mintapontjait tárolja.
4. Amikor értékelést hoz létre, a Kiszolgálói értékelés azonosítja a megfelelő adatpontot, amelyet a jobb méretezéshez használni kell, a *Teljesítményelőzmények* és a *Percentilis-használat*százalékos értékei alapján.

    - Ha például a teljesítményelőzmények egy hétre vannak állítva, és a percentilis kihasználtsága a 95. 
    - A 95 százalékos érték gondoskodik arról, hogy figyelmen kívül hagyja a kiugró értékeket, amelyek a 99 százalékos értéket választják.
    - Ha az adott időszak csúcshasználatát szeretné kiválasztani, és nem szeretne kihagyni egyetlen kiugró értéket sem, válassza ki a 99 százalékos százalékos értéket a percentilis-kihasználtsághoz.

5. Ezt az értéket megszorozza a kényelmi tényező, hogy a hatékony teljesítménykihasználási adatok at minden metrika (CPU-kihasználtság, memóriakihasználtság, lemez IOPS (olvasási és írási), lemez átviteli (olvasási és írási), és a hálózati átviteli teljesítmény (be és ki), hogy a készülék gyűjt.



## <a name="how-are-assessments-calculated"></a>Hogyan számítják ki az értékeléseket? 

A kiszolgálóértékelés ben végzett értékelések et a helyszíni gépek metaadat-/teljesítményadatai alapján számítják ki. Ha telepíti az Azure Migrate készüléket, majd a készülék által gyűjtött adatok alapján értékeli. Ha a használatával importált gépekre vonatkozóan végez értékelést. CSV-fájlt, akkor adja meg a számítások metaadatait. A számítások három szakaszban történnek:

1. **Azure-készenlét kiszámítása:** Mérje fel, hogy a gépek alkalmasak-e az Azure-ba való migrálásra.
2. **Méretezési javaslatok kiszámítása:** Becslés számítási, tárolási és hálózati méretezés. 
2. **Havi költségek kiszámítása:** Számítsa ki a gépek áttelepítés utáni futtatásának becsült havi számítási és tárolási költségeit az Azure-ban.

A számítások rendben vannak, és a gépkiszolgáló csak akkor lép tovább egy későbbi szakaszba, ha az előzőt átmegy. Például ha egy kiszolgáló nem felel meg az Azure-ra való felkészültség, az Azure-hoz nem megfelelőként van megjelölve, és a méretezés és a költségszámítás nem történik meg az adott kiszolgálón.


## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

A kiszolgálóértékelés ben szereplő értékelés az alábbiakat tartalmazza.

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az a hely, ahhoz a helyhez, ahhoz, ahhoz, ahhoz, hogy áttelepüljen. A Kiszolgálóértékelés jelenleg a következő célAzure-régiókat támogatja:<br/><br/> Ausztrália Kelet, Ausztrália Délkelet, Brazília Déli, Kanada Középső, Kanada Kelet-, Közép-India, Központi USA, Kína Kelet-, Kína Észak-, Kelet-Ázsia, Kelet-USA, Kelet-USA2, Németország Középső, Németország Északkelet, Japán Kelet, Japán Nyugat, Korea Központi, Korea Dél- Közép-USA, Észak-Európa, Dél-Közép USA, Délkelet-Ázsia, Dél-India, UK Déli, EGYESÜLT Királyság Nyugati, Egyesült Államok Gov Arizona, MINKET Gov Texas, MINKET Virginia Gov , Usa nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és US2 nyugat-amerikai régió.
*Céltároló lemez (méretezéskor)** | Az Azure-beli tároláshoz használandó lemezek típusa. <br/><br/> Adja meg a céltároló lemezt prémium szintű felügyelt, szabványos SSD-vel vagy szabványos HDD-vel felügyeltként.
**Céltároló lemez (teljesítményalapú méretezés)** | Adja meg a céltároló lemez típusát automatikus, prémium szintű felügyelt, szabványos HDD-vel kezelt vagy szabványos SSD-vel kezeltként.<br/><br/> **Automatikus**: A lemezre vonatkozó javaslat a lemezek teljesítményadatain (a bemeneti/kimeneti műveletek másodpercenkénti (IOPS) és átviteli teljesítményen alapul).<br/><br/>**Prémium/standard**: Az értékelés egy lemeztermékváltozatot javasol a kiválasztott tárolási típuson belül.<br/><br/> Ha azt szeretné elérni, hogy egy példány vm SLA 99,9%, figyelembe véve a prémium szintű felügyelt lemezek használatát. Ez biztosítja, hogy az értékelésben szereplő összes lemez ajánlott prémium szintű felügyelt lemezként.<br/><br/> Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
**Fenntartott példányok (RI-k)** | Adja meg [a fenntartott példányokat az](https://azure.microsoft.com/pricing/reserved-vm-instances/) Azure-ban, hogy a költségbecslések az értékelésben figyelembe vegyék a fenntartott példányok kedvezményeit.<br/><br/> A rit-ek jelenleg csak az Azure Migrate szolgáltatásban csak a használatra szánt fizetési alapú ajánlatok esetében támogatottak.
**Méretezési feltételek** | A virtuális gép megfelelő méretezéséhez használható az Azure-ban.<br/><br/> Használja a méretezést vagy a teljesítményalapú méretezést.
**Teljesítményelőzmények** | Teljesítményalapú méretezéssel használható. Adja meg a teljesítményadatok kiértékelésekor használt időtartamot.
**Százalékos kihasználtság** | Teljesítményalapú méretezéssel használható. Megadja a jobb méretezéshez használandó teljesítményminta percentilis értékét. 
**Virtuálisgép-sorozatok** | Adja meg az Azure VM-sorozat, amely figyelembe szeretné venni a jobb méretezés. Ha például nem rendelkezik olyan éles környezettel, amelynek A-sorozatú virtuális gépeket kell az Azure-ban, kizárhatja az A-sorozatot a listából vagy a sorozatból.
**Kényelmi faktor** | Az értékelés során használt puffer. A virtuális gépek (PROCESSZOR, memória, lemez és hálózat) gépi kihasználtsági adataira alkalmazott alkalmazásra szolgál. Ez figyelembe olyan kérdések, mint a szezonális használat, rövid teljesítmény előzmények, és valószínűleg növeli a jövőbeli használat.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsításáltalában kétmagos virtuális gép. A 2,0x-es komforttényezővel az eredmény egy négymagos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat,](https://azure.microsoft.com/support/legal/offer-details/) amelyben regisztrált. A kiszolgálófelmérés ennek megfelelően becsüli meg a költségeket.
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Felsorolja az Azure-ajánlaton felül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha az Azure-beli virtuális gépek nem futnak a nap 24 órájában, a hét 7 napján, megadhatja az időtartamot (hónapés napi óra) fognak futni. A költségbecslések kezelése ennek megfelelően történt.<br/><br/> Az alapértelmezett érték havi 31 nap és napi 24 óra.
**Azure Hybrid Benefit** | Itt adható meg, hogy rendelkezik-e szoftvergaranciával, és jogosult-e az [Azure Hybrid Benefit használatára.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Ha az Igen (az alapértelmezett beállítás) értékre van állítva, a windowsos virtuális gépek nem Windows Azure-árait veszi figyelembe a rendszer.

Tekintse át az értékelés létrehozásának [gyakorlati tanácsait](best-practices-assessment.md) a kiszolgálóértékeléssel.


## <a name="calculate-readiness"></a>Készültség számítása

Nem minden gép alkalmas az Azure-ban való futtatásra. A Kiszolgálóértékelés felméri az egyes helyszíni gépeket, és egy készenléti kategóriát rendel hozzá. 
- **Készen áll az Azure-ra:** A gép módosítások nélkül áttelepíthető az Azure-ba. Az Azure-ban fog kezdődni teljes Körű Azure-támogatással.
- **Feltételesen készen áll az Azure-ra:** Előfordulhat, hogy a gép elindul az Azure-ban, de előfordulhat, hogy nem rendelkezik teljes Azure-támogatással. Például egy gép, amely a Windows Server egy régebbi verzióját futtatja, nem támogatott az Azure-ban. Óvatosnak kell lennie, mielőtt ezeket a gépeket az Azure-ba telepíti át. Kövesse az értékelésben javasolt javítási útmutatást a készültségi problémák megoldásához.
- **Nem áll készen az Azure-ra:** A gép nem indul el az Azure-ban. Ha például egy helyszíni géplemez több mint 64 TB-os, nem üzemeltethető az Azure-ban. Kövesse a szervizelési útmutatót a probléma megoldásához az áttelepítés előtt. 
- **Készenlét ismeretlen:** Az Azure Migrate nem tudta meghatározni a számítógép készenlétét, mert nem áll rendelkezésre elegendő metaadat.

A készenlét kiszámításához a Kiszolgálóértékelés áttekinti a számítógép tulajdonságait és az operációs rendszer beállításait az alábbi táblázatokban összegezve. 

### <a name="machine-properties"></a>A gép tulajdonságai

A Kiszolgáló-felmérés áttekinti a helyszíni virtuális gép következő tulajdonságait, és megállapítja, hogy futtatható-e az Azure-on.

**Tulajdonság** | **Részletek** | **Az Azure-ra való felkészültség állapota**
--- | --- | ---
**Rendszerindítás típusa** | Az Azure nem UEFI-vel, hanem rendszerindítási típusú virtuális gépeket is támogat. | Feltételesen készen áll, ha a rendszerindítási típus UEFI.
**Cores** | A magok száma a gépekben egyenlőnek vagy kisebbnek kell lennie, mint az Azure virtuális gépek által támogatott magok maximális száma (128).<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate a használt magokat veszi összehasonlításra. Ha az értékelési beállításokban meg van adva egy komforttényező, a felhasznált magok számát megszorozza a komforttényező.<br/><br/> Ha nincs teljesítményelőzmények, az Azure Migrate a kiosztott magokat használja a kényelmi tényező alkalmazása nélkül. | Készen áll, ha kisebb vagy egyenlő a határértékeket.
**Memory (Memória)** | A gép memória méretének egyenlőnek vagy kisebbnek kell lennie, mint az Azure M sorozatban&nbsp;Standard_M128m 2) engedélyezett maximális memória (3892 gigabájt [GB] az Azure M sorozatban Standard_M128m<sup>2)</sup>az Azure virtuális gép számára engedélyezett. [További információ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate a felhasznált memóriát veszi figyelembe az összehasonlításhoz. Ha meg van adva egy kényelmi tényező, a felhasznált memóriát megszorozza a komforttényező.<br/><br/> Ha nincs előzmény, a lefoglalt memória a komforttényező alkalmazása nélkül kerül használva.<br/><br/> | Készen állunk, ha a határokon belül.
**Tárolólemez** | A lemez lefoglalt méretének legkisebbméretűnek kell lennie. Bár az Azure támogatja a 64 TB-os lemezek Ultra SSD-lemezekkel, az Azure Migrate: Server Assessment jelenleg ellenőrzi a 32 TB a lemez mérete korlátok, mivel nem támogatja az Ultra SSD még. <br/><br/> A számítógéphez csatlakoztatott lemezek számának legkevesebbnek kell lennie, beleértve az operációs rendszer lemezét is. | Készen állunk, ha a határokon belül.
**Hálózat** | A számítógéphez 32 vagy kevesebb hálózati adapternek (NIC) kell kapcsolódnia. | Készen állunk, ha a határokon belül.

### <a name="guest-operating-system"></a>Vendég operációs rendszer
A virtuális gép tulajdonságaival együtt a Server Assessment megvizsgálja a gépek vendég operációs rendszerét, hogy megállapítsa, futtatható-e az Azure-on.

> [!NOTE]
> VMware virtuális gépek esetén a Kiszolgálói értékelés a vCenter Server virtuális gépéhez megadott operációs rendszert használja a vendég operációs rendszer elemzésének kezeléséhez. A VMware-en futó Linux virtuális gépek esetében jelenleg nem azonosítja a vendég operációs rendszer pontos kernelverzióját.

A kiszolgálóértékelése a következő logikát használja az Azure-készenlét azonosítására az operációs rendszer alapján.

**Operációs rendszer** | **Részletek** | **Az Azure-ra való felkészültség állapota**
--- | --- | ---
Windows Server 2016 & összes sp-t | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2012 R2 & összes SP | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
A Windows Server 2012 & az összes sp-t | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2008 R2 az összes sp-vel | Az Azure teljes körű támogatást nyújt.| Készen áll az Azure-beli használatra
Windows Server 2008 (32 és 64 bites) | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-beli használatra
Windows Server 2003, 2003 R2 | Ezek az operációs rendszerek már elmúltak a támogatási dátum, és szükség van egy [egyéni támogatási szerződés (CSA)](https://aka.ms/WSosstatement) az Azure-ban nyújtott támogatáshoz. | Feltételesen készen áll az Azure-ra. Fontolja meg az operációs rendszer frissítését, mielőtt áttérne az Azure-ba.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | Ezek az operációs rendszerek már elmúltak a támogatás megszűnésének dátuma. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Windows-ügyfél 7, 8 és 10 | Az Azure [csak visual studio-előfizetéssel](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) nyújt támogatást. | Feltételesen készen áll az Azure-beli használatra
Windows 10 Pro asztal | Az Azure támogatást nyújt a [több-bérlős üzemeltetési jogokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-beli használatra
Windows Vista, XP Professional | Ezek az operációs rendszerek már elmúltak a támogatás megszűnésének dátuma. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Linux | Az Azure támogatja ezeket a [Linux operációs rendszereket.](../virtual-machines/linux/endorsed-distros.md) Más Linux operációs rendszerek is elindulhatnak az Azure-ban, de azt javasoljuk, hogy frissítse az operációs rendszert egy jóváhagyott verzióra, mielőtt átkossza az Azure-ba. | Készen áll az Azure-ra, ha a verzió t.<br/><br/>Feltételesen kész, ha a verzió nincs jóváhagyva.
Egyéb operációs rendszerek<br/><br/> Például Oracle Solaris, Apple macOS stb., FreeBSD stb. | Az Azure nem támogatja ezeket az operációs rendszereket. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy az Azure-ba való áttelepítés előtt telepítsen egy támogatott operációs rendszert.  
A vCenter-kiszolgálón **másként** megadott operációs rendszer | Az Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készenlét. Győződjön meg arról, hogy a virtuális gépen belül futó operációs rendszer támogatja az Azure-ban.
32 bites operációs rendszerek | Előfordulhat, hogy a gép elindul az Azure-ban, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra. Fontolja meg a számítógép operációs rendszerének 32 bites operációs rendszerről 64 bites operációs rendszerre való frissítését, mielőtt áttérne az Azure-ra.

## <a name="calculating-sizing"></a>Méretezés számítása


Miután a gép meg van jelölve készen áll az Azure-ra, a Server Assessment méretezési javaslatokat tesz az Azure virtuális gép és a lemez termékváltozatának azonosítására. A méretezési számítások attól függnek, hogy a helyszíni méretezést vagy a teljesítményalapú méretezést használja-e.

### <a name="calculate-sizing-as-is-on-premises"></a>Méretezés számítása (a hogy van a helyszínen)

 Ha a helyszíni méretezés, a kiszolgálóértékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményelőzményeit.

- **Számítási méretezés:** Egy Azure-beli virtuális gép termékváltozata a helyszíni lefoglalt méret alapján.
- **Tárolás/lemez méretezése**: A kiszolgálófelmérés az értékelési tulajdonságokban megadott tárolási típust vizsgálja (standard HDD/SSD/premium), és ennek megfelelően ajánlja a lemez típusát. Az alapértelmezett tárolótípus a prémium szintű lemezek.
- **Hálózati méretezés**: A kiszolgálófelmérés figyelembe veszi a helyszíni számítógép hálózati adapterét.


### <a name="calculate-sizing-performance-based"></a>Méretezés számítása (teljesítményalapú)

Ha teljesítményalapozási méretezést használ, a Kiszolgálói értékelés méretezési javaslatokat az alábbiak szerint hajt végre:

- A Kiszolgálófelmérés a számítógép teljesítményelőzményeit veszi figyelembe a virtuális gép méretének és lemeztípusának azonosításához az Azure-ban.
- Ha a kiszolgálókcsv-fájllal lettek importálva, a program a megadott értékeket használja. Ez a módszer különösen akkor hasznos, ha túlfoglalta a helyszíni gépet, a kihasználtság alacsony, és a költségek csökkentése érdekében szeretné megfelelő méretezést az Azure-ban. 
- Ha nem szeretné használni a teljesítményadatokat, állítsa vissza a méretezési feltételeket a helyszíni állapotban, az előző szakaszban leírtak szerint.

#### <a name="calculate-storage-sizing"></a>Tárolási méretezés számítása

A tárhely méretezéséhez az Azure Migrate megpróbálja leképezni a géphez csatlakoztatott összes lemezt egy azure-beli lemezre, és a következőképpen működik:

1. Server Assessment hozzáadja a lemez olvasási és írási IOPS-át a szükséges teljes IOPS leolvasásához. Hasonlóképpen hozzáadja az olvasási és írási átviteli értékeket az egyes lemezek teljes átviteli értékének leolvasásához.
2. Ha a tényleges IOPS- és átviteli-átviteli értékek alapján automatikus ként adta meg a tárolási típust, a Kiszolgálóértékelés határozza meg, hogy a lemezt le kell-e képeznie egy szabványos HDD-re, szabványos SSD-re vagy prémium szintű lemezre az Azure-ban. Ha a tárolótípus standard HDD/SSD/Premium értékre van állítva, a Server Assessment megpróbál egy lemeztermékváltozatot találni a kiválasztott tárolási típuson belül (standard HDD/SSD/Premium lemezek).
3. A lemezek a következőképpen vannak kiválasztva:
    - Ha a kiszolgálófelmérés nem talál egy lemezt a szükséges IOPS és átviteli, azt jelzi, hogy a gép nem megfelelő az Azure számára.
    - Ha a Kiszolgálófelmérés megfelelő lemezeket talál, kiválasztja azokat a lemezeket, amelyek támogatják az értékelési beállításokban megadott helyet.
    - Ha több jogosult lemez van, a Kiszolgálófelmérés a legalacsonyabb költségű lemezt választja ki.
    - Ha a lemez teljesítményadatai nem érhetők el, a lemez konfigurációs adatait (lemezméret) használja egy szabványos SSD-lemez kereséséhez az Azure-ban.

#### <a name="calculate-network-sizing"></a>Hálózati méretezés számítása

A Server Assessment olyan Azure virtuális gépet próbál keresni, amely támogatja a helyszíni géphez csatlakoztatott hálózati adapterek számát és a hálózati adapterek által igényelt teljesítményt.
- A helyszíni virtuális gép hatékony hálózati teljesítményének lehetővé ételeérdekében a Kiszolgálóértékelés összesíti a másodpercenként továbbított adatokat a gépből (hálózatból), az összes hálózati adapteren keresztül, és alkalmazza a komforttényezőt. Ezt a számot használja egy Azure virtuális gép, amely támogatja a szükséges hálózati teljesítményt.
- A hálózati teljesítmény mellett a Server Assessment azt is megvizsgálja, hogy az Azure virtuális gép támogatja-e a szükséges hálózati adapterek számát.
- Ha nem áll rendelkezésre hálózati teljesítményadat, a Kiszolgálófelmérés csak a virtuális gépek méretezésének hálózati adapterszámát veszi figyelembe.


#### <a name="calculate-compute-sizing"></a>Számítási méretezés számítása

Miután kiszámítja a tárolási és hálózati követelményeket, a Server Assessment a PROCESSZOR- és memóriaigényeket veszi figyelembe a megfelelő virtuális gépméret kereséséhez az Azure-ban.
- Az Azure Migrate megvizsgálja a hatékonyan használt magokat és a memóriát, hogy megtalálja a megfelelő virtuális gép méretét az Azure-ban.
- Ha nem talál megfelelő méretet, a gép az Azure számára alkalmatlanként van megjelölve.
- Ha megfelelő méretet talál, az Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután alkalmazza a hely- és tarifacsomag-beállításokat a végső virtuálisgép-méretajánláshoz.
- Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.


## <a name="confidence-ratings-performance-based"></a>Megbízhatósági besorolások (teljesítményalapú)

Az Azure Migrate minden teljesítményalapú felmérése egy megbízhatósági besorolással van társítva, amely egy (legalacsonyabb) és öt csillag (legmagasabb) között mozog. A megbízhatósági besorolás segít megbecsülni az Azure Migrate által biztosított méretjavaslatok megbízhatóságát.

- A megbízhatósági minősítés az értékelések kiszámításához szükséges adatpontok rendelkezésre állása alapján vannak az értékelésekhez rendelve.
- A teljesítményalapú méretezéshez a kiszolgálófelmérésnek szüksége van:
    - A cpu- és virtuálisgép-memória kihasználtsági adatai.
    - A lemez IOPS és átviteli adatok minden lemez a virtuális géphez csatolt.
    - A hálózati I/O-hoz kezelni a teljesítmény-alapú méretezés minden hálózati adapter csatlakozik egy virtuális géphez.
    - Ha ezek közül a kihasználtsági számok bármelyike nem érhető el, a méretjavaslatok nem lehetnek megbízhatóak.

> [!NOTE]
> A megbízhatósági minősítések nincsenek hozzárendelve az importált. CSV-fájlt. A minősítések szintén nem alkalmazhatók a helyszíni értékeléshez.
   
### <a name="ratings"></a>Minősítések

A rendelkezésre álló adatpontok százalékos arányától függően az értékelés megbízhatósági besorolása a következőképpen halad.

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0-20% | 1 csillag
   21-40% | 2 csillag
   41-60% | 3 csillag
   61-80% | 4 csillag
   81-100% | 5 csillag

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági besorolás

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági értékelést kaphat:

- Nem profilozta a környezetet arra az időtartamra, amelyre az értékelést létrehozza. Ha például úgy hozza létre az értékelést, hogy a teljesítmény időtartama egy napra van állítva, legalább egy napot kell várnia a felderítés megkezdése után az összes adatpont gyűjtésére.
- Egyes virtuális gépek leálltak abban az időszakban, amelyre az értékelést számították ki. Ha valamelyik virtuális gép bizonyos ideig ki van kapcsolva, a Kiszolgálói értékelés nem tudja összegyűjteni az adott időszakra vonatkozó teljesítményadatokat.
- Néhány virtuális gép jött létre abban az időszakban, amelyre az értékelést számították ki. Például ha létrehozott egy értékelést a teljesítményelőzmények az elmúlt hónapban, de néhány virtuális gépek hoztak létre a környezetben csak egy héttel ezelőtt, az új virtuális gépek teljesítményelőzményei nem létezik a teljes időtartam.

> [!NOTE]
> Ha bármely értékelés megbízhatósági besorolása kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék profilálja a környezetet, majd újraszámítja az értékelést. Ellenkező esetben előfordulhat, hogy a teljesítményalapú méretezés nem megbízható. Ebben az esetben azt javasoljuk, hogy az értékelést a helyszíni méretezésre váltsa át.

## <a name="calculate-monthly-costs"></a>Havi költségek számítása

A méretezési javaslatok befejezése után az Azure Migrate kiszámítja az áttelepítés utáni számítási és tárolási költségeket.

- **Számítási költség:** Az ajánlott Azure virtuális gép méretének használatával az Azure Migrate a számlázási API-t használja a virtuális gép havi költségének kiszámításához.
    - A számítás figyelembe veszi az operációs rendszert, a szoftverbiztosítást, a fenntartott példányokat, a virtuális gép üzemidejét, helyét és pénznembeállításait.
    - Összesíti a teljes havi számítási költség kiszámításához az összes gép költségét.
- **Tárolási költség**: A gép havi tárolási költségét a géphez csatlakoztatott összes lemez havi költségének összesítésével számítják ki, az alábbiak szerint:
    - A Server Assessment az összes gép tárolási költségeinek összesítésével számítja ki a teljes havi tárolási költséget.
    - Jelenleg a számítás nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.


## <a name="next-steps"></a>További lépések

[Tekintse át](best-practices-assessment.md) az értékelések készítésére vonatkozó bevált gyakorlatokat. 


- További információ a [VMware virtuális gépek,](tutorial-prepare-vmware.md) [a Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a fizikai kiszolgálók értékelésének [futtatásáról.](tutorial-prepare-physical.md)
- További információ a [CSV-fájllal importált](tutorial-assess-import.md)kiszolgálók értékeléséről.
- További információ a [függőségi megjelenítés](concepts-dependency-visualization.md)beállításáról.
