---
title: Áttekintés – Azure Batch Rendering
description: Az Azure renderelésre és az Azure Batch renderelési képességeket áttekintést bevezetése
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: bc8c96345aeb1886696326edd230666ac8b6c41d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542210"
---
# <a name="rendering-using-azure"></a>Renderelés az Azure használatával

Renderelési véve a 3D-modellek és átalakítás őket, 2D lemezképek. 3D jelenet fájlok készült alkalmazások például az Autodesk 3ds Max, az Autodesk Maya és a Blender.  Renderelési alkalmazások, például az Autodesk Maya, az Autodesk Arnold, a Chaos Group V-Ray és a Blender ciklusok 2D lemezképek előállításához.  Néha egyetlen lemezképek alapján a jelenetfájlok hoztak létre. Azonban szokás modellezheti és jelennek meg több lemezképet, és majd ötvözze őket egy animációt.

A renderelési számítási erősen használatos különleges hatások (VFX) médiában és iparág. Renderelési számos egyéb, például a hirdetések, kereskedelmi, olaj és gáz és gyártási industires is használatban van.

A folyamat leképezésének a nagy számítási igényű; számos keretek/rendszerkép létrehozására lehet, és minden egyes képe jelennek meg több órát is igénybe vehet.  Renderelés, amelyek kihasználhatják az Azure és az Azure Batch számos rendereket párhuzamosan futó tökéletes kötegelt feldolgozási számítási feladatok ezért.

## <a name="why-use-azure-for-rendering"></a>Miért érdemes használni az Azure renderelési?

Számos oka lehet renderelési tökéletesen alkalmas az Azure és az Azure Batch számítási feladatok esetén:

* Renderelési feladatok használatával több virtuális gép párhuzamosan futtatható hány darab osztható:
  * Minden egyes keret párhuzamosan jeleníthetők meg, és számos keretek animációk állnak.  A további virtuális gépek rendelkezésre folyamat minden keret, annál gyorsabban minden a keretek és az animáció elő lehet állítani.
  * Néhány renderelési szoftver lehetővé teszi, hogy több információt, például a csempék, illetve a szeletek felosztani a egyetlen keretek.  Minden egyes külön-külön jelenik meg, majd a végleges kép összesítve, ha minden egyes elemek végzett.  A további virtuális gépeket, amelyek is elérhetők, annál gyorsabban keret megjeleníthetők-e.
* Projektek megjelenítése hatalmas méretezési lehet szükség:
  * Egyes keretek összetettek lehetnek, és több órával renderelni, még a csúcskategóriás hardveren; megkövetelése több százezer keretek animációk állhat.  Egy ésszerű időn belül magas színvonalú animációkészítés hatalmas mennyiségű számítási van szükség.  Bizonyos esetekben több mint 100 000 magok használták több ezer párhuzamos keretek megjelenítése.
* Renderelési projektek project-alapú, ezért változó mennyiségű számítási:
  * Szükséges számítási és tárolási kapacitás foglalása, a projekt során terhelés alapján skálázza fel a felfelé és lefelé, és távolítsa el a projekt befejezésekor.
  * Amikor a kiosztott kapacitást kell fizetnie, de nem kell fizetnie, ha nincs terhelés, mint például között projektek.
  * Gondoskodjon arról, hogy miatt a váratlan módosítások; csúcsforgalomra méretezési csoport magasabb, ha vannak a váratlan módosítások késői a projektben, és ezeket a módosításokat kell feldolgozni szoros ütemezés szerint.
* Alkalmazások, számítási feladatok és időkeretre megfelelően hardver széles választékával közül választhat:
  * Nincs a Batch-Csel lefoglalva, és felügyelt Azure-ban elérhető hardverek széles választékával.
  * A követelmény a projektet, a legjobb ár-teljesítmény vagy a lehető legjobb általános teljesítményt is lehet.  Különböző jelenetek és/vagy renderelő alkalmazásokkal fog rendelkezni a különböző memóriára vonatkozó követelményeknek.  Néhány renderelési alkalmazások használhatják a GPU-k a legjobb teljesítmény elérése érdekében vagy bizonyos szolgáltatások. 
