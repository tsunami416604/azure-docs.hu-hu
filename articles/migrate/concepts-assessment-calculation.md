---
title: Értékelések az Azure Áttelepítési kiszolgáló értékelésében
description: További információ az Azure Áttelepítési kiszolgáló értékelésének felméréseiről
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 2f76ea5f195be2914cdcdb4de9e93af38504d66e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769925"
---
# <a name="assessments-in-azure-migrate-server-assessment"></a>Értékelések az Azure Áttelepítése: Kiszolgálóértékelése

Ez a cikk áttekintést nyújt az [Azure Áttelepítés: Kiszolgálóértékelés](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszköz ben végzett értékelésekről. Az eszköz felmérheti a helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók az Azure-ba való áttelepítés.

## <a name="whats-an-assessment"></a>Mi az az értékelés?

A Kiszolgálóértékelés eszközzel végzett értékelés méri a helyszíni kiszolgálók Azure-ba való áttelepítésének felkészültségét és becslését.

> [!NOTE]
> Az Azure Government tekintse át a [támogatott célértékelési](migrate-support-matrix.md#supported-geographies-azure-government) helyek. Vegye figyelembe, hogy a virtuális gép méretére vonatkozó javaslatok at értékelések fogja használni a virtuális gép sorozat kifejezetten a kormányzati felhő régiók. [További információ](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) a virtuálisgép-típusokról.

## <a name="types-of-assessments"></a>Az értékelések típusai

A kiszolgálóértékeléssel létrehozott értékelések az adatok időponthoz helyszíni pillanatképét teszik ki. A Kiszolgálófelmérés kétféle értékelést biztosít.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | Az összegyűjtött teljesítményadatokon alapuló ajánlásokat fogalmazó értékelések | A virtuális gép méretére vonatkozó javaslat processzor- és RAM-kihasználtsági adatokon alapul.<br/><br/> A lemeztípus-javaslat a bemeneti/kimeneti műveletek másodpercenkénti (IOPS) és a helyszíni lemezek átviteli. A lemeztípusok az Azure Standard HDD, az Azure Standard SSD és az Azure Premium lemezek.
**A hogy van a helyszíni** | Olyan értékelések, amelyek nem használnak teljesítményadatokat ajánlások megfogalmazására | A virtuális gép mérete javaslat a helyszíni virtuális gép mérete alapul.<br/><br> Az ajánlott lemeztípus a kiválasztott tárolási típuson alapul.

## <a name="how-do-i-run-an-assessment"></a>Hogyan futtathatok értékelést?

Többféleképpen is futtathatok értékelést.

- Gépek értékelése egy könnyű Azure Migrate készülék által gyűjtött kiszolgálói metaadatok használatával. A készülék helyszíni gépeket fedez fel. Ezután elküldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásba.
- A gépeket vesszővel tagolt értékekkel (CSV) importált kiszolgálói metaadatok használatával mérheti fel.

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan kell felmérni a készüléket?

Ha egy Azure Migrate-berendezést telepít a helyszíni kiszolgálók felderítésére, tegye a következő lépéseket:

1. Állítsa be az Azure-t és a helyszíni környezetet, hogy működjön együtt a Kiszolgálóértékelés szolgáltatással.
1. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a Kiszolgálóértékelés eszközt.
1. Üzembe helyezhet egy könnyű Azure Migrate-berendezést. A készülék folyamatosan felderíti a helyszíni gépeket, és elküldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásba. Telepítse a készüléket virtuális gépként vagy fizikai gépként. Nem kell semmit telepítenie olyan gépekre, amelyeket értékelni szeretne.

Miután a készülék megkezdi a gépfelderítést, összegyűjtheti a felmérni kívánt gépeket egy csoportba, és lefuttathatja a csoport értékelését.

Kövesse oktatóanyagainkat a [VMware,](tutorial-prepare-vmware.md) [Hyper-V](tutorial-prepare-hyper-v.md), vagy [fizikai szerverekhez,](tutorial-prepare-physical.md) hogy kipróbáld ezeket a lépéseket.

## <a name="how-do-i-assess-with-imported-data"></a>Hogyan értékelhetem az importált adatokat?

Ha csv-fájl használatával értékeli a kiszolgálókat, nincs szüksége készülékre. Ehelyett tegye a következő lépéseket:

1. Állítsa be az Azure-t a Kiszolgálói értékelés használatára.
1. Az első értékeléshez hozzon létre egy Azure-projektet, és adja hozzá a Kiszolgálóértékelés eszközt.
1. Töltsön le egy CSV-sablont, és adjon hozzá kiszolgálóadatokat.
1. Importálja a sablont a Kiszolgálóértékelés alkalmazásba.
1. Fedezze fel az importálással hozzáadott kiszolgálókat, gyűjtse össze őket egy csoportba, és futtasson értékelést a csoportszámára.

## <a name="what-data-does-the-appliance-collect"></a>Milyen adatokat gyűjt a készülék?

Ha az Azure Migrate készüléket használja az értékeléshez, ismerje meg a [VMware](migrate-appliance.md#collected-data---vmware) és a [Hyper-V](migrate-appliance.md#collected-data---hyper-v)számára gyűjtött metaadatokat és teljesítményadatokat.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hogyan számítja ki a készülék a teljesítményadatokat?

Ha felderítésre használja a készüléket, az a következő lépésekkel gyűjti a számítási beállítások teljesítményadatait:

1. A készülék valós idejű mintapontot gyűjt.

    - **VMware virtuális gépek:** 20 másodpercenként mintavételi pontot gyűjtenek.
    - **Hyper-V virtuális gépek:** 30 másodpercenként mintavételi pontot gyűjtenek.
    - **Fizikai kiszolgálók**: Ötpercenként mintavételi pontot gyűjtünk.

1. A készülék egyesíti a mintapontokat, hogy 10 percenként egyetlen adatpontot hozzon létre. Az adatpont létrehozásához a készülék kiválasztja a csúcsértékeket az összes mintából. Ezután elküldi az adatpontot az Azure-ba.
1. A Server Assessment az elmúlt hónap összes 10 perces adatpontjait tárolja.
1. Amikor létrehoz egy értékelést, a Kiszolgálói értékelés azonosítja a megfelelő adatpontot, amelyet a jogok hoz. Az azonosítás a *teljesítményelőzmények* és a *percentilis-kihasználtság*százalékos értékein alapul.

    - Ha például a teljesítményelőzmények egy hét, és a percentilis kihasználtsága a 95. Növekvő sorrendbe rendezi őket, és kiválasztja a 95.
    - A 95 százalékos érték gondoskodik arról, hogy figyelmen kívül hagyja a kiugró értékeket, amelyek szerepelhetnek, ha a 99 percentilist választotta.
    - Ha az adott időszak csúcshasználatát szeretné kiválasztani, és nem szeretne kihagyni egyetlen kiugró értéket sem, válassza a 99 százalékos százalékos százalékos kihasználtságot.

1. Ezt az értéket megszorozzuk a komforttényezővel, hogy a készülék által gyűjtött mérőszámok tényleges teljesítménykihasználási adatait megkapjuk:

    - Processzorhasználat
    - RAM-kihasználtság
    - Disk IOPS (olvasás és írás)
    - Lemez átviteli -áteresztése (olvasás és írás)
    - Hálózati átviteli -át (be- és kifelé)

## <a name="how-are-assessments-calculated"></a>Hogyan számítják ki az értékeléseket?

A Kiszolgálóértékelés a helyszíni gépek metaadatait és teljesítményadatait használja az értékelések kiszámításához. Ha telepíti az Azure Migrate készüléket, az értékelés a készülék által gyűjtött adatokat használja. Ha azonban csv-fájllal importált értékelést futtat, megadja a számítás metaadatait.

A számítások a következő három szakaszban történnek:

1. **Azure-készenlét kiszámítása:** Mérje fel, hogy a gépek alkalmasak-e az Azure-ba való migrálásra.
1. **Méretezési javaslatok kiszámítása:** Becslés számítási, tárolási és hálózati méretezés.
1. **Havi költségek kiszámítása:** Számítsa ki a gépek áttelepítés utáni futtatásának becsült havi számítási és tárolási költségeit az Azure-ban.

A számítások az előző sorrendben vannak. A gépkiszolgáló csak akkor lép át egy későbbi szakaszra, ha az előzőt is átmegy. Ha például egy kiszolgáló meghibásodik az Azure-készenléti szakaszban, akkor az Azure számára alkalmatlanként van megjelölve. A méretezési és költségszámítások nem az adott kiszolgálón történik.

## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

A kiszolgálóértékelés ben az alábbiak szerepelnek:

Tulajdonság | Részletek
--- | ---
**Célhely** | Az a hely, ahhoz a helyhez, ahhoz, ahhoz, ahhoz, hogy áttelepüljen. A Kiszolgálóértékelés jelenleg a következő célAzure-régiókat támogatja:<br/><br/> Ausztrália Kelet, Ausztrália Délkelet, Brazília Déli, Kanada Középső, Kanada Kelet-, Közép-India, Központi USA, Kína Kelet-, Kína Észak-, Kelet-Ázsia, Kelet-USA 2, Németország Középső, Németország Északkelet, Japán Kelet, Japán Nyugat, Korea Központi, Korea Dél, Észak-Közép-USA, Észak-Európa, Dél-Közép USA, Délkelet-Ázsia, Dél-India, UK Déli, EGYESÜLT Királyság Nyugati, Egyesült Államok Gov Arizona, US Gov Texas, US Virginia Gov , Usa nyugati középső régiója, Nyugat-Európa, Nyugat-India, USA nyugati régiója és USA nyugati régiója 2.
**Céltároló lemez (méretezéskor)** | Az Azure-beli tároláshoz használandó lemez típusa. <br/><br/> Adja meg a céltároló lemezt prémium szintű felügyelt, standard SSD-vel vagy standard HDD-vel kezeltként.
**Céltároló lemez (teljesítményalapú méretezés)** | A céltároló lemez típusát adja meg automatikus, prémium szintű felügyelt, standard HDD-vel kezelt vagy standard SSD-vel kezeltként.<br/><br/> **Automatikus**: A lemezre vonatkozó javaslat a lemezek teljesítményadataira, azaz az IOPS-ra és az átviteli teljesítményre épül.<br/><br/>**Prémium vagy standard:** Az értékelés egy lemeztermékváltozatot javasol a kiválasztott tárolási típuson belül.<br/><br/> Ha 99,9%-os egypéldányos virtuálisgép-szolgáltatásszintű szerződést (SLA) szeretne, fontolja meg a prémium szintű felügyelt lemezek használatát. Ez a használat biztosítja, hogy az értékelésben szereplő összes lemez prémium szintű felügyelt lemezként ajánlott.<br/><br/> Az Azure Migrate csak a felügyelt lemezeket támogatja az áttelepítési felméréshez.
**Azure által fenntartott virtuálisgép-példányok** | A [fenntartott példányokat](https://azure.microsoft.com/pricing/reserved-vm-instances/) határozza meg, hogy az értékelésben lévő költségbecslések figyelembe vegyék azokat.<br/><br/> Az Azure Migrate jelenleg csak a használatra szánt fizetéses ajánlatok esetén támogatja az Azure fenntartott virtuálisgép-példányokat.
**Méretezési feltételek** | Az Azure-virtuális gép megfelelő méretezése.<br/><br/> Méretezésként vagy teljesítményalapú méretezésként használható.
**Teljesítményelőzmények** | Teljesítményalapú méretezéssel használható. A teljesítményelőzmények a teljesítményadatok kiértékelésekor használt időtartamot határozzák meg.
**Százalékos kihasználtság** | Teljesítményalapú méretezéssel használható. Percentilis-kihasználtság a jogokhoz használt teljesítményminta percentilis értékét adja meg.
**Virtuálisgép-sorozatok** | Az Azure virtuális gép sorozat, amely meg szeretné fontolni a jogok. Ha például nem rendelkezik olyan éles környezettel, amelynek A-sorozatú virtuális gépeket kell az Azure-ban, kizárhatja az A-sorozatokat a sorozatok listájából.
**Kényelmi faktor** | Az értékelés során használt puffer. A processzorra, a RAM-ra, a lemezre és a virtuális gépek hálózati kihasználtsági adataira alkalmazza. Ez figyelembe a kérdések, mint a szezonális használat, rövid teljesítmény előzmények, és valószínűleg növeli a jövőbeli használat.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsításáltalában kétmagos virtuális gép. A 2.0-s komforttényezővel az eredmény egy négymagos virtuális gép.
**Ajánlat** | Az [Azure-ajánlat,](https://azure.microsoft.com/support/legal/offer-details/) amelyben regisztrált. A Kiszolgálóértékelés az ajánlat költségét becsüli meg.
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott előfizetés-specifikus kedvezmények. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Az azure-beli virtuális gépek időtartama a hónap és a nap napjaiban, amelyek nem futnak folyamatosan. A költségbecslések ezen időtartamon alapulnak.<br/><br/> Az alapértelmezett értékek a havi 31 nap és a napi 24 óra.
**Azure Hybrid Benefit** | Itt adható meg, hogy rendelkezik-e szoftvergaranciával, és jogosult-e az [Azure Hybrid Benefit használatára.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Ha a beállítás alapértelmezett értéke "Igen", a Windows-tól eltérő operációs rendszerek Azure-árait veszi figyelembe a Windows virtuális gépek.

Tekintse át az értékelés létrehozásának [gyakorlati tanácsait](best-practices-assessment.md) a kiszolgálóértékeléssel.

## <a name="calculate-readiness"></a>Készültség számítása

Nem minden gép alkalmas az Azure-ban való futtatásra. A Kiszolgálóértékelés felméri az összes helyszíni gépet, és készenléti kategóriát rendel hozzájuk.

- **Készen áll az Azure-ra:** A gép módosítások nélkül áttelepíthető az Azure-ba. Az Azure-ban fog kezdődni teljes Körű Azure-támogatással.
- **Feltételesen készen áll az Azure-ra:** Előfordulhat, hogy a gép elindul az Azure-ban, de előfordulhat, hogy nem rendelkezik teljes Azure-támogatással. Az Azure például nem támogatja a Windows Server egy régi verzióját futtató gépet. Óvatosnak kell lennie, mielőtt ezeket a gépeket az Azure-ba telepíti át. A készenléti problémák megoldásához kövesse az értékelés által javasolhiba-útmutatást.
- **Nem áll készen az Azure-ra:** A gép nem indul el az Azure-ban. Például ha egy helyszíni gép lemeze 64 TB-nál több, az Azure nem üzemeltetheti a gépet. Kövesse a szervizelési útmutatót a probléma megoldásához az áttelepítés előtt.
- **Készenlét ismeretlen:** Az Azure Migrate nem tudja meghatározni a számítógép készenlétét a nem megfelelő metaadatok miatt.

A készenlét kiszámításához a Kiszolgálóértékelés áttekinti a számítógép tulajdonságait és az operációs rendszer beállításait az alábbi táblázatokban összegezve.

### <a name="machine-properties"></a>A gép tulajdonságai

A Kiszolgáló-felmérés áttekinti a helyszíni virtuális gép következő tulajdonságait, és megállapítja, hogy futtatható-e az Azure-on.

Tulajdonság | Részletek | Az Azure-ra való felkészültség állapota
--- | --- | ---
**Rendszerindítás típusa** | Az Azure nem UEFI-vel, hanem rendszerindítási típusú virtuális gépeket is támogat. | Feltételesen készen áll, ha a rendszerindítástípusa UEFI
**Cores** | Minden gép legfeljebb 128 maggal rendelkezhet, amely az Azure virtuális gép által támogatott maximális szám.<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate a használt magokat veszi összehasonlításra. Ha az értékelési beállítások kényelmi tényezőt határoznak meg, a felhasznált magok számát megszorozza a komforttényező.<br/><br/> Ha nincs teljesítményelőzmények, az Azure Migrate a kiosztott magokat használja a kényelmi tényező alkalmazása nélkül. | Kész, ha a magok száma a korláton belül van
**Ram** | Minden gép legfeljebb 3892 GB RAM-mal rendelkezhet, amely az&nbsp;Azure M sorozatú Standard_M128m<sup>2</sup> virtuális gép által támogatott maximális méret. [További információ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).<br/><br/> Ha a teljesítményelőzmények elérhetők, az Azure Migrate a felhasznált RAM-ot veszi figyelembe az összehasonlításhoz. Ha meg van adva egy kényelmi tényező, a felhasznált RAM-ot megszorozzuk a komforttényezővel.<br/><br/> Ha nincs előzmény, a kiosztott RAM-ot a komforttényező alkalmazása nélkül használják.<br/><br/> | Készen áll, ha a RAM mennyisége a korláton belül van
**Tárolólemez** | A lemez lefoglalt mérete nem lehet nagyobb 32 TB-nál. Bár az Azure támogatja a 64 TB-os lemezek et az Azure Ultra SSD-lemezekkel, az Azure Migrate: Server Assessment jelenleg 32 TB-ot keres lemezméret-korlátként, mert még nem támogatja az Ultra SSD-t. <br/><br/> A számítógéphez csatlakoztatott lemezek számának, beleértve az operációsrendszer-lemezt is, legkevesebbnek kell lennie. | Készen áll, ha a lemez mérete és száma a határokon belül van
**Hálózat** | A számítógéphez legfeljebb 32 hálózati csatoló (NIC) csatlakoztatható. | Készen áll, ha a hálózati adapterek száma a korláton belül van

### <a name="guest-operating-system"></a>Vendég operációs rendszer

A virtuális gép tulajdonságainak áttekintése mellett a Server Assessment megvizsgálja a számítógép vendég operációs rendszerét, hogy megállapítsa, futtatható-e az Azure-on.

> [!NOTE]
> A VMware virtuális gépek vendégelemzésének kezeléséhez a Server Assessment a vCenter Server ben a virtuális géphez megadott operációs rendszert használja. A VMware-en futó Linux-virtuális gépek esetében a Server Assessment jelenleg nem azonosítja a vendég operációs rendszer kernelverzióját.

A Kiszolgálófelmérés a következő logikát használja az Azure operációs rendszer alapján való felkészültségének azonosítására:

**Operációs rendszer** | **Részletek** | **Az Azure-ra való felkészültség állapota**
--- | --- | ---
Windows Server 2016 és az összes sp | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-ra.
Windows Server 2012 R2 és az összes SP | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-ra.
Windows Server 2012 és az összes sp | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-ra.
Windows Server 2008 R2 az összes sp-vel | Az Azure teljes körű támogatást nyújt.| Készen áll az Azure-ra.
Windows Server 2008 (32 és 64 bites) | Az Azure teljes körű támogatást nyújt. | Készen áll az Azure-ra.
Windows Server 2003 és Windows Server 2003 R2 | Ezek az operációs rendszerek már elmúltak a támogatási dátumok, és [egyéni támogatási szerződésre (CSA)](https://aka.ms/WSosstatement) van szükségük az Azure-beli támogatáshoz. | Feltételesen készen áll az Azure-ra. Fontolja meg az operációs rendszer frissítését, mielőtt áttérne az Azure-ba.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 és MS-DOS | Ezek az operációs rendszerek átmentek a támogatási dátumokon. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Windows 7, Windows 8 és Windows 10 | Az Azure [csak Visual Studio-előfizetéssel](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) nyújt támogatást. | Feltételesen készen áll az Azure-ra.
Windows 10 Pro | Az Azure támogatást nyújt a [több-bérlős üzemeltetési jogokhoz.](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) | Feltételesen készen áll az Azure-ra.
Windows Vista és Windows XP Professional | Ezek az operációs rendszerek átmentek a támogatási dátumokon. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy frissítse az operációs rendszer az Azure-ba való áttelepítés előtt.
Linux | Tekintse meg az Azure által támogatott [Linux operációs rendszereket.](../virtual-machines/linux/endorsed-distros.md) Más Linux operációs rendszerek is elindulhatnak az Azure-ban. De azt javasoljuk, hogy frissítse az operációs rendszer egy jóváhagyott verzióra, mielőtt az Azure-ba való áttelepítés. | Készen áll az Azure-ra, ha a verzió t.<br/><br/>Feltételesen kész, ha a verzió nincs jóváhagyva.
Más operációs rendszerek, például az Oracle Solaris, az Apple macOS és a FreeBSD | Az Azure nem támogatja ezeket az operációs rendszereket. Előfordulhat, hogy a gép elindul az Azure-ban, de az Azure nem nyújt operációs rendszer támogatást. | Feltételesen készen áll az Azure-ra. Azt javasoljuk, hogy az Azure-ba való áttelepítés előtt telepítsen egy támogatott operációs rendszert.  
A vCenter-kiszolgálón **másként** megadott operációs rendszer | Az Azure Migrate ebben az esetben nem tudja azonosítani az operációs rendszert. | Ismeretlen készenlét. Győződjön meg arról, hogy az Azure támogatja a virtuális gépen futó operációs rendszert.
32 bites operációs rendszerek | Előfordulhat, hogy a gép elindul az Azure-ban, de előfordulhat, hogy az Azure nem nyújt teljes körű támogatást. | Feltételesen készen áll az Azure-ra. Fontolja meg a 64 bites operációs rendszerre való frissítést, mielőtt átkontatna az Azure-ba.

## <a name="calculating-sizing"></a>Méretezés számítása

Miután a gép készen áll az Azure-ra, a Server Assessment méretezési javaslatokat tesz. Ezek a javaslatok azonosítják az Azure virtuális gép és a lemez termékváltozat. A méretezési számítások attól függnek, hogy a helyszíni méretezést vagy a teljesítményalapú méretezést használja-e.

### <a name="calculate-sizing-as-is-on-premises"></a>Méretezés számítása (a hogy van a helyszínen)

 Ha a helyszíni méretezés, a kiszolgálóértékelése nem veszi figyelembe a virtuális gépek és lemezek teljesítményelőzményeit.

- **Számítási méretezés:** A kiszolgálói felmérés egy Azure vm termékváltozatot foglal le a helyszíni lefoglalt méret alapján.
- **Tárolás és lemezméretezés**: A Kiszolgálófelmérés az értékelési tulajdonságokban megadott tárolási típust vizsgálja, és a megfelelő lemeztípust ajánlja. A lehetséges tárolási típusok a standard HDD, standard SSD és Premium. Az alapértelmezett tárolási típus a Prémium.
- **Hálózati méretezés**: A kiszolgálófelmérés figyelembe veszi a helyszíni számítógép hálózati adapterét.

### <a name="calculate-sizing-performance-based"></a>Méretezés számítása (teljesítményalapú)

Ha teljesítményalapú méretezést használ, a Kiszolgálófelmérés a következőképpen tesz méretezési javaslatokat:

- A Kiszolgálófelmérés a számítógép teljesítményelőzményeit veszi figyelembe a virtuális gép méretének és lemeztípusának azonosításához az Azure-ban.
- Ha csv-fájllal importál kiszolgálókat, a program a megadott értékeket használja. Ez a módszer különösen akkor hasznos, ha túlterhelt a helyszíni gép, a kihasználtság alacsony, és szeretné, hogy rightsize az Azure virtuális gép a költségek csökkentése érdekében.
- Ha nem szeretné használni a teljesítményadatokat, állítsa vissza a méretezési feltételeket a helyszíni állapotban, az előző szakaszban leírtak szerint.

#### <a name="calculate-storage-sizing"></a>Tárolási méretezés számítása

A tárolási méretezéshez az Azure Migrate megpróbálja leképezni a számítógéphez csatlakoztatott minden egyes lemezt egy Azure-lemezhez. A méretezés a következőképpen működik:

1. Server Assessment hozzáadja a lemez olvasási és írási IOPS-át a szükséges teljes IOPS leolvasásához. Hasonlóképpen hozzáadja az olvasási és írási átviteli értékeket az egyes lemezek teljes átviteli értékének leolvasásához.
1. Ha a tárolási típust automatikusként adta meg, a kiválasztott típus a tényleges IOPS- és átviteli-átviteli értékeken alapul. A Kiszolgálófelmérés határozza meg, hogy a lemezt egy szabványos HDD-hez, standard szintű SSD-hez vagy prémium szintű lemezhez rendelje-e az Azure-ban. Ha a tárolótípus ezen lemeztípusok egyikére van beállítva, a Kiszolgálófelmérés megpróbál egy lemeztermékéket találni a kiválasztott tárolótípuson belül.
1. A lemezek a következőképpen vannak kiválasztva:
    - Ha a kiszolgálófelmérés nem talál egy lemezt a szükséges IOPS és átviteli, azt jelzi, hogy a gép nem megfelelő az Azure számára.
    - Ha a Kiszolgálófelmérés megfelelő lemezeket talál, kiválasztja azokat a lemezeket, amelyek támogatják az értékelési beállításokban megadott helyet.
    - Ha több jogosult lemez van, a Kiszolgálófelmérés a legalacsonyabb költségű lemezt választja ki.
    - Ha bármely lemez teljesítményadatai nem érhetők el, a konfigurációs lemez mérete a szabványos SSD-lemez azure-beli keresésére szolgál.

#### <a name="calculate-network-sizing"></a>Hálózati méretezés számítása

Server Assessment megpróbálja megtalálni az Azure virtuális gép, amely támogatja a helyszíni géphez csatlakoztatott hálózati adapterek számát és szükséges teljesítményét.

- A helyszíni virtuális gép hatékony hálózati teljesítményének leigazolása érdekében a Kiszolgálóértékelés összesíti az adatátviteli sebességet a gépről (hálózatról ki) az összes hálózati adapteren. Ezután alkalmazza a komfort tényező. Az eredményül kapott értéket használja egy Azure virtuális gép, amely támogatja a szükséges hálózati teljesítményt.
- A hálózati teljesítmény mellett a Server Assessment azt is megvizsgálja, hogy az Azure virtuális gép támogatja-e a szükséges számú hálózati adaptert.
- Ha a hálózati teljesítményadatok nem érhetők el, a Kiszolgálófelmérés csak a virtuális gépek méretezésének hálózati adapterszámát veszi figyelembe.

#### <a name="calculate-compute-sizing"></a>Számítási méretezés számítása

Miután kiszámítja a tárolási és hálózati követelményeket, a Server Assessment a CPU- és RAM-követelményeket veszi figyelembe a megfelelő virtuális gépméret kereséséhez az Azure-ban.

- Az Azure Migrate megvizsgálja a hatékonyan használt magok és a RAM megtalálni a megfelelő Azure virtuális gép méretét.
- Ha nem talál megfelelő méretet, a gép az Azure számára alkalmatlanként van megjelölve.
- Ha megfelelő méretet talál, az Azure Migrate alkalmazza a tárolási és hálózati számításokat. Ezután alkalmazza a hely- és tarifaszintű beállításokat a végső virtuálisgép-méretajánláshoz.
- Ha több lehetséges Azure-beli virtuálisgép-méret létezik, a rendszer a legalacsonyabb költségűt ajánlja.

## <a name="confidence-ratings-performance-based"></a>Megbízhatósági besorolások (teljesítményalapú)

Az Azure Migrate minden teljesítményalapú értékelése megbízhatósági minősítéssel van társítva. A minősítés egy (legalacsonyabb) és öt (legmagasabb) csillag között mozog. A megbízhatósági besorolás segítségével megbecsülheti az Azure Migrate által biztosított, a méretet.

- A megbízhatósági minősítés egy értékeléshez van rendelve. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- A teljesítményalapú méretezéshez a kiszolgálófelmérésnek szüksége van:
    - A CPU és a virtuális gép RAM kihasználtsági adatai.
    - A lemez IOPS és átviteli adatok minden lemez a virtuális géphez csatolt.
    - A hálózati I/O-hoz kezelni a teljesítmény-alapú méretezés minden hálózati adapter csatlakozik egy virtuális géphez.

Ha ezek közül a kihasználtsági számok bármelyike nem érhető el, a méretjavaslatok nem lehetnek megbízhatatlanok.

> [!NOTE]
> A megbízhatósági minősítések nincsenek hozzárendelve az importált CSV-fájllal értékelt kiszolgálókhoz. A minősítések szintén nem alkalmazhatók a helyszíni értékeléshez.

### <a name="ratings"></a>Minősítések

Ez a táblázat a rendelkezésre álló adatpontok százalékos arányától függő megbízhatósági minősítéseket mutatja:

   **Az adatpontok rendelkezésre állása** | **Megbízhatósági minősítés**
   --- | ---
   0-20% | 1 csillag
   21-40% | 2 csillag
   41-60% | 3 csillag
   61-80% | 4 csillag
   81-100% | 5 csillag

### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági besorolás

Íme néhány ok, amiért egy értékelés alacsony megbízhatósági értékelést kaphat:

- Nem profilozta a környezetet arra az időtartamra, amelyre az értékelést létrehozza. Ha például úgy hozza létre az értékelést, hogy a teljesítmény időtartama egy napra van állítva, legalább egy napot várnia kell a felderítés megkezdése után az összes adatpont gyűjtésére.
- Egyes virtuális gépek leálltak az értékelés kiszámításának ideje alatt. Ha valamelyik virtuális gép bizonyos ideig ki van kapcsolva, a Kiszolgálói értékelés nem tudja összegyűjteni az adott időszakra vonatkozó teljesítményadatokat.
- Néhány virtuális gép jött létre az értékelés kiszámításának ideje alatt. Tegyük fel például, hogy létrehozott egy értékelést az elmúlt hónap teljesítményelőzményeihez, de néhány virtuális gép csak egy héttel ezelőtt jött létre. Az új virtuális gépek teljesítményelőzményei nem léteznek a teljes időtartamig.

> [!NOTE]
> Ha bármely értékelés megbízhatósági besorolása kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék profilálja a környezetet, majd újraszámítja az értékelést. Ellenkező esetben a teljesítményalapú méretezés megbízhatatlan lehet. Ebben az esetben azt javasoljuk, hogy az értékelést a helyszíni méretezésre váltsa át.

## <a name="calculate-monthly-costs"></a>Havi költségek számítása

A méretezési javaslatok befejezése után az Azure Migrate kiszámítja az áttelepítés utáni számítási és tárolási költségeket.

- **Számítási költség:** Az Azure Migrate az ajánlott Azure virtuális gép méretét és az Azure Billing API-t használja a virtuális gép havi költségének kiszámításához.

    A számítás figyelembe veszi a következőket:
    - Operációs rendszer
    - Szoftverbiztosítás
    - Fenntartott példányok
    - Virtuális gép üzemideje
    - Hely
    - Pénznem beállításai

    A Kiszolgálóértékelés összesíti a költségeket az összes gépen a teljes havi számítási költség kiszámításához.

- **Tárolási költség:** A gép havi tárolási költségét a számítógéphez csatlakoztatott összes lemez havi költségének összesítésével számítjuk ki.

    A Server Assessment az összes gép tárolási költségeinek összesítésével számítja ki a teljes havi tárolási költséget. Jelenleg a számítás nem veszi figyelembe az értékelési beállításokban megadott ajánlatokat.

A költségek az értékelési beállításokban megadott pénznemben jelennek meg.

## <a name="next-steps"></a>További lépések

[Tekintse át](best-practices-assessment.md) az értékelések készítésére vonatkozó bevált gyakorlatokat. 

- További információ a [VMware virtuális gépek,](tutorial-prepare-vmware.md) [a Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a fizikai kiszolgálók értékelésének [futtatásáról.](tutorial-prepare-physical.md)
- További információ a [CSV-fájllal importált](tutorial-assess-import.md)kiszolgálók értékeléséről.
- További információ a [függőségi megjelenítés](concepts-dependency-visualization.md)beállításáról.
