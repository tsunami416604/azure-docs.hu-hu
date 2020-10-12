---
title: Renderelés áttekintése
description: Az Azure használatának bemutatása és a Azure Batch renderelési képességeinek áttekintése
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 9fac5d3efabc5d9f796c91d688f35e01aeefdca3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092762"
---
# <a name="rendering-using-azure"></a>Renderelés az Azure használatával

A renderelés a 3D modellek készítésének és a 2D-képekbe való átalakításának folyamata. a 3D-jelenet fájljai olyan alkalmazásokban vannak létrehozva, mint az Autodesk 3ds Max, a Autodesk Maya és a Blender.  Az olyan alkalmazások renderelése, mint a Autodesk Maya, a Autodesk Arnold, a Chaos Group V-Ray és a Blender ciklusok 2D-képeket készítenek.  Néha egyetlen lemezkép jön létre a jelenet fájljaiból. Azonban gyakori a több rendszerkép modellezése és renderelése, majd egy animációban való kombinálása.

A renderelési munkaterhelést nagy mértékben használják a média és a szórakoztató ipar különleges hatásaira (VFX). A renderelés számos más iparágban is használatos, például a reklámokban, a kereskedelemben, az olaj- és gáziparban és a gépiparban.

A renderelés folyamata számítási igényű; számos képkocka vagy rendszerkép hozható létre, és az egyes képek renderelése több órát is igénybe vehet.  A renderelés ezért egy tökéletes batch-feldolgozási feladat, amely képes az Azure-t és a Azure Batcht egyszerre több renderelés futtatására használni.

## <a name="why-use-azure-for-rendering"></a>Miért érdemes az Azure-t renderelésre használni?

Számos ok miatt a renderelés az Azure-hoz és a Azure Batchhoz tökéletesen illeszkedő munkaterhelés:

* A renderelési feladatok számos darabra bonthatók, amelyek több virtuális gép használatával párhuzamosan is futtathatók:
  * Az animációk számos képkockát tartalmaznak, és az egyes keretek párhuzamosan is megjeleníthető.  Minél több virtuális gép áll rendelkezésre az egyes keretek feldolgozásához, annál gyorsabban hozhatók létre az összes keret és az animáció.
  * Egyes renderelési szoftverek lehetővé teszik, hogy az egyes keretek több darabra legyenek bontva, például csempék vagy szeletek számára.  Mindegyik darab külön jeleníthető meg, majd a végső képhez kombinálva, ha az összes darab elkészült.  Minél több virtuális gép érhető el, annál gyorsabban lehet megjeleníteni a keretet.
* A renderelési projektek nagy léptéket igényelhetnek:
  * Az egyes keretek összetettek lehetnek, és több órát is igénybe vehetnek, még a nagy teljesítményű hardveren is. az animációk több száz ezer képkockából állhatnak.  Nagy mennyiségű számításra van szükség a kiváló minőségű animációk ésszerű időtartamú megjelenítéséhez.  Néhány esetben több mint 100 000 magot használtak párhuzamosan több ezer keret megjelenítéséhez.
* A renderelési projektek projekt-alapúak, és különböző mennyiségű számítást igényelnek:
  * Szükség esetén kioszthatja a számítási és a tárolási kapacitást, felskálázást végez a projekt során betöltésnek megfelelően, és eltávolíthatja a projekt befejezésekor.
  * A kapacitás kiosztása után kell fizetnie, de nem kell fizetnie, ha nincs betöltés, például a projektek között.
  * A váratlan változások miatt gondoskodik a kitörésekről; nagyobb skálázás, ha a projektben váratlan változások történnek, és ezeket a módosításokat szűk időben kell feldolgozni.
* Az alkalmazások, a számítási feladatok és az időkeretek alapján széles választékban választhat:
  * Az Azure-ban széles körben elérhetők a rendelkezésre álló hardverek, amelyeket a Batch szolgáltatással lehet kiosztani és felügyelni.
  * A projekttől függően előfordulhat, hogy a követelmény a legjobb ár/teljesítmény vagy a legjobb általános teljesítmény.  A különböző jelenetek és/vagy megjelenítési alkalmazások eltérő memóriabeli követelményekkel rendelkeznek.  Egyes renderelési alkalmazások a legjobb teljesítményhez vagy bizonyos funkciókhoz használhatják a GPU-ket. 
* Az alacsony prioritású virtuális gépek csökkentik a költségeket:
  * Az alacsony prioritású virtuális gépek a normál igény szerinti virtuális gépekhez képest nagy kedvezményekhez érhetők el, és bizonyos feladatokhoz megfelelőek.
  * Az alacsony prioritású virtuális gépeket Azure Batch kioszthatja, a Batch pedig rugalmasságot biztosít, hogy a követelmények széles körének ellátása milyen módon történik.  A Batch-készletek tartalmazhatnak dedikált és alacsony prioritású virtuális gépeket is, így bármikor módosíthatók a virtuálisgép-típusok kombinációja.

## <a name="options-for-rendering-on-azure"></a>Az Azure-on való renderelés lehetőségei

A számítási feladatok megjelenítéséhez számos Azure-funkció használható.  A használni kívánt képességek a meglévő környezettől és követelményektől függenek.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Meglévő helyszíni megjelenítési környezet renderelési felügyeleti alkalmazás használatával

A leggyakoribb eset az, hogy egy meglévő helyszíni Render Farm felügyelhető egy renderelési felügyeleti alkalmazással, például a PipelineFX Qube, a Royal Render vagy a Thinkbox határidővel.  A követelmény a helyszíni rendering Farm kapacitásának kiterjesztése Azure-beli virtuális gépek használatával.

A renderelési felügyeleti szoftver beépített Azure-támogatással rendelkezik, vagy elérhetővé tettük az Azure-támogatást hozzáadó beépülő modulokat. A támogatott Render managerekkel és funkciókkal kapcsolatos további információkért tekintse meg a [Render Manager használatával](./batch-rendering-render-managers.md)foglalkozó cikket.

### <a name="custom-rendering-workflow"></a>Egyéni renderelési munkafolyamat

A szükséges, hogy a virtuális gépek kiterjesszék a meglévő Render farmokat.  Azure Batch-készletek nagy mennyiségű virtuális gépet foglalhatnak le, lehetővé teszik az alacsony prioritású virtuális gépek használatát, valamint a teljes árú virtuális gépekkel való dinamikusan automatikus méretezést, valamint a népszerű renderelési alkalmazások számára a használaton kívüli licencelést.

### <a name="no-existing-render-farm"></a>Nincs meglévő Render Farm

Előfordulhat, hogy az ügyfél-munkaállomások renderelést végeznek, de a renderelési feladat egyre nő, és a munkaállomás kapacitása túl hosszú.  A Azure Batch a render Farm számítási igény szerinti kiosztásához, valamint a renderelési feladatok Azure Render farmhoz való ütemezéséhez is használható.

## <a name="azure-batch-rendering-capabilities"></a>Renderelési képességek Azure Batch

Azure Batch lehetővé teszi a párhuzamos számítási feladatok futtatását az Azure-ban.  Lehetővé teszi nagy számú virtuális gép létrehozását és felügyeletét, amelyen az alkalmazások telepítve vannak és futnak.  Emellett átfogó feladatütemezés-képességeket is biztosít az alkalmazások példányainak futtatásához, így biztosítva a tevékenységek hozzárendelését a virtuális gépekhez, a sorba állításhoz, az alkalmazások figyeléséhez és így tovább.

Azure Batch számos számítási feladathoz használatos, de a következő lehetőségek állnak rendelkezésre, hogy könnyebb és gyorsabb legyen a renderelési feladatok futtatása.

* Virtuálisgép-lemezképek előre telepített grafikus és megjelenítési alkalmazásokkal:
  * Az Azure piactéren elérhető virtuálisgép-lemezképek népszerű grafikát és megjelenítési alkalmazásokat tartalmaznak, elkerülve az alkalmazások telepítését, vagy saját egyéni lemezképek létrehozását a telepített alkalmazásokkal. 
