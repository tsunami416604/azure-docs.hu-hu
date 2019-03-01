---
title: Az Azure Migrate bemutatása | Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 01/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e2cafe0c8c5d8194519428839ec3c73f5dc726c0
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992029"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Az Azure Migrate szolgáltatás a helyszíni számítási feladatokat értékeli ki az Azure-ra történő migráláshoz. A szolgáltatás felméri a helyszíni gépek migrálásra való alkalmasságát, elvégzi a teljesítményalapú méretezést, és költségbecslést ad a helyi számítógépek Azure-ban történő futtatásával kapcsolatban. Ha átemeléses migráláson gondolkodik, vagy épp a migrálási szakaszok felmérésének elején jár, hasznát veheti ennek a szolgáltatásnak. Az értékelés után olyan szolgáltatásokat használhat a számítógépek Azure-ra történő migrálásához, mint az [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) vagy az [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="why-use-azure-migrate"></a>Miért javasolt az Azure Migrate használata?

Az Azure Migrate az alábbiakban nyújt segítséget:

- **Az Azure készen állásának felmérése**: Annak ellenőrzéséhez, hogy a helyszíni gépek Azure-ban futó alkalmasak.
- **Javaslatok a méretekkel kapcsolatban**: Méretezési javaslatokat kaphat az Azure-beli virtuális gépek, a helyszíni virtuális gépek korábbi teljesítménye alapján.
- **Becsült havi költség**: Becsült költségek a helyszíni gépek Azure-ban futó beolvasása.  
- **Megbízható migrálás**: Gépek, amelyek fog értékelni és migrálása együtt csoportok létrehozása a helyszíni gépek függőségeit, jelenítheti meg.

## <a name="current-limitations"></a>Aktuális korlátozások