* Alacsony prioritású virtuális gépek a költségek csökkentése:
  * Alacsony prioritású virtuális gépek hagyományos, igény szerinti virtuális gépekhez képest nagy kedvezménnyel érhetők el, és néhány feladattípusok kiválóan alkalmasak.
  * Alacsony prioritású virtuális gépek által az Azure Batch, ami rugalmasságot biztosít a használatuk különböző követelmények kielégítése, a Batch-lehet kiosztani.  Batch-készletekben dedikált és alacsony prioritású virtuális gépeket, azt folyamatban lehet módosítani a virtuális gép különböző vegyesen bármikor állhat.

## <a name="options-for-rendering-on-azure"></a>Az Azure-ban renderelési beállításai

Nincsenek a renderelési számítási feladatokhoz felhasználható az Azure képességeit.  Mely funkciók használatához minden meglévő környezet és követelmények függ.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Meglévő helyszíni renderelési környezet renderelési felügyeleti alkalmazás használatával

A leggyakoribb eset van ott kell egy meglévő helyszíni jelennek meg farm például PipelineFX Qube, Royal Render vagy Thinkbox határidő egy renderelési projektmenedzsment-alkalmazás felügyeli.  A követelmény, kiterjesztése a helyszíni renderelési farm kapacitás Azure virtuális gépekkel.

