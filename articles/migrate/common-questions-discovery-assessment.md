---
title: Kérdések a felderítéssel, értékeléssel és függőségelemzéssel kapcsolatban az Azure Áttelepítése szolgáltatásban
description: Válaszok at kaphat a felderítéssel, értékeléssel és függőségelemzéssel kapcsolatos gyakori kérdésekre az Azure Migrate alkalmazásban.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 700e5318b66cdf4993a17b1dae85fb43f75ab035
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529773"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Felfedezés, értékelés és függőségi elemzés - Gyakori kérdések

Ez a cikk az Azure Migrate felderítésével, értékelésével és függőségelemzésével kapcsolatos gyakori kérdésekre ad választ. Ha egyéb kérdése van, ellenőrizze az alábbi forrásokat:

- [Általános kérdések az](resources-faq.md) Azure Áttelepítésével kapcsolatban
- Kérdések az [Azure Migrate készülékkel kapcsolatban](common-questions-appliance.md)
- Kérdések a [kiszolgáló áttelepítésével](common-questions-server-migration.md) kapcsolatban
- Kérdések megválaszolása az [Azure Áttelepítésfórumában](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Milyen földrajzi területek támogatottak a felderítéshez és az értékeléshez az Azure Migrate szolgáltatással?

Tekintse át a támogatott földrajzi köz- [és](migrate-support-matrix.md#supported-geographies-public-cloud) [kormányzati felhőket.](migrate-support-matrix.md#supported-geographies-azure-government)


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hány virtuális gépet fedezhetek fel egy készülékkel?

Akár 10 000 VMware virtuális gépet, akár 5000 Hyper-V virtuális gépet és akár 250 fizikai kiszolgálót is felfedezhet egyetlen készülék használatával. Ha több gépe van, olvassa el [a Hyper-V-felmérés méretezését,](scale-hyper-v-assessment.md) [a VMware-felmérés méretezését](scale-vmware-assessment.md)vagy [a fizikai kiszolgálói felmérés méretezését.](scale-physical-assessment.md)


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>A virtuális gép mérete megváltozott. Lefuttathatok még egy értékelést?

Az Azure Migrate berendezés folyamatosan gyűjti a helyszíni környezetadatait.  Az értékelés a helyszíni virtuális gépek időpont-időpontban pillanatképe. Ha módosítja a virtuális gép, amely értékelni szeretné a beállításokat, használja az újraszámítás lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hogyan fedezhetem fel a virtuális gépeket egy több-bérlős környezetben?

- **VMware:** Ha egy környezet meg van osztva a bérlők között, és nem szeretné felderíteni a bérlő virtuális gépeit egy másik bérlő előfizetésében, hozzon létre VMware vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépekhez férhetnek hozzá. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést az Azure Migrate készülékben.
- **Hyper-V**: A felderítés Hyper-V állomás hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V gazdagépet, jelenleg nincs módja a felderítés elkülönítése.  

## <a name="do-i-need-vcenter-server"></a>Szükségem van vCenter Server?

Igen, az Azure Migrate használatához vCenter Server vMware környezetben a felderítés végrehajtásához szükséges. Az Azure Migrate nem támogatja a vCenter Server által nem kezelt ESXi-állomások felderítését.

## <a name="what-are-the-sizing-options"></a>Milyen méretezési lehetőségek vannak?

A helyszíni méretezés, az Azure Migrate nem veszi figyelembe a virtuális gép teljesítményadatait az értékeléshez. Az Azure Migrate a helyszíni konfiguráció alapján értékeli a virtuális gépek méretét. A teljesítményalapú méretezéssel a méretezés a kihasználtsági adatokon alapul.

Ha például egy helyszíni virtuális gép négy maggal és 8 GB memóriával rendelkezik 50%-os CPU-kihasználtság és 50%-os memóriakihasználtság mellett:
- A helyszíni méretezés egy Azure virtuális gép termékváltozatát ajánlja, amely négy maggal és 8 GB memóriával rendelkezik.
- A teljesítményalapú méretezés két maggal és 4 GB memóriával rendelkezik, mivel a kihasználtsági százalék ot veszi figyelembe.

Hasonlóképpen, a lemezméretezés a méretezési feltételektől és a tárolási típustól függ:
- Ha a méretezési feltételek teljesítményalapúak, és a tárolási típus automatikus, az Azure Migrate veszi a lemez IOPS-és átviteli értékeit a rendszer figyelembe veszi, amikor azonosítja a céllemez típusát (Standard vagy Premium).
- Ha a méretezési feltételek teljesítményalapúak, és a tárolási típus prémium, az Azure Migrate prémium szintű lemeztermékváltozatot javasol a helyszíni lemez mérete alapján. Ugyanez a logika vonatkozik a lemez méretezése, ha a méretezés a helyszíni és a tárolási típus Standard vagy Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>A teljesítményelőzmények és a kihasználtság hatással van a méretezésre?

Igen, a teljesítményelőzmények és a kihasználtság hatással van az Azure Áttelepítés méretezésére.

### <a name="performance-history"></a>Teljesítményelőzmények

Csak teljesítményalapú méretezés esetén az Azure Migrate összegyűjti a helyszíni gépek teljesítményelőzményeit, majd a virtuális gép méretét és lemeztípusát ajánlja az Azure-ban:

1. A készülék folyamatosan profilozza a helyszíni környezetet, hogy 20 másodpercenként valós idejű kihasználtsági adatokat gyűjtsön.
1. A készülék összetekeri az összegyűjtött 20 másodperces mintákat, és 15 percenként egyetlen adatpontot hoz létre.
1. Az adatpont létrehozásához a készülék kiválasztja a csúcsértéket az összes 20 másodperces mintából.
1. A készülék elküldi az adatpontot az Azure-nak.

### <a name="utilization"></a>Hasznosítás

Amikor létrehoz egy értékelést az Azure-ban, a teljesítmény időtartamától és a teljesítményelőzmények százalékos értékétől függően, az Azure Migrate kiszámítja a tényleges kihasználtsági értéket, majd felméri azt.

Ha például a teljesítmény időtartamát egy napra, a percentilis értéket pedig 95 százalékosra állítja, az Azure Migrate növekvő sorrendbe rendezi a gyűjtő által az elmúlt napra küldött 15 perces mintavételi pontokat. A 95. percentilis értéket választja ki tényleges kihasználtságként.

A 95. percentilis érték használata biztosítja a kiugró értékek figyelmen kívül hagyását. Kiugró értékek is szerepelhetnek, ha az Azure Migrate a 99. Ha az adott időszak maximális használatát szeretné kihagyni, állítsa be az Azure Migrate beállítását a 99.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Miben különböznek az importálásalapú értékelések a felderítési forrással rendelkező értékelésektől, mint berendezés?

Az importálásalapú értékelések olyan gépeken létrehozott értékelések, amelyekcsv-fájl használatával importálva vannak az Azure Migrate szolgáltatásba. Csak négy mezőt kell importálni: kiszolgálónév, magok, memória és operációs rendszer. Íme néhány dolog, amit érdemes megjegyezni: 
 - A készenléti kritériumok kevésbé szigorúak a rendszerindító típus paraméterimportalapú értékelésében. Ha a rendszerindítási típus nincs megadva, akkor feltételezhető, hogy a gép BIOS-rendszerindítási típussal rendelkezik, és a gép nincs **feltételesen készként**megjelölve. A felderítési forrással rendelkező készülékben a készenlét **feltételesen készként** van megjelölve, ha a rendszerindítási típus hiányzik. Ez a különbség a készültségi számítás azért van, mert a felhasználók nem rendelkeznek az összes információt a gépek a korai szakaszában az áttelepítés tervezése, amikor az import-alapú értékelések et. 
 - A teljesítményalapú importálási értékelések a felhasználó által megadott kihasználtsági értéket használják a jobb méretezési számításokhoz. Mivel a kihasználtsági értéket a felhasználó adja meg, a **teljesítményelőzmények** és a **percentilis kihasználtsági** beállítások le vannak tiltva az értékelési tulajdonságokban. A felderítési forrással rendelkező készülék ben a kiválasztott percentilis érték a készülék által gyűjtött teljesítményadatokból kerül ki.

## <a name="what-is-dependency-visualization"></a>Mi a függőségi megjelenítés?

A függőségi megjelenítés segítségével felmérheti a virtuális gépek nagyobb megbízhatósággal áttelepítendő csoportjait. A függőségi megjelenítés átellenőrzi a számítógép-függőségeket az értékelés futtatása előtt. Ez segít biztosítani, hogy semmi sem marad hátra, és segít elkerülni a váratlan kimaradások, amikor áttelepíti az Azure-ba. Az Azure Migrate a Service Map-megoldást használja az Azure Monitorban a függőségi vizualizáció engedélyezéséhez. [További információ](concepts-dependency-visualization.md).

> [!NOTE]
> Függőségi megjelenítés nem érhető el az Azure Government.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Mi a különbség az ügynök-alapú és ügynök nélküli?

Az ügynök nélküli vizualizáció és az ügynökalapú megjelenítés közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak vmware virtuális gépek esetén érhető el. [Tekintse át a](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) támogatott operációs rendszereket. | Általános anamvaló (GA).
Ügynök | Nem kell ügynököket telepíteni azátveire telepíteni kívánt gépekre. | Az elemezni kívánt minden helyszíni gépen telepítendő ügynökök: A [Microsoft Monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)és a [Függőségi ügynök](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Előfeltételek | [Tekintse át](concepts-dependency-visualization.md#agentless-analysis) az előfeltételeket és a telepítési követelményeket. | [Tekintse át](concepts-dependency-visualization.md#agent-based-analysis) az előfeltételeket és a telepítési követelményeket.
Log Analytics | Nem szükséges. | Az Azure Migrate a [Service Map-megoldást](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) használja az [Azure Monitor naplóiban](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) a függőségi vizualizációhoz. [További információ](concepts-dependency-visualization.md#agent-based-analysis).
Működés | A tcp-kapcsolat adatait rögzíti a függőségi megjelenítéshez engedélyezett gépeken. A felderítés után ötperces időközönként gyűjti az adatokat. | A számítógépre telepített Szolgáltatástérkép-ügynökök adatokat gyűjtenek a TCP-folyamatokról és a bejövő/kimenő kapcsolatokról az egyes folyamatokhoz.
Adatok | A forrásgép kiszolgálójának neve, folyamata, alkalmazásneve.<br/><br/> A célszámítógép kiszolgálójának neve, folyamata, alkalmazásneve és portja. | A forrásgép kiszolgálójának neve, folyamata, alkalmazásneve.<br/><br/> A célszámítógép kiszolgálójának neve, folyamata, alkalmazásneve és portja.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és elérhető log analytics-lekérdezések. 
Vizualizáció | Az egykiszolgálós függőségi térkép egy óra és 30 nap között tekinthető meg. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> A térkép csak egy óra alatt tekinthető meg.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/> A csoport kiszolgálóinak hozzáadása és eltávolítása a térképnézetből.
Adatexportálás | Jelenleg nem tölthető le táblázatos formátumban. | Az adatok lekérdezhetők a Log Analytics segítségével.

## <a name="do-i-pay-for-dependency-visualization"></a>Fizetek a függőségi megjelenítésért?

Nem. További információ az [Azure Áttelepítés díjszabásáról.](https://azure.microsoft.com/pricing/details/azure-migrate/)

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Mit telepítsek az ügynökalapú függőségi vizualizációhoz?

Az ügynökalapú függőségi vizualizáció használatához töltsön le és telepítsen ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni:

- [Microsoft monitoring ügynök (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Ha olyan számítógépekkel rendelkezik, amelyek nem rendelkeznek internetkapcsolattal, töltse le és telepítse rájuk a Log Analytics-átjárót.

Ezekre az ügynökökre csak akkor van szüksége, ha ügynökalapú függőségi vizualizációt használ.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok meglévő munkaterületet?

Igen, az ügynök-alapú függőségi vizualizációhoz egy meglévő munkaterületet csatolhat az áttelepítési projekthez, és függőségi megjelenítésre használhatja. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatom a függőség vizualizációjáról szóló jelentést?

Nem, az ügynökalapú vizualizációfüggőségi vizualizációs jelentés nem exportálható. Az Azure Migrate azonban szolgáltatásleképezést használ, és a [Service Map REST API-val](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) json formátumban lekérheti a függőségeket.

## <a name="can-i-automate-agent-installation"></a>Automatizálhatom az ügynökök telepítését?

Ügynökalapú függőségi megjelenítés esetén:

- Parancsfájl használatával [telepítse a függőségi ügynököt.](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)
- MmA esetén [használja a parancssort vagy az automatizálást,](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)vagy használjon [parancsfájlt.](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)
- A parancsfájlok mellett a központi telepítési eszközök, például a Microsoft Endpoint Configuration Manager és az [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) is használhatja az ügynökök üzembe helyezését.

## <a name="what-operating-systems-does-mma-support"></a>Milyen operációs rendszereket támogat az MMA?

- Az [MMA által támogatott Windows operációs rendszerek listájának](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)megtekintése.
- Tekintse meg az [MMA által támogatott Linux operációs rendszerek](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)listáját.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Több mint egy órán keresztül jeleníthetem meg a függőségeket?

Az ügynökalapú vizualizációhoz legfeljebb egy órán keresztül jelenítheti meg a függőségeket. A megjelenítés maximális időtartama egy óra. Használhatja például a függőségi térkép en az időtartamot a tegnapi függőségek megtekintéséhez, de a függőségeket csak egy órás időszakban tekintheti meg. Azonban az Azure Monitor naplók segítségével [lekérdezheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) a függőségi adatok hosszabb ideig.

Ügynök nélküli vizualizáció esetén megtekintheti egyetlen kiszolgáló függőségi térképét egy óra és 30 nap közötti időtartamból.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Tudom elképzelni a függőségek csoportok több mint 10 virtuális gép?

Legfeljebb 10 virtuális gépből álló csoportok [függőségeit jelenítheti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) meg. Ha 10-nél több virtuális gépből álló csoporttal rendelkezik, azt javasoljuk, hogy ossza fel a csoportot kisebb csoportokra, majd jelenítse meg a függőségeket.

## <a name="next-steps"></a>További lépések

Olvassa el az [Azure Migrate áttekintését.](migrate-services-overview.md)
