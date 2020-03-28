---
title: DoD Impact Level 4 tervminta
description: A DoD Impact Level 4 tervminta lépéseit, beleértve a tervezet összetevő paraméter ének részleteit.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: c92e24327bc1879b8fea89cf85a50f845e473810
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415308"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>A DoD 4-es hatásszintjének tervminta telepítése

Az Azure Blueprints Department of Defense Impact Level 4 (DoD IL4) tervminta üzembe helyezéséhez a következő lépéseket kell tenni:

> [!div class="checklist"]
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg a **DoD Impact Level 4** tervezetminta alatt _Egyéb minták_ és válassza használja ezt **a mintát**.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve:** Adja meg a dod impact level 4 tervminta példányának nevét.
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Sok összetevő nek vannak paraméterei, amelyeket később definiálunk. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezetminta példánya testreszabható a környezetés az igények igényei szerint, de a módosítás áthelyezheti azt a DoD Impact Level 4 vezérlőivel való igazítástól.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új lapon adja meg a tervezetminta egy **verzióját.** Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **meg módosítási megjegyzéseket,** például "A DoD IL4 tervezetmintából közzétett első verzió". Ezután válassza a **Közzététel** lehetőséget a lap alján.

## <a name="assign-the-sample-copy"></a>A mintapéldány hozzárendelése

Miután a tervezetminta példánya sikeresen **közzélett,,** hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve mentették. Ez a lépés az, ahol paraméterek et biztosítanak, hogy a tervezet minta másolatának minden egyes központi telepítése egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza a **Tervrajzok lehetőséget.**

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza **a Blueprint hozzárendelése** lehetőséget a tervezetdefiníciós lap tetején.

