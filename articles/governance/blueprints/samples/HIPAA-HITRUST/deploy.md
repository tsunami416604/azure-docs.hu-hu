---
title: A HIPAA HITRUST minta telepítése
description: A HIPAA HITRUST-minta lépéseit, beleértve a tervezet összetevő paraméterének részleteit.
ms.date: 12/03/2019
ms.topic: sample
ms.openlocfilehash: a4277dbbf40a9ab8651598203e9279bca00eb164
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472302"
---
# <a name="deploy-the-hipaa-hitrust-blueprint-sample"></a>A HIPAA HITRUST tervezetminta telepítése

A HIPAA HITRUST tervezetminta telepítéséhez a következő lépéseket kell tenni:

> [!div class="checklist"]
> - Új tervrajz létrehozása a mintából
> - A minta példányának megjelölése **közzétettként**
> - A tervrajz másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először valósítsa meg a tervtervezet minta egy új tervtervezet a környezetben a minta, mint a kezdő.

1. Válassza a **Minden szolgáltatás lehetőséget,** és a bal oldali ablaktáblában keresse meg a Házirend elemet, és válassza a **Házirend** lehetőséget. A **Házirend** lapon válassza a **Tervrajzok**lehetőséget.

1. A bal oldali **Első lépések** lapon válassza a **Létrehozás** gombot a _Rajzrajz létrehozása_csoportban.

1. Keresse meg a **HIPAA HITRUST** tervezetmintát _az Egyéb minták csoportban,_ és válassza **a Minta használata**lehetőséget.

1. Adja meg a tervminta _alapjait:_

   - **Tervrajz neve:** Adja meg a tervezet minta példányának nevét.
   - **Definíció helye:** Használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az _Eltérések_ lapot a lap tetején, vagy a **Tovább: Eltérések** a lap alján.

1. Tekintse át a tervmintát kiálló összetevők listáját. Sok összetevő nek vannak paraméterei, amelyeket később definiálunk. Válassza **a Vázlat mentése lehetőséget,** ha befejezte a tervezetminta áttekintését.

## <a name="publish-the-sample-copy"></a>A mintapéldány közzététele

A tervtervezet minta példánya most már létre lett hozva a környezetében. **Vázlat** módban jön létre, és közzé kell **tenni,** mielőtt hozzárendelhető és üzembe helyezhető. A tervezetminta másolata testreszabható a környezetés az igények igényei szerint, de a módosítás áthelyezheti azt a szabványtól.

1. Válassza a **Minden szolgáltatás lehetőséget,** és a bal oldali ablaktáblában keresse meg a Házirend elemet, és válassza a **Házirend** lehetőséget. A **Házirend** lapon válassza a **Tervrajzok**lehetőséget.

1. Válassza ki a **blueprint definíciók** lap a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, majd válassza ki.

1. Válassza a **Tervrajz közzététele** lehetőséget a lap tetején. A jobb oldali új lapon adja meg a tervezetminta egy **verzióját.** Ez a tulajdonság akkor hasznos, ha később módosítja a munkát. Adjon **meg módosítási megjegyzéseket,** például "A HITRUST HIPAA tervezetmintából közzétett első verzió". Ezután válassza a **Közzététel** lehetőséget a lap alján.

## <a name="assign-the-sample-copy"></a>A mintapéldány hozzárendelése

Miután a tervezetminta példánya sikeresen **közzélett,,** hozzárendelhető egy előfizetéshez a felügyeleti csoporton belül, amelybe mentve mentették. Ez a lépés az, ahol paraméterek et biztosítanak, hogy a tervezet minta másolatának minden egyes központi telepítése egyedi legyen.

1. Válassza a **Minden szolgáltatás lehetőséget,** és a bal oldali ablaktáblában keresse meg a Házirend elemet, és válassza a **Házirend** lehetőséget. A **Házirend** lapon válassza a **Tervrajzok**lehetőséget.

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

