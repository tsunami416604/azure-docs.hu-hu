---
title: Renderelés – Azure Batch
description: Bevezetés az Azure rendereléshez való használatának bevezetésére és az Azure Batch renderelési képességeinek áttekintése
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60774029"
---
# <a name="rendering-using-azure"></a>Renderelés az Azure használatával

A renderelés a 3D modellek bevételének és 2D-s képekké alakításának folyamata. A 3D-s jelenetfájlok olyan alkalmazásokban készülnek, mint az Autodesk 3ds Max, az Autodesk Maya és a Blender.  A renderelési alkalmazások, például az Autodesk Maya, az Autodesk Arnold, a Chaos Group V-Ray és a Blender Cycles 2D-s képeket hoznak létre.  Néha egyetlen kép jön létre a jelenet fájlokat. Azonban gyakori, hogy több képet modelleznek és renderelnek, majd animációban kombinálják őket.

A renderelési munkaterhelés terősen speciális effektusok (VFX) számára használják a média- és szórakoztatóiparban. A renderelés számos más iparágban is használatos, például a reklámokban, a kereskedelemben, az olaj- és gáziparban és a gépiparban.

A renderelés folyamata számításigényes; nem lehet sok kép / kép, hogy készítsen, és minden kép is eltarthat több órát, hogy.  A renderelés ezért tökéletes kötegelt feldolgozási számítási feladat, amely az Azure és az Azure Batch segítségével számos renderet futtathat párhuzamosan.

## <a name="why-use-azure-for-rendering"></a>Miért érdemes az Azure-t használni a rendereléshez?

Több okból is a renderelés az Azure és az Azure Batch számára tökéletesen megfelelő számítási feladat:

* A renderelési feladatok több darabra oszthatók, amelyek több virtuális gép használatával párhuzamosan futtathatók:
  * Az animációk sok képkockából állnak, és minden képkocka párhuzamosan renderelhető.  Minél több virtuális gép áll rendelkezésre az egyes képkockák feldolgozásához, annál gyorsabban előállítható az összes képkocka és az animáció.
  * Egyes renderelő szoftverek lehetővé teszik, hogy egyetlen képkocka több darabra, például mozaikra vagy szeletre legyen felosztva.  Minden darab külön renderelhető, majd a végső képbe kombinálható, amikor az összes darab elkészült.  Minél több virtuális gép érhető el, annál gyorsabban renderelhető egy keret.
* A renderelési projektek hatalmas méreteket vehetnek igénybe:
  * Az egyes keretek összetettek lehetnek, és sok órát igényelnek a rendereléshez, még a csúcskategóriás hardvereken is; animációk több százezer képkockából állhatnak.  A kiváló minőségű animációk ésszerű időn át való megjelenítéséhez hatalmas mennyiségű számításra van szükség.  Egyes esetekben több mint 100 000 magot használtak több ezer képkocka párhuzamos megjelenítésére.
* A renderelési projektek projektalapúak, és különböző számítási mennyiségeket igényelnek:
  * Szükség esetén foglalja le a számítási és tárolási kapacitást, méretezse fel- vagy le a projekt során betöltésszerint, és távolítsa el, amikor a projekt befejeződött.
  * A kapacitás kifizetése lefoglaláskor, de nem fizet érte, ha nincs terhelés, például a projektek között.
  * A váratlan változások miatti sorozatok kielégítése; magasabb skálán feljebb, ha a projekt késői elkésése váratlan változásokat hajt végre, és ezeket a módosításokat szoros ütemezés szerint kell feldolgozni.
* Válasszon a hardverek széles választékából az alkalmazás, a munkaterhelés és az időkeret szerint:
  * Az Azure-ban elérhető hardverek széles választéka áll rendelkezésre, amelyek a Batch-tel foglalhatók le és kezelhetők.
  * A projekttől függően a követelmény lehet a legjobb ár/teljesítmény vagy a legjobb általános teljesítmény.  A különböző jelenetek és/vagy renderelési alkalmazások eltérő memóriaigényűek lesznek.  Egyes renderelési alkalmazások a GPU-kat a legjobb teljesítmény vagy bizonyos funkciók érdekében használhatják. 