1. Adja meg a tervezet hozzárendelésének paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések:** Válasszon ki egy vagy több előfizetést, amely abban a felügyeleti csoportban van, amelybe a tervezetminta másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez egy-egy hozzárendelés jön létre a megadott paraméterek használatával.
     - **Hozzárendelés neve**: A név előre ki van töltve a tervrajz neve alapján.
       Szükség szerint változtassa meg, vagy hagyja úgy, ahogy van.
     - **Hely**: Válassza ki azt a régiót, amelyben a felügyelt identitást létre szeretné hozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Blueprint-definíciós verzió:** Válasszon egy **közzétett** változata a tervezet minta példánya.

   - Hozzárendelés zárolása

     Válassza ki a környezetéhez való tervezetzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszer hez rendelt_ felügyelt identitás beállítást.

   - Műtermék-paraméterek

     Az ebben a szakaszban meghatározott paraméterek a műtermékre vonatkoznak, amely alatt definiálva van. Ezek a paraméterek [dinamikus paraméterek,](../../concepts/parameters.md#dynamic-parameters) mivel a terv hozzárendelése során vannak definiálva. A teljes lista- vagy műtermék-paramétereket és azok leírását a [Műtermék paraméterek táblázatban láthatja.](#artifact-parameters-table)

1. Miután az összes paramétert megadta, válassza a **Hozzárendelés** lehetőséget a lap alján. A tervrajz-hozzárendelés jön létre, és a műtermék üzembe helyezése kezdődik. A telepítés nagyjából egy órát vesz igénybe. A telepítés állapotának ellenőrzéséhez nyissa meg a tervhozzárendelést.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervezetminták **ingyenesek.** Az Azure-erőforrások [ára termékenként.](https://azure.microsoft.com/pricing/) A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segítségével becsülje meg a tervezet minta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Műtermék-paraméterek táblája

Az alábbi táblázat a tervezet összetevő paramétereinek listáját tartalmazza:

|Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A Log Analytics-munkaterület-azonosító, amelyhez a virtuális gépeket konfigurálni kell|Ez annak a Log Analytics-munkaterületnek az azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Azon erőforrástípusok listája, amelyeknek engedélyeznie kell a diagnosztikai naplókat|Naplózandó erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Az elfogadható értékek az [Azure Monitor diagnosztikai naplóinak sémájában](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Azon felhasználók listája, akiket ki kell zárni a Windows virtuálisgép-rendszergazdák csoportból|A tagok pontosvesszővel elválasztott listája, amelyet ki kell zárni a Rendszergazdák helyi csoportból. Pl.: rendszergazda; myUser1; sajatuser2|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A Windows Virtuálisgép-rendszergazdák csoportba bevonandó felhasználók listája|A tagok pontosvesszővel elválasztott listája, amelyet a Rendszergazdák helyi csoportba kell foglalni. Pl.: rendszergazda; myUser1; sajatuser2|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Log Analytics-munkaterület Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics Közreműködő" engedélyeket (vagy hasonlót) a házirend-hozzárendelés elsődleges azonosítójára.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuálisgép-méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert|Üres tömb használható választható paraméterek jelzésére:\[\]|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez|Szabályzat-hozzárendelés|Log Analytics munkaterület Linuxos virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics Közreműködő" engedélyeket (vagy hasonlót) a házirend-hozzárendelés elsődleges azonosítójára.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert|Üres tömb használható választható paraméterek jelzésére:\[\]|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows VM méretezési csoportokhoz (VMSS)|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics Közreműködő" engedélyeket (vagy hasonlót) a házirend-hozzárendelés elsődleges azonosítójára.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows vm méretezési csoportokhoz (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Windows operációs rendszert|Üres tömb használható választható paraméterek jelzésére:\[\]|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows virtuális gépekhez|Ha ez a munkaterület kívül esik a hozzárendelés hatókörén, manuálisan kell megadnia a "Log Analytics Közreműködő" engedélyeket (vagy hasonlót) a házirend-hozzárendelés elsődleges azonosítójára.|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez|Szabályzat-hozzárendelés|Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Windows operációs rendszert|Üres tömb használható választható paraméterek jelzésére:\[\]|
|Speciális veszélyforrások elleni védelem telepítése a tárfiókokon|Szabályzat-hozzárendelés|Hatás|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|Naplózás telepítése SQL-kiszolgálókon|Szabályzat-hozzárendelés|A megőrzési időszak napjaiban lévő érték (a 0 korlátlan megőrzést jelez)|Megőrzési napok (nem kötelező, 180 nap, ha nincs megadva)|
|Naplózás telepítése SQL-kiszolgálókon|Szabályzat-hozzárendelés|Az SQL-kiszolgáló naplózásához való tárfiók erőforráscsoport-neve|Naplózási írja az adatbázis-eseményeket egy naplót az Azure Storage-fiók (a tárfiók jön létre minden régióban, ahol egy SQL Server jön létre, amely megosztja az összes kiszolgáló az adott régióban). Fontos - a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a tárfiókokat.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|Tárfiók előtagja a hálózati biztonsági csoport diagnosztikájához|Ez az előtag a hálózati biztonsági csoport helyével kombinálva alkotja a létrehozott tárfiók nevét.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|A hálózati biztonságicsoport diagnosztikájához a tárfiók erőforráscsoportának neve (léteznie kell)|Az erőforráscsoport, amelyben a tárfiók létre jön. Ennek az erőforráscsoportnak már léteznie kell.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Erőforrások és erőforráscsoportok engedélyezett helyei|Az Azure-helyek listája, amelyet a szervezet az erőforrások üzembe helyezésekor megadhat. Ezt a megadott értéket a házirend-kezdeményezésen belül az "Engedélyezett helyek" házirend is használja.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A biztonsági rés felmérését engedélyezni kell az SQL által kezelt példányokon|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A biztonsági rés felmérését engedélyezni kell az SQL-kiszolgálókon|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A biztonsági rés felmérését engedélyezni kell a virtuális gépeken|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A földrajzilag redundáns tárolást engedélyezni kell a tárfiókokszámára|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A georedundáns biztonsági mentést engedélyezni kell a MariaDB Azure Database számára|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A georedundáns biztonsági mentést engedélyezni kell a MySQL-hez készült Azure Database számára|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A georedundáns biztonsági mentést engedélyezni kell a PostgreSQL Azure Database számára|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A hálózati biztonsági csoport internetes virtuális gépekre vonatkozó szabályait meg kell erősíteni|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A webalkalmazás csak HTTPS-en keresztül érhető el|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A Függvényalkalmazás csak HTTPS-en keresztül érhető el|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az írási engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az olvasási engedéllyel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az elavult fiókokat el kell távolítani az előfizetésből|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A CORS nem teszi lehetővé, hogy minden erőforrás hozzáférjen a webalkalmazáshoz|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|A virtuálisgép-méretezési csoportok rendszerfrissítéseit telepíteni kell|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéséhez olvasási engedéllyel rendelkező fiókokon|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéstulajdonosi engedélyekkel rendelkező fiókokban|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Az MFA-t engedélyezni kell az előfizetéshez írási engedéllyel rendelkező fiókokon|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|\[Előzetes\]: DoD Impact Level 4|Szabályzat-hozzárendelés|Hosszú távú georedundáns biztonsági mentést engedélyezni kell az Azure SQL-adatbázisokhoz|A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók.|
|Engedélyezett helyek|Szabályzat-hozzárendelés|Engedélyezett helyek|Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. A földrajzi megfelelőségi követelmények betartására szolgál.|
|Engedélyezett helyek erőforráscsoportokhoz|Szabályzat-hozzárendelés |Engedélyezett helyek|Ez a házirend lehetővé teszi, hogy korlátozza azokat a helyeket, ahol a szervezet erőforráscsoportokat hozhat létre. A földrajzi megfelelőségi követelmények betartására szolgál.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a DoD Impact Level 4 tervminta telepítésének lépéseit, a következő cikkekben ismerkedhet meg a tervrajz és a vezérlőleképezés lehetőségével:

> [!div class="nextstepaction"]
> [DoD Impact Level 4 terv - Áttekintés](./index.md)
> [DoD Impact Level 4 terv - Control mapping](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)