* Felhasználónkénti licencelés az alkalmazások megjelenítéséhez:
  * Kiválaszthatja, hogy az alkalmazásokra a számítási virtuális gépekért fizetett díj mellett az alkalmazásokért fizet, így nem kell licenceket vásárolnia, és konfigurálnia kell a licenckiszolgálót az alkalmazásokhoz.  A használat megfizetése azt is jelenti, hogy a különböző és váratlan terhelések is fennállnak, mivel nem rögzített számú licenc.
  * Az előre telepített alkalmazásokat is használhatja saját licencekkel, és nem használhatja a használaton kívüli licencelést. Ehhez általában egy helyszíni vagy Azure-alapú licenckiszolgálót kell telepítenie, és egy Azure-beli virtuális hálózattal kell összekapcsolni a renderelési készletet a licenckiszolgálóval.
* Beépülő modulok az ügyfél tervezési és modellezési alkalmazásaihoz:
  * A beépülő modulok lehetővé teszik, hogy a végfelhasználók a Azure Batch közvetlenül az ügyfélalkalmazás, például a Autodesk Maya használatával használják, így készleteket hozhatnak létre, feladatokat küldhetnek, és nagyobb számítási kapacitást használhatnak a gyorsabb renderelés érdekében.
* Render Manager-integráció:
  * A Azure Batch a renderelési felügyeleti alkalmazások vagy a beépülő modulok számára elérhetővé teszik a Azure Batch-integrációt.

A Azure Batch számos módon használható, amelyek mindegyike a Azure Batch renderelésre is vonatkozik.

* API-k:
  * Kód írása a [Rest](/rest/api/batchservice), a [.net](/dotnet/api/overview/azure/batch), a [Python](/python/api/overview/azure/batch), a [Java](/java/api/overview/azure/batch)vagy más támogatott API-k használatával.  A fejlesztők a meglévő alkalmazásokhoz vagy munkafolyamatokhoz, akár a felhőben, akár a helyszínen is integrálhatja Azure Batch képességeit.  A [Autodesk Maya beépülő modul](https://github.com/Azure/azure-batch-maya) például a Batch Python API-t használja a kötegek meghívásához, a készletek létrehozásához és kezeléséhez, a feladatok és a feladatok elküldéséhez, valamint a figyelési állapothoz.
* Parancssori eszközök:
  * Az [Azure parancssori](/cli/azure/) vagy [Azure PowerShell](/powershell/azure/) használható a Batch-használat parancsfájlhoz.
  * Különösen a Batch CLI-sablon támogatása sokkal egyszerűbbé teszi a készletek létrehozását és a feladatok elküldését.
* Felhasználóifelület
  * A [Batch Explorer](https://github.com/Azure/BatchExplorer) egy platformfüggetlen ügyfél-eszköz, amely lehetővé teszi a Batch-fiókok felügyeletét és figyelését is, de a Azure Portal felhasználói felületéhez képest gazdagabb képességeket biztosít.  A készlet és a feladatütemezés készlete minden támogatott alkalmazáshoz testreszabható, és felhasználható a készletek egyszerű létrehozására és a feladatok elküldésére.
  * A Azure Portal a Azure Batch felügyeletére és figyelésére használható.
* Ügyfélalkalmazás beépülő modul:
  * Olyan beépülő modulok érhetők el, amelyek lehetővé teszik, hogy a Batch rendering közvetlenül az ügyfél-tervezési és modellező alkalmazásokban legyen használatban. A beépülő modulok elsősorban a Batch Explorer alkalmazást hívja meg az aktuális 3D-modellel kapcsolatos környezetfüggő információkkal.
  * A következő beépülő modulok érhetők el:
    * [Maya Azure Batch](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Azure Batch renderelés – első lépések

Tekintse meg a következő bevezető oktatóanyagokat a Azure Batch megjelenítésének kipróbálásához:

* [A Batch Explorer használata Turmixgépi jelenet megjelenítéséhez](./tutorial-rendering-batchexplorer-blender.md)
* [Az Autodesk 3ds Max-jelenet megjelenítése a Batch CLI használatával](./tutorial-rendering-cli.md)

## <a name="next-steps"></a>További lépések

Határozza meg az Azure Marketplace virtuálisgép-lemezképekben található renderelési alkalmazások és verziók listáját [ebben a cikkben](./batch-rendering-applications.md).