A renderelési felügyeleti szoftver van-e az Azure-támogatás beépített vagy VÁLLALUNK az elérhető beépülő modulok hozzáadása az Azure-támogatás. További információ a támogatott jelennek meg a kezelők és a funkció engedélyezve van, lásd: a cikk a [használatával jelennek meg a kezelők](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Egyéni leképezés munkafolyamat

A követelmény, a virtuális gépek egy meglévő renderelési farm kiterjesztése.  Azure Batch-készletek foglal le a virtuális gépek nagy mennyiségű, alacsony prioritású virtuális gépek használják, és dinamikusan automatikusan méretezett virtuális gépek teljes árú engedélyezése, és adja meg a népszerű renderelési alkalmazások licencelése használatalapú esetében használható.

### <a name="no-existing-render-farm"></a>Nincs meglévő renderelési farm

Ügyfél-munkaállomásoknak blokkolásának megjelenítés esetén, de a renderelési számítási feladat elvégzésének, és kizárólag a munkaállomás-kapacitás használata túl sokáig tart.  Az Azure Batch renderelési farm számítási igény szerinti lefoglalni és is ütemezhet a renderelési feladatokat az Azure renderelési farm használható.

## <a name="azure-batch-rendering-capabilities"></a>Az Azure Batch renderelési képességeket

Az Azure Batch lehetővé teszi a párhuzamos számítási feladatok futtatásához az Azure-ban.  Lehetővé teszi a létrehozását és kezelését virtuális gépek, amelyen telepítve van és alkalmazások futtatása nagy számú.  Átfogó feladatütemezés ezeknek az alkalmazásoknak, a feladatok hozzárendelését biztosít a virtuális gépekhez, üzenetsor-kezelési, figyelési, alkalmazás-példány futtatása, és így tovább képességeket is biztosít.

Az Azure Batch számos számítási feladatokhoz használatos, de az alábbi képességek érhetők el kifejezetten legyen egyszerűbb és gyorsabb a renderelési számítási feladatok futtatásához.

* VM-rendszerképek előre telepített grafikus és megjelenítési alkalmazások:
  * Az Azure piactér Virtuálisgép-rendszerképek érhetők el, amely a népszerű grafikus és megjelenítési alkalmazások, az alkalmazások telepítése a saját maga, vagy hozzon létre saját egyéni rendszerképeit telepített alkalmazásokat nem kell tartalmaznia. 
* Használatalapú fizetés renderelési alkalmazások licencelése:
  * Ha szeretné, az alkalmazások percalapú, a compute-beli virtuális gépek elkerülhető gyorsítótárpéldányok kellene licencek vásárlása és potenciálisan konfigurálása az alkalmazásra vonatkozó licenckiszolgáló fizetési kötelezettség.  És használja is azt jelenti, hogy a különböző és váratlan terheléselosztást méretformátumok figyelembe vétele, nem rögzített számú licenccel nem lehetséges.
  * Akkor is, az előre telepített alkalmazásokkal a saját licenccel rendelkező és a használatalapú fizetés licencelés használják. Ehhez általában egy helyszíni vagy telepítése Azure-alapú kiszolgálói licenc és használja az Azure-beli virtuális hálózathoz való csatlakozáshoz a renderelési készlet a licenckiszolgáló.
* Beépülő modulok az ügyfél tervezési és modellező alkalmazásokban:
  * Beépülő modulok engedélyezése vehető igénybe az Azure Batch ügyfélalkalmazás közvetlenül a végfelhasználók számára, például Autodesk Maya, így hozhat létre készletek, feladatok elküldéséhez, és győződjön meg arról, felhasználásának további számítási kapacitás gyorsabb rendereket végrehajtásához.
* A leképezés manager integrációs:
  * Az Azure Batch renderelési alkalmazások integrálva van, vagy a beépülő modulok érhető el, az Azure Batch-integráció.

Többféleképpen is az Azure Batch, amelyek mindegyike is vonatkoznak az Azure Batch rendering.

* API-kat:
  * Használatával kód írása a [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch), vagy más API-kat támogatja.  A fejlesztők beépíthetik az Azure Batch-funkciók a meglévő alkalmazások vagy a munkafolyamat, hogy felhőbeli vagy helyszíni alapján.  Ha például a [Autodesk Maya beépülő modul](https://github.com/Azure/azure-batch-maya) már használja a Batch Python API meghívásához a Batch, létrehozását és a készletek kezelését, feladatokat és tevékenységeket küldjön és állapotának figyelése.
* Parancssori eszközei:
  * A [Azure parancssori felületével](https://docs.microsoft.com/cli/azure/) vagy [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview) használható parancsfájl a Batch használatát.
  * Ilyen például a Batch parancssori felületi sablon támogatása nagy mértékben megkönnyíti készletek létrehozásához és feladatok elküldéséhez.
* Előkészíthetik:
  * [A Batch Explorer](https://github.com/Azure/BatchExplorer) , kezelhető és figyelhető a Batch-fiókok lehetővé teszi többplatformos ügyféleszköz, de néhány gazdagabb képességekkel képest az Azure portal felhasználói Felületet biztosít.  Készlet és -feladat sablonok feltéve, hogy az egyes támogatott alkalmazások vannak igazítva és segítségével könnyedén hozhat létre a készletek és feladatok elküldéséhez.
  * Az Azure portal segítségével kezelhetők és figyelhetők Azure Batch.
* Ügyféloldali alkalmazás beépülő modul:
  * Beépülő modulok érhetők el, amelyek engedélyezik a Batch rendering közvetlenül az ügyfél szakterület való használatra, és modellező alkalmazásokban. A beépülő modulok főként hívja meg a Batch Explorer alkalmazás a jelenlegi 3D modell környezeti információkat.
  * A következő modulok érhetők el:
    * [A Maya alkalmazáshoz készült Azure Batch](https://github.com/Azure/azure-batch-maya)
    * [3DS Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [A Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Ismerkedés az Azure Batch rendering

Tekintse meg az alábbi bevezető oktatóanyagok az Azure Batch rendering kipróbálása:

* [Jelenetek Blender a Batch Explorer használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Az Autodesk 3ds Max-jelenet renderelni a Batch CLI használatával](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>További lépések

Renderelési alkalmazások és verziók tartalmaznak az Azure piactér Virtuálisgép-lemezképek használata a listájának meghatározása [Ez a cikk](https://docs.microsoft.com/azure/batch/batch-rendering-applications).