* Az alacsony prioritású virtuális gépek csökkentik a költségeket:
  * Alacsony prioritású virtuális gépek állnak rendelkezésre a rendszeres igény szerinti virtuális gépekhez képest nagy kedvezményt, és alkalmasak bizonyos feladattípusok.
  * Alacsony prioritású virtuális gépek et lehet kiosztani az Azure Batch, a Batch rugalmasságot biztosít, hogyan használják a követelmények széles körének kielégítésére.  Batch készletek állhat dedikált és alacsony prioritású virtuális gépek, azzal, hogy bármikor módosíthatja a virtuálisgép-típusok keveréke.

## <a name="options-for-rendering-on-azure"></a>Az Azure-beli megjelenítés lehetőségei

Számos Azure-képességek, amelyek a számítási feladatok renderelése használható.  A használandó képességek a meglévő környezettől és követelményektől függenek.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Meglévő helyszíni renderelési környezet rendereléskezelő alkalmazás használatával

A leggyakoribb eset az, hogy van egy meglévő helyszíni render farm által kezelt render kezelő alkalmazás, mint a PipelineFX Qube, Royal Render, vagy Thinkbox határidő.  A követelmény az Azure-beli virtuális gépek használatával a helyszíni renderelési farm kapacitásának kiterjesztése.

A rendereléskezelő szoftver beépített Azure-támogatással rendelkezik, vagy elérhetővé tesszük az Azure-támogatást hozzáadó beépülő modulokat. A támogatott renderkezelőkről és funkciókról további információt a [renderkezelők használatáról](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)szóló cikktartalmaz.

### <a name="custom-rendering-workflow"></a>Egyéni leképezési munkafolyamat

A követelmény az, hogy a virtuális gépek egy meglévő renderelési farmot bővítsenek ki.  Az Azure Batch-készletek nagy számú virtuális gépet foglalhatnak le, lehetővé teszik az alacsony prioritású virtuális gépek használatát és dinamikus automatikus skálázását a teljes árú virtuális gépek segítségével, és a népszerű renderelési alkalmazások számára díjfizetéses licencelést biztosítanak.

### <a name="no-existing-render-farm"></a>Nincs meglévő renderfarm

Lehet, hogy az ügyfélmunkaállomások végzik renderelést, de a renderelési munkaterhelés növekszik, és túl sokáig tart a munkaállomás-kapacitás kizárólagos használata.  Az Azure Batch egyaránt használható renderelési farm számítási igény szerinti lefoglalására, valamint a renderelési feladatok ütemezésére az Azure renderelési farmra.

## <a name="azure-batch-rendering-capabilities"></a>Az Azure Batch leképezési képességei

Az Azure Batch lehetővé teszi a párhuzamos számítási feladatok futtatását az Azure-ban.  Lehetővé teszi a létrehozása és kezelése nagy számú virtuális gépek, amelyeken alkalmazások vannak telepítve és futtatva.  Emellett átfogó feladatütemezési lehetőségeket biztosít az alkalmazások példányainak futtatásához, a feladatok virtuális gépekhez való hozzárendelését, a sorban állást, az alkalmazásfigyelést és így tovább.

Az Azure Batch számos számítási feladathoz használható, de a következő képességek érhetők el, hogy megkönnyítse és gyorsabban futtassa a renderelési számítási feladatokat.

* Virtuálisgép-lemezképek előre telepített grafikus és renderelő alkalmazásokkal:
  * Az Azure Marketplace virtuálisgép-lemezképek állnak rendelkezésre, amelyek népszerű grafikus és renderelési alkalmazásokat tartalmaznak, így nem kell saját maga telepíteniaz alkalmazásokat, vagy saját egyéni lemezképeket létrehozni a telepített alkalmazásokkal. 