- Csak a helyszíni VMware virtuális gépeket értékelheti ki az Azure-beli virtuális gépekre való migráláshoz. A VMware virtuális gépeket (verziójú 5.5-ös, 6.0-s, 6.5-ös vagy 6.7) vCenter Servernek kell felügyelnie.
- Hyper-V virtuális gépek felméréséhez az [Azure Site Recovery Deployment Plannert](https://aka.ms/asr-dp-hyperv-doc), fizikai gépek felméréséhez pedig [partnereszközeinket](https://azure.microsoft.com/migration/partners/) használja.
- Egyetlen felderítéssel legfeljebb 1500 virtuális gépet, egyetlen projekt részeként pedig szintén legfeljebb 1500 virtuális gépet deríthet fel. Egyetlen értékeléssel emellett legfeljebb 1500 virtuális gépet értékelhet.
- Ha egy nagyobb méretű környezetet szeretne felderíteni, feloszthatja a felderítést, és létrehozhat több projektet. [További információk](how-to-scale-assessment.md). Az Azure Migrate előfizetésenként legfeljebb 20 projektet támogat.
- Az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
-  Azure Migrate-projektet csak az alábbi földrajzi területeken hozhat létre. Azonban ez nem korlátozza az értékelések más létrehozásának lehetősége cél Azure-helyen.
    **Régiócsoport** | **Tárolási hely**
    --- | ---
    Azure Government | USA-beli államigazgatás – Virginia
    Ázsia | Délkelet-Ázsia
    Európa | Észak-Európában és Nyugat-Európa
    Egyesült Államok | USA keleti RÉGIÓJA és USA nyugati középső RÉGIÓJA

    A migrálási projekthez tartozó földrajzi a helyszíni környezetből felderített metaadatok tárolására szolgál. Metaadatok alapján a migrálási projekt megadott földrajzi régiók egyikében tárolódik. Ha függőségmegjelenítést használ, hozzon létre egy új Log Analytics-munkaterületet, a munkaterület létrehozását a projekt ugyanabban a régióban.
- A függőségek képi megjelenítésének funkcióival nem érhető el az Azure Government szolgáltatásban.


## <a name="what-do-i-need-to-pay-for"></a>Mi az, amiért fizetnem kell?

[Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.


## <a name="whats-in-an-assessment"></a>Mit tartalmaz egy értékelés?

Az értékelési beállítások az adott igényekhez szabhatók. Az értékelési tulajdonságokat az alábbi táblázat foglalja össze.

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az Azure-beli hely, ahová a migrálást szeretné végezni.<br/><br/>Az Azure Migrate jelenleg támogatja 33 régióban áttelepítési célhelyeket. [Régiók megtekintése](https://azure.microsoft.com/global-infrastructure/services/). Alapértelmezés szerint a célrégióban az USA keleti RÉGIÓJA van beállítva.
**Tárolás típusa** | A felügyelt lemezeket szeretne lefoglalni az értékelés részét képező összes virtuális gép típusát. Ha a méretezési feltétel teljesítményalapú *helyszíni méretezési* is megadhat a céllemez típusa vagy a prémium szintű lemezek (alapértelmezett), standard SSD-lemez vagy HDD a standard szintű lemezek. A *teljesítményalapú*, a fenti lehetőségek, valamint akkor is választhatja, amely biztosítja, hogy a méretezési javaslat lemezt automatikusan történik a virtuális gépek teljesítményadatait alapján automatikus. Ha például szeretne elérni egy [egypéldányos virtuális gép SLA 99,9 %-os](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), előfordulhat, hogy szeretne megadni a tárolási típust, amely biztosítja, hogy azok a lemezek az értékelés rendszer azt javasolja, prémium szintű felügyelt lemezek prémium szintű felügyelt lemezek. Vegye figyelembe, hogy az Azure Migrate kizárólag a felügyelt lemezek migrálásfelmérését támogatja.
**Fenntartott példányok** |  Az értékelés figyelembe veszi, hogy vannak-e [fenntartott példányai](https://azure.microsoft.com/pricing/reserved-vm-instances/) az Azure-ban. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Méretezési feltétel** | Méretezési alapulhat **teljesítményelőzmények** a helyszíni virtuális gépek (alapértelmezett), vagy **helyszíni**, teljesítményelőzmények figyelembe vétele nélkül.
**Teljesítményelőzmények** | Alapértelmezés szerint az Azure Migrate a helyszíni gépek teljesítményét az utolsó nap teljesítményelőzményei alapján, 95%-os százalékértékkel értékeli ki.
**Kényelmi faktor** | Az Azure Migrate az értékelés során figyelembe veszi a puffert (kényelmi faktor). Ezt a puffert a rendszer a virtuális gépek gépkihasználtsági adatai (CPU, memória, lemez és hálózat) mellett alkalmazza. A kényelmi faktor áll az olyan problémák mögött, mint a szezonális használat, a rövid teljesítményelőzmények és a jövőbeli használat várható növekedése.<br/><br/> Például egy 10 magos virtuális gép 20%-os kihasználtsággal normál esetben egy 2 magos virtuális gépnek felel meg. 2.0x-es kényelmi faktorral azonban az eredmény ehelyett egy 4 magos virtuális gép. Az alapértelmezett kényelmi beállítás 1.3x.
**Virtuálisgép-sorozatok** | Méretbecslésekhez használt virtuálisgép-sorozatok. Például egy olyan éles környezetben, amelyet nem szeretne A-sorozatú virtuális gépekre migrálni az Azure-ban, kizárhatja a listából vagy sorozatból az A-sorozatot. Ilyenkor a méretezés csak a kiválasztott sorozatokat veszi figyelembe.   
**Pénznem** | A számlázás pénzneme. Az alapértelmezett érték az amerikai dollár.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott, az előfizetéshez tartozó kedvezmények. Az alapértelmezett beállítás 0%.
**Virtuális gép üzemideje** | Ha nem tervezi a virtuális gépek 24 x 7 fut az Azure-ban, amely azok kellene futnia adhat meg az időtartamot (havonta napok száma) és napi óraszám, és a költségbecslések elkészítését ennek megfelelően történik. Az alapértelmezett értéke 31 nap / hó és napi 24 órában.
**Azure-ajánlat** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/), amelyre regisztrált. Az Azure Migrate ez alapján becsüli meg a költségeket.
**Azure Hybrid Benefit** | Az értékelés figyelembe veszi, hogy rendelkezik-e szoftvergaranciával, és jogosult-e az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/) kedvezményes díjainak igénybevételére.

## <a name="how-does-azure-migrate-work"></a>Hogyan működik az Azure Migrate?

1.  Hozzon létre egy Azure Migrate projektet.
2.  Az Azure Migrate egy gyűjtőberendezésnek nevezett helyszíni virtuális gépet használ a helyszíni gépek adatainak felderítésére. A berendezés létrehozásához töltse le a telepítőfájlt Open Virtualization Appliance (.ova) formátumban, majd importálja virtuális gépként a helyszíni vCenter Serverre.
3. Csatlakozzon a virtuális géphez a vCenter Serverről, és csatlakozás közben adjon meg hozzá egy új jelszót.
4. Futtassa a gyűjtőt a virtuális gépen a felderítés elindításához.
5. A gyűjtő a VMware PowerCLI-parancsmagok segítségével összegyűjti a virtuális gépek metaadatait. A felderítés ügynök nélkül történik, és nem telepít semmit a VMware-gazdagépekre vagy a virtuális gépekre. Az összegyűjtött metaadatok a virtuális gépek adatait is tartalmazzák (magok, memória, lemezek, lemezméretek és hálózati adapterek). A gyűjtő ezenkívül a virtuális gépek teljesítményadatait is gyűjti, például a processzor- és memóriahasználatot, a lemez IOPS-t, a lemezek átviteli sebességét (MB/s) és a hálózati kimenetet (MB/s).
5.  A rendszer továbbítja a metaadatokat az Azure Migrate projektnek. Ezeket az Azure Portalon tekintheti meg.
6.  Csoportokba rendezheti a felderített virtuális gépeket az értékeléshez. Például egy csoportba helyezheti az azonos alkalmazást futtató virtuális gépeket. A még pontosabb csoportosítás érdekében megtekintheti egy adott géphez vagy egy csoport gépeihez tartozó függőségeket, illetve a csoportok összes gépéhez tartozókat és pontosíthatja a csoportot.
7.  A csoport meghatározását követően hozzon létre hozzá egy értékelést.
8.  Az értékelés befejeződése után megtekintheti azt a portálon, vagy letöltheti Excel-formátumban.

  ![Az Azure Migrate architektúrája](./media/migration-planner-overview/overview-1.png)

## <a name="what-are-the-port-requirements"></a>Milyen követelmények vonatkoznak a portokra?

A táblázat összefoglalja az Azure Migrate kommunikációjához szükséges portokat.

Összetevő | Kommunikációs cél |  Részletek
--- | --- |---
Gyűjtő  | Azure Migrate szolgáltatás | A gyűjtő a 443-as SSL-porton keresztül csatlakozik a szolgáltatáshoz.
Gyűjtő | vCenter Server | Alapértelmezés szerint a gyűjtő a 443-as porton csatlakozik a vCenter Serverhez. Ha a kiszolgáló egy másik porton figyel, azt kimenő portként kell konfigurálni a gyűjtő virtuális gépen.
Helyszíni virtuális gép | Log Analytics-munkaterület | [443-as TCP] | [A Microsoft Monitoring Agent (MMA)](../log-analytics/log-analytics-windows-agent.md) szeretne csatlakozni az Azure Monitor naplóira 443-as TCP-portot használja. Erre a portra csak akkor van szükség, ha a függőségmegjelenítési funkciót használja, amelyhez szükség van az MMA-ügynökre.


## <a name="what-happens-after-assessment"></a>Mi történik az értékelés után?

Miután értékelte a helyszíni gépeket, többféle eszközzel is végrehajthatja a migrálást:

- **Az Azure Site Recovery**: Az Azure Site Recovery segítségével Azure-bA migrálásához. Ehhez [elő kell készítenie a szükséges Azure-összetevőket](../site-recovery/tutorial-prepare-azure.md), például a tárfiókot és a virtuális hálózatot. A helyszínen [a VMware-környezetet kell előkészítenie](../site-recovery/vmware-azure-tutorial-prepare-on-premises.md). Ha minden készen áll, állítsa be és engedélyezze az Azure-ba való replikációt, majd migrálja a virtuális gépeket. [További információk](../site-recovery/vmware-azure-tutorial.md).
- **Azure Database Migration**: Ha például az SQL Server, MySQL és Oracle-adatbázis a helyszíni gépeket futtat, akkor használhatja a [Azure Database Migration Service](../dms/dms-overview.md) áttelepítheti az Azure-bA.


## <a name="next-steps"></a>További lépések

- [Az oktatóanyag utasításait követve](tutorial-assessment-vmware.md) hozzon létre értékelést egy helyszíni VMware virtuális gép számára.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
