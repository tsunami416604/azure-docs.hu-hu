---
title: Renderelési lehetőségek - Azure Batch
description: A szabványos Azure Batch-képességek a renderelési számítási feladatok és alkalmazások futtatására szolgálnak. A Batch speciális funkciókat tartalmaz a renderelési számítási feladatok támogatásához.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449722"
---
# <a name="azure-batch-rendering-capabilities"></a>Az Azure Batch leképezési képességei

A szabványos Azure Batch-képességek a renderelési számítási feladatok és alkalmazások futtatásához használatosak. A Batch a renderelési számítási feladatok támogatásához speciális funkciókat is tartalmaz.

A Kötegfogalmak áttekintését, beleértve a készleteket, a feladatokat és a feladatokat, olvassa el [ezt a cikket.](https://docs.microsoft.com/azure/batch/batch-api-basics)

## <a name="batch-pools"></a>Kötegkészletek

### <a name="rendering-application-installation"></a>Renderelési alkalmazás telepítése

Az Azure Marketplace-en renderelés virtuális gép lemezképe megadható a készlet konfigurációjában, ha csak az előre telepített alkalmazásokat kell használni.

Van egy Windows 2016-lemezkép és egy CentOS-lemezkép.  Az [Azure Piactéren](https://azuremarketplace.microsoft.com)a virtuális gép lemezképek találhatók a "batch renderelés" keres.

Például készlet konfiguráció, tekintse meg az [Azure CLI renderelés i. bemutató.](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)  Az Azure Portal és a Batch Explorer gui eszközöket biztosít a renderelési virtuálisgép-lemezkép kiválasztásához készlet létrehozásakor.  Batch API használata esetén adja meg a következő tulajdonságértékeket az [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) számára készlet létrehozásakor:

| Közzétevő | Ajánlat | SKU | Verzió |
|---------|---------|---------|--------|
| kötegelt | renderelés-centos73 | Renderelés | legújabb |
| kötegelt | renderelés-windows2016 | Renderelés | legújabb |

További lehetőségek akkor érhetők el, ha további alkalmazásokra van szükség a készlet virtuális gépein:

* Egyéni kép a Megosztott képtárból:
  * így pontosan azokkal az alkalmazásokkal és verziókkal konfigurálhatja a virtuális gépet, amelyekre szüksége van. További információt a [Készlet létrehozása a Megosztott képtárral című témakörben talál.](batch-sig-images.md) Az Autodesk és a Chaos Group módosította az Arnoldot, illetve a V-Rayt, hogy érvényesítse az Azure Batch licencelési szolgáltatással szemben. Győződjön meg arról, hogy rendelkezik ezen alkalmazások verzióival ezzel a támogatással, különben a használatalapú licencelés nem fog működni. A Maya vagy a 3ds Max jelenlegi verziói nem igényelnek licenckiszolgálót fej nélküli futtatásesetén (kötegelt/parancssori módban). Lépjen kapcsolatba az Azure-támogatással, ha nem biztos abban, hogy miként kell eljárnia ezzel a beállítással.
* [Alkalmazáscsomagok:](https://docs.microsoft.com/azure/batch/batch-application-packages)
  * Csomagolja be az alkalmazásfájlokat egy vagy több ZIP-fájl használatával, töltse fel az Azure Portalon keresztül, és adja meg a csomagot a készlet konfigurációjában. A készletvirtuális gépek létrehozásakor a ZIP-fájlok letöltése és a kibontás.
* Erőforrásfájlok:
  * Az alkalmazásfájlok feltöltése az Azure blob storage-ba, és fájlhivatkozásokat a [készlet indítási feladat.](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) Készlet virtuális gépek létrehozásakor az erőforrásfájlok minden virtuális gépre letöltődnek.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Használatalapú licencelés előre telepített alkalmazásokhoz

A használni kívánt alkalmazásokat, és a licencelési díjat meg kell adni a készlet konfigurációjában.

* Adja `applicationLicenses` meg a [tulajdonságot készlet létrehozásakor](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  A következő értékeket lehet megadni a tömb karakterláncok - "vray", "arnold", "3dsmax", "maya".
* Ha egy vagy több alkalmazást ad meg, akkor ezeknek az alkalmazásoknak a költsége hozzáadódik a virtuális gépek költségéhez.  Az alkalmazásárak az [Azure Batch díjszabási oldalán](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)jelennek meg.

> [!NOTE]
> Ha ehelyett licenckiszolgálóhoz csatlakozik a renderelési `applicationLicenses` alkalmazások használatához, ne adja meg a tulajdonságot.

Az Azure Portalon vagy a Batch Explorerben kiválaszthatja az alkalmazásokat, és megjelenítheti az alkalmazás árakat.

Ha megkísérli egy alkalmazás használatát, de az alkalmazás nincs `applicationLicenses` megadva a készletkonfiguráció tulajdonságában, vagy nem éri el a licenckiszolgálót, akkor az alkalmazás végrehajtása licencelési hibával és nem nulla kilépési kóddal sikertelen.

### <a name="environment-variables-for-pre-installed-applications"></a>Környezeti változók az előre telepített alkalmazásokhoz

A renderelési feladatok parancssorának létrehozásához meg kell adni a renderelési alkalmazás végrehajtható fájljainak telepítési helyét.  Rendszerkörnyezeti változók jöttek létre az Azure Marketplace virtuális gép rendszerképek, amely a tényleges elérési utak megadása helyett használható.  Ezek a környezeti változók az egyes feladatokhoz létrehozott [szabványos Batch környezeti változókon](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) kívül találhatók.

|Alkalmazás|Alkalmazás végrehajtható fájlja|Környezeti változó|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Káosz Csoport V-Ray Önálló|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 parancssor|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 parancssor|kick.exe|ARNOLD_2018_EXEC|
|Turmixgép|turmixgép.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure virtuálisgép-családok

Más számítási feladatokhoz is az alkalmazásrendszer követelményei eltérőek, a teljesítménykövetelmények pedig eltérőek a feladatok és a projektek esetében.  A virtuálisgép-családok széles választéka érhető el az Azure-ban az Ön igényeitől függően – a legalacsonyabb költség, a legjobb ár/teljesítmény, a legjobb teljesítmény és így tovább.
Egyes renderelési alkalmazások, például az Arnold, CPU-alapúak; más, például a V-Ray és a Blender Cycles processzorokat és/vagy GPU-kat használhat.
Az elérhető virtuálisgép-családok és virtuálisgép-méretek leírását [a Virtuálisgép-típusok és -méretek ismerteti.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)

### <a name="low-priority-vms"></a>Alacsony prioritású virtuális gépek

Más számítási feladatokhoz is csak úgy, alacsony prioritású virtuális gépek is használhatók a kötegkészletekben a rendereléshez.  Az alacsony prioritású virtuális gépek ugyanazt a teljesítményt hajtják végre, mint a normál dedikált virtuális gépek, de kihasználja a felesleges Azure-kapacitást, és nagy kedvezményesetén érhetők el.  Az alacsony prioritású virtuális gépek használatának kompromisszumos használata az, hogy ezek a virtuális gépek nem lehet lefoglalni, vagy lehet, hogy a rendelkezésre álló kapacitás. Emiatt az alacsony prioritású virtuális gépek nem lesznek alkalmasak minden renderelési feladathoz. Például ha a képek megjelenítése több órát vesz igénybe, akkor valószínű, hogy a képek renderelése megszakadt és újraindult a virtuális gépek előzetes használata miatt nem lenne elfogadható.

Az alacsony prioritású virtuális gépek jellemzőiről és a Batch használatával történő konfigurálásuk különböző módjairól az [Alacsony prioritású virtuális gépek használata a Batch használatával](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)című témakörben talál további információt.

## <a name="jobs-and-tasks"></a>Feladatok és feladatok

A feladatokhoz és feladatokhoz nincs szükség renderelésspecifikus támogatásra.  A fő konfigurációs elem a feladat parancssora, amelynek hivatkoznia kell a szükséges alkalmazásra.
Az Azure Marketplace virtuálisgép-lemezképek használata kor, majd az ajánlott eljárás a környezeti változók használatával adja meg az elérési utat és az alkalmazás végrehajtható.

## <a name="next-steps"></a>További lépések

A Batch renderelés példáit próbálja ki a két oktatóanyag:

* [Renderelés az Azure CLI használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Renderelés a Batch Explorer használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