* Használatalapú licencelés renderelési alkalmazásokhoz:
  * Dönthet úgy, hogy az alkalmazások ért a perc, amellett, hogy fizet a számítási virtuális gépek, amely elkerüli, hogy vásárolni licenceket, és potenciálisan konfigurálja a licenckiszolgáló az alkalmazások.  A használat kifizetése azt is jelenti, hogy lehetőség van a változó és váratlan terheléskielégítésére, mivel nincs meghatározott számú licenc.
  * Lehetőség van az előre telepített alkalmazások saját licenccel való használatára is, és nem használhatja a használatalapú licencelést. Ehhez általában telepíteni e helyszíni vagy Azure-alapú licenckiszolgáló, és egy Azure virtuális hálózat segítségével csatlakoztassa a renderelési készlet a licenckiszolgálóhoz.
* Beépülő modulok az ügyfelek tervezéséhez és modellezéséhez:
  * A beépülő modulok lehetővé teszik a végfelhasználók számára, hogy közvetlenül az ügyfélalkalmazásból, például az Autodesk Maya-ból használják az Azure Batchet, lehetővé téve számukra, hogy készleteket hozzanak létre, feladatokat küldjenek el, és több számítási kapacitást használjanak a gyorsabb renderelések végrehajtásához.
* Rendereléskezelő integrációja:
  * Az Azure Batch integrálva van a renderelési felügyeleti alkalmazásokba, vagy beépülő modulok állnak rendelkezésre az Azure Batch-integráció biztosításához.

Az Azure Batch számos módon használható, amelyek mindegyike az Azure Batch-renderelésre is vonatkozik.

* API-k:
  * Kódot írhat a [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET,](https://docs.microsoft.com/dotnet/api/overview/azure/batch) [Python,](https://docs.microsoft.com/python/api/overview/azure/batch) [Java](https://docs.microsoft.com/java/api/overview/azure/batch)vagy más támogatott API-k használatával.  A fejlesztők integrálhatják az Azure Batch-képességeket meglévő alkalmazásaikba vagy munkafolyamataikba, akár felhőben, akár helyszíni környezetben.  Az [Autodesk Maya beépülő modul](https://github.com/Azure/azure-batch-maya) például a Batch Python API-t használja a Batch meghívásához, a készletek létrehozásához és kezeléséhez, a feladatok és feladatok elküldéséhez, valamint az állapot figyeléséhez.
* Parancssori eszközök:
  * Az [Azure parancssori vagy](https://docs.microsoft.com/cli/azure/) Azure [PowerShell](https://docs.microsoft.com/powershell/azure/overview) parancsfájl batch használatra használható.
  * A Batch CLI sablon támogatása különösen megkönnyíti a gyűjtők létrehozását és a feladatok elküldését.
* Felülete:
  * [A Batch Explorer](https://github.com/Azure/BatchExplorer) egy platformfüggetlen ügyféleszköz, amely lehetővé teszi a Batch-fiókok kezelését és figyelését is, de az Azure Portal felhasználói felületéhez képest néhány gazdagabb képességet biztosít.  Készlet- és feladatsablonok készlete, amelyek minden támogatott alkalmazáshoz vannak szabva, és segítségével egyszerűen létrehozhatkészleteket és küldhet feladatokat.
  * Az Azure Portal azure-köteg kezelésére és figyelésére használható.
* Az ügyfélalkalmazás beépülő moduljai:
  * Beépülő modulok állnak rendelkezésre, amelyek lehetővé teszik a Batch renderelés takarásos használható közvetlenül az ügyfél tervezési és modellezési alkalmazások. A beépülő modulok főként a Batch Explorer alkalmazást hívja meg az aktuális 3D modell kontextuális adataival.
  * A következő beépülő modulok állnak rendelkezésre:
    * [Azure Batch maya számára](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Turmixgép](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Az Azure Batch-renderelés első lépései

Az Azure Batch-renderelés kipróbálásához tekintse meg az alábbi bevezető oktatóanyagokat:

* [Blender-jelenet renderelése a Batch Explorer segítségével](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [A Batch CLI segítségével jelenítsen meg egy Autodesk 3ds Max jelenetet](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>További lépések

Határozza meg a [jelen cikkben](https://docs.microsoft.com/azure/batch/batch-rendering-applications)az Azure Marketplace virtuálisgép-lemezképekben szereplő renderelési alkalmazások és verziók listáját.