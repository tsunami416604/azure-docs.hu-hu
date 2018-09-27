---
title: A Batch Rendering szolgáltatás képességei
description: Az Azure Batch szolgáltatásban meghatározott renderelési képességeket
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 6d79cdf279022320f654fbbeadb870f82db88cab
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392785"
---
# <a name="azure-batch-rendering-capabilities"></a>Az Azure Batch renderelési képességeket

Standard szintű Azure Batch-funkciók renderelési számítási feladatok és alkalmazások futtatására szolgálnak. A Batch renderelési számítási feladatok funkciók is tartalmaz.

Batch-fogalommal, beleértve a készletek, feladatok és tevékenységek áttekintését lásd: [Ez a cikk](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Batch-készletek

### <a name="rendering-application-installation"></a>Renderelési alkalmazások telepítése

Ha csak az előre telepített alkalmazásokkal kell használni a készlet konfigurációjában adható meg az Azure Marketplace-en renderelési Virtuálisgép-rendszerképet.

Van egy Windows 2016-rendszerképet és a egy CentOS lemezképet.  Az a [Azure Marketplace-en](https://azuremarketplace.microsoft.com), a Virtuálisgép-rendszerképek keresése a "batch rendering" található.

Egy példa készletkonfigurációt, lásd: a [Azure CLI-vel renderelési oktatóanyag](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Az Azure portal és a Batch Explorer adja meg a grafikus eszközök renderelési Virtuálisgép-rendszerkép kiválasztása, amikor létrehoz egy készletet.  Ha a Batch API-val, majd adja meg az alábbi tulajdonságértékeit [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) egy készlet létrehozásakor:

| Közzétevő | Ajánlat | SKU | Verzió |
|---------|---------|---------|--------|
| kötegelt | Megjelenítés – centos73 | Megjelenítés | legújabb |
| kötegelt | Megjelenítés – windows2016 | Megjelenítés | legújabb |

Ha további alkalmazásokat van szükség a virtuális gépek készletté más lehetőségek érhetők el:

* Egyéni lemezkép alapján standard Piactéri lemezképet:
  * így pontosan azokkal az alkalmazásokkal és verziókkal konfigurálhatja a virtuális gépet, amelyekre szüksége van. További információkért lásd: [hozzon létre egy virtuálisgép-készletek egyéni rendszerkép használatával](https://docs.microsoft.com/azure/batch/batch-custom-images). Autodesk és a Chaos Grouppal módosította Arnold és a V-Ray, egy Azure Batch szolgáltatás licencelési szemben érvényesítendő. Ellenőrizze, hogy ezeknek az alkalmazásoknak a támogatással, máskülönben a használatalapú fizetés licencelése nem fog működni a verziója van-e. Aktuális verziók a Maya és a 3ds Max nem igényelnek licenckiszolgálót távfelügyelt (a kötegelt/parancssori módban) futtatásakor. Ha nem biztos abban, hogy való ezt a lehetőséget kérje az Azure ügyfélszolgálatától.
* [Az alkalmazáscsomagok](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Csomag az alkalmazás fájljait egy vagy több ZIP-fájl használatával, és töltse fel az Azure Portalon adja meg a csomagot a tárolókészlet konfigurációját. Készlet virtuális gépek létrehozásakor a ZIP-fájlok letöltése, és a fájlok kibontása.
* Erőforrás-fájlok:
  * Alkalmazás fájlok feltöltése az Azure blob storage, és adja meg a fájlra mutató hivatkozást a [készlet indítási tevékenységet](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask). Készlet virtuális gépek jönnek létre, amikor a erőforrás fájlokat tölti be minden egyes virtuális Géphez.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Fizetési a használatban licenceinek előre telepített alkalmazások

Az alkalmazásokat fogja használni, és a egy díját kell megadni a készlet konfigurációjában.

* Adja meg a `applicationLicenses` tulajdonság amikor [készletet hoz létre egy](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  A következő értékeket a tömb karakterláncok – "v-Ray", "arnold", "3dsmax", "maya" adható meg.
* Ha megad egy vagy több alkalmazást, majd a költség, ezeknek az alkalmazásoknak hozzáadódik a virtuális gépek díját.  Alkalmazás árak szerepel a [Azure Batch díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Ha inkább a renderelési alkalmazások használatát a licenckiszolgáló csatlakozik, nem adja meg a `applicationLicenses` tulajdonság.

Az Azure portal vagy a Batch Explorer segítségével válassza ki az alkalmazások és az alkalmazás-díjszabás megjelenítése.

Ha egy alkalmazás használatát tett kísérlet, de az alkalmazás még nem lett megadva a a `applicationLicenses` tulajdonság a tárolókészlet konfigurációját, vagy nem a licenckiszolgáló, majd az alkalmazás végrehajtása meghiúsul, és a egy licencelési hiba és nullától eltérő kilépési kódot elérhetőséget biztosítja.

### <a name="environment-variables-for-pre-installed-applications"></a>Környezeti változók előre telepített alkalmazások

Az, hogy hozzon létre a parancssorban a renderelési feladatokhoz, meg kell adni a renderelési alkalmazások végrehajtható fájlok telepítési helyét.  Rendszerszintű környezeti változókat az Azure piactér-beli Virtuálisgép-lemezképek is használható, ahelyett hogy meg kellene adni a tényleges elérési utak lett létrehozva.  Ezek a környezeti változók vannak mellett a [standard szintű Batch környezeti változókat](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) minden feladathoz létrehozott.

|Alkalmazás|Alkalmazás futtatható|Környezeti változó|
|---------|---------|---------|
|Autodesk 3ds Max 2018.|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017.|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018.|render.exe|MAYA_2018_EXEC|
|A Chaos Group V-Ray önálló|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 parancssor|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 parancssor|kick.exe|ARNOLD_2018_EXEC|
|A Blender|Blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Az Azure Virtuálisgép-család

Egyéb számítási feladatokat, mint a renderelési alkalmazások rendszerre vonatkozó követelmények eltérőek lehetnek, és a teljesítményre vonatkozó követelmények eltérőek lehetnek, feladatok és projektek.  Virtuálisgép-családok számos érhetők el a követelményei – legalacsonyabb költségű Azure, legjobb ár-teljesítmény, ajánlott teljesítményét, és így tovább.
Egyes renderelési alkalmazások, például az Arnold, a CPU-alapú; mások, például a V-Ray és a Blender ciklusok használhatja a processzorokat és/vagy gpu-kkal.
Elérhető Virtuálisgép-család és Virtuálisgép-méretek leírását [tekintse meg a Virtuálisgép-típusairól és -méreteiről](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Alacsony prioritású virtuális gépek

Egyéb számítási feladatokat, mint a Batch-készletekben renderelést, amellyel alacsony prioritású virtuális gépek.  Alacsony prioritású virtuális gépek végrehajtásához ugyanaz, mint a hagyományos dedikált virtuális gépekhez, de az Azure többletkapacitását használják, és nagy kedvezménnyel érhetők el.  Alacsony prioritású virtuális gépek használatával ára azonban, hogy azokon a virtuális gépeken nem érhető el, amelyet ki kell, vagy bármikor rendelkezésre álló kapacitás függően előfordulhat, hogy háttérbe. Emiatt alacsony prioritású virtuális gépek nem fogja lehet megfelelő, az összes renderelési feladatokat. Például ha a lemezképek jelennek meg, akkor valószínűleg több órát is igénybe, amely megszakad, és a miatt folyamatban van a leállított virtuális gépek újraindítása képek renderelése kellene hát nem lenne elfogadható.

További információ az alacsony prioritású virtuális gépek és a különféle módon konfigurálhatja őket a Batch használatával, lásd: [alacsony prioritású virtuális gépek használata a Batch-Csel](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Feladatok és tevékenységek

Nincs leképezés-specifikus támogatás nem szükséges feladatokat és tevékenységeket.  A fő konfigurációs elem a tevékenység parancssora, amelyekre szüksége van a szükséges alkalmazás hivatkozni.
Az Azure piactér-beli Virtuálisgép-lemezképeket használnak, majd az ajánlott eljárás esetén a környezeti változók használatával adja meg az elérési út és az alkalmazás végrehajtható fájlját.

## <a name="next-steps"></a>További lépések

A példák a Batch rendering próbálja ki a két oktatóanyagok:

* [Renderelés az Azure CLI használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderelés, a Batch Explorer használata](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