Műtermék neve|Műtermék típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez |Szabályzat-hozzárendelés |Log Analytics munkaterület Linuxos virtuális gépekhez |További információ: [Log Analytics-munkaterület létrehozása az Azure Portalon.](../../../../azure-monitor/learn/quick-create-workspace.md) |
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Linuxos virtuális gépekhez |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Linux operációs rendszert |Üres tömb használható választható paraméterek jelzésére:`[]` |
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatták a hatókörhöz hozzáadni kívánt Windows operációs rendszert |Üres tömb használható választható paraméterek jelzésére:`[]` |
|\[Előzetes\]verzió : Log Analytics-ügynök telepítése Windows virtuális gépekhez |Szabályzat-hozzárendelés |Log Analytics-munkaterület Windows virtuális gépekhez |További információ: [Log Analytics-munkaterület létrehozása az Azure Portalon.](../../../../azure-monitor/learn/quick-create-workspace.md) |
|\[Előzetes\]verzió : HiTRUST HIPAA-vezérlők naplózása és adott virtuálisgép-bővítmények telepítése a naplózási követelmények támogatása érdekében |Szabályzat-hozzárendelés |A Log Analytics-munkaterület-azonosító, amelyhez a virtuális gépeket konfigurálni kell |Ez annak a Log Analytics-munkaterületnek az azonosítója (GUID), amelyhez a virtuális gépeket konfigurálni kell. |
|\[Előzetes\]verzió : HiTRUST HIPAA-vezérlők naplózása és adott virtuálisgép-bővítmények telepítése a naplózási követelmények támogatása érdekében |Szabályzat-hozzárendelés |Azon erőforrástípusok listája, amelyeknek engedélyeznie kell a diagnosztikai naplókat |Naplózandó erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Az elfogadható értékek az [Azure Monitor diagnosztikai naplóinak sémájában](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)találhatók. |
|\[Előzetes\]verzió : HiTRUST HIPAA-vezérlők naplózása és adott virtuálisgép-bővítmények telepítése a naplózási követelmények támogatása érdekében |Szabályzat-hozzárendelés |Rendszergazdák csoport |Csoport. Például: `Administrator; myUser1; myUser2` |
|\[Előzetes\]verzió : HiTRUST HIPAA-vezérlők naplózása és adott virtuálisgép-bővítmények telepítése a naplózási követelmények támogatása érdekében |Szabályzat-hozzárendelés |A Windows Virtuálisgép-rendszergazdák csoportba bevonandó felhasználók listája |A tagok pontosvesszővel elválasztott listája, amelyet a Rendszergazdák helyi csoportba kell foglalni. Például: `Administrator; myUser1; myUser2` |
|Speciális veszélyforrások elleni védelem telepítése a tárfiókokon |Szabályzat-hozzárendelés |Hatás |A szabályzathatásaival kapcsolatos információk az [Azure policy effects ismertetése című](../../../policy/concepts/effects.md)helyen találhatók. |
|Naplózás telepítése SQL-kiszolgálókon |Szabályzat-hozzárendelés |A megőrzési időszak napjaiban lévő érték (a 0 korlátlan megőrzést jelez) |Megőrzési napok (nem kötelező, _180_ nap, ha nincs megadva) |
|Naplózás telepítése SQL-kiszolgálókon |Szabályzat-hozzárendelés |Az SQL-kiszolgáló naplózásához való tárfiók erőforráscsoport-neve |Naplózási írja az adatbázis-eseményeket egy naplót az Azure Storage-fiók (a tárfiók jön létre minden régióban, ahol egy SQL Server jön létre, amely az adott régió összes kiszolgálója által megosztott). Fontos – a naplózás megfelelő működéséhez ne törölje vagy nevezze át az erőforráscsoportot vagy a tárfiókokat. |
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése |Szabályzat-hozzárendelés |Tárfiók előtagja a hálózati biztonsági csoport diagnosztikájához |Ez az előtag a hálózati biztonsági csoport helyével kombinálva alkotja a létrehozott tárfiók nevét. |
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése |Szabályzat-hozzárendelés |A hálózati biztonságicsoport diagnosztikájához a tárfiók erőforráscsoportának neve (léteznie kell) |Az erőforráscsoport, amelyben a tárfiók jön létre. Ennek az erőforráscsoportnak már léteznie kell. |

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a HITRUST HIPAA-minta üzembe helyezésének lépéseit, az alábbi cikkekben ismerkedhet meg az áttekintő és vezérlőleképezéssel:

> [!div class="nextstepaction"]
> [HITRUST HIPAA tervrajzok - Áttekintés](./index.md)
> [HITRUST HIPAA tervrajzok - Control mapping](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- További információ a [tervterv életciklusáról.](../../concepts/lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](../../concepts/parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](../../concepts/sequencing-order.md)
- Ismerje meg, hogyan használhatja a [tervezet erőforrás-zárolást.](../../concepts/resource-locking.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../../how-to/update-existing-assignments.md)