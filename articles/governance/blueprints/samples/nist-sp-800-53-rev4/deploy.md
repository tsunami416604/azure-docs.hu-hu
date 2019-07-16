---
title: Minta - 800-53 előírásain NIST SP R4 tervezet - telepítés lépéseit
description: Az NIST SP fázisaiban 800-53 előírásain R4 tervezet minta üzembe helyezése.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228904"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Az NIST SP 800-53 előírásain R4 tervezet minta üzembe helyezése

A 800-53 előírásain R4 tervezet minta üzembe helyezése az Azure-tervek NIST SP, az alábbi lépéseket kell tenni:

> [!div class="checklist"]
> - Hozzon létre egy új tervezet arról a minta
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először meg a tervezet minta létrehoz egy új tervezet alapszintű, a minta az környezet.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **NIST SP 800-53 előírásain R4** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a NIST SP 800-53 előírásain R4 tervezet minta példányának nevét.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Az összetevők számos rendelkezik, paraméterek, amelyeket később fogunk meghatározni. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet minta másolatát is beállítható, hogy a környezet és az igényeinek, azonban, hogy a módosítás lehet, hogy esniük, NIST SP ciklustól 800-53 előírásain vezérlők.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. A jobb oldalon az új lap, adja meg egy **verzió** a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója közzétett a NIST SP 800-53 előírásain R4 tervezet minta." Válassza ki **közzététel** az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolási hozzárendelése

Miután a tervezet minta másolata sikeresen megtörtént **közzétett**, előfizetésre történő mentése felügyeleti csoporton belül is hozzárendelhető. Ez a lépés nem, melyekben a paraméterek vannak-e adva az, hogy az egyes telepítések a példány a tervezet minta egyedi.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több az előfizetéseket, amelyek a felügyeleti csoportban, a tervezet minta másolatának mentése. Ha egynél több előfizetéssel, hozzárendelést a megadott paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: Az Ön a tervrajz neve alapján előre megadott név.
       Szükség szerint változtassa meg, vagy hagyja üresen, mivel.
     - **Hely**: Válassza ki a régiót, a felügyelt identitás kell létrehozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Tervrajz-definíció verziója**: Válasszon ki egy **közzétett** a tervezet minta másolatának verzióját.

   - Zárolási hozzárendelés

     Válassza ki a tervezet zárolás, a környezet beállítása. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás lehetőséget.

   - Összetevő-paraméterek

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. A teljes listát vagy összetevő paraméterek és ezek leírását: [összetevő paramétereket tartalmazó](#artifact-parameters-table).

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján. A tervezet-hozzárendelést jön létre, és összetevő telepítési kezdődik. Üzembe helyezés eltarthat nagyjából egy óra. Üzembe helyezés állapotának ellenőrzéséhez nyissa meg a tervezet-hozzárendelést.

> [!WARNING]
> Az Azure-tervek szolgáltatás és a beépített tervezet minták **díjmentes**. Az Azure-erőforrások [termék díjszabása](https://azure.microsoft.com/pricing/). Használja a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) a tervezet minta által üzembe helyezett erőforrások futtatásával járó költségeket megbecsülheti.

## <a name="artifact-parameters-table"></a>Összetevő paraméterek táblában

A következő táblázat felsorolja a tervezet összetevő paraméterek:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes verzió\]: Naplózási NIST SP 800-53 előírásain R4 vezérlők és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése|Szabályzat-hozzárendelés|Virtuális gépek konfigurálni kell a log Analytics munkaterület azonosítója|Ez az az Azonosítóját (GUID), amely a virtuális gépek konfigurálni kell a Log Analytics-munkaterületen.|
|\[Előzetes verzió\]: Naplózási NIST SP 800-53 előírásain R4 vezérlők és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése|Szabályzat-hozzárendelés|Erőforrástípusok, amely elvben engedélyezett a diagnosztikai naplók listája|Ha diagnosztikai naplót a beállítás nincs engedélyezve a naplózandó erőforrástípusok listája. Elfogadható értékek található [Azure Monitor-diagnosztikai naplók sémák](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Előzetes verzió\]: Naplózási NIST SP 800-53 előírásain R4 vezérlők és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése|Szabályzat-hozzárendelés|Windows virtuális gép Rendszergazdák csoportot ki kell zárni, felhasználók listája|Ki kell zárni a helyi Rendszergazdák csoport tagjainak pontosvesszővel tagolt listája. Például: Rendszergazda; myUser1; myUser2|
|\[Előzetes verzió\]: Naplózási NIST SP 800-53 előírásain R4 vezérlők és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése|Szabályzat-hozzárendelés|Windows virtuális gép Rendszergazdák csoportjában bevonandó felhasználók listája|Szerepelnie kell a helyi Rendszergazdák csoport tagjai pontosvesszővel tagolt listája. Például: Rendszergazda; myUser1; myUser2|
|\[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics-munkaterület Linux rendszerű virtuális gép méretezési csoportok (VMSS)|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, kell manuálisan adjon jogosultságot "Log Analytics-közreműködő" (vagy hasonlót) a szabályzat-hozzárendelés egyszerű azonosító.|
|\[Előzetes verzió\]: Log Analytics-ügynököket Linux Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hogy támogatott Linux operációs rendszer hatókör hozzáadása Virtuálisgép-rendszerképek listája|Üres tömb jelzi a választható paraméterek nélkül használható: \[\]|
|\[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics-munkaterület Linux rendszerű virtuális gépekhez|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, kell manuálisan adjon jogosultságot "Log Analytics-közreműködő" (vagy hasonlót) a szabályzat-hozzárendelés egyszerű azonosító.|
|\[Előzetes verzió\]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hogy támogatott Linux operációs rendszer hatókör hozzáadása Virtuálisgép-rendszerképek listája|Üres tömb jelzi a választható paraméterek nélkül használható: \[\]|
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows virtuális gép méretezési csoportok (VMSS)|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, kell manuálisan adjon jogosultságot "Log Analytics-közreműködő" (vagy hasonlót) a szabályzat-hozzárendelés egyszerű azonosító.|
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows Virtuálisgép-méretezési csoportok (VMSS) üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hatókör hozzáadása a Windows operációs rendszer rendelkezik támogatott Virtuálisgép-rendszerképek listája|Üres tömb jelzi a választható paraméterek nélkül használható: \[\]|
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Log Analytics-munkaterület Windows-beli virtuális gépek|Ha ez a munkaterület a hozzárendelés hatókörén kívül esik, kell manuálisan adjon jogosultságot "Log Analytics-közreműködő" (vagy hasonlót) a szabályzat-hozzárendelés egyszerű azonosító.|
|\[Előzetes verzió\]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése|Szabályzat-hozzárendelés|Nem kötelező: Hatókör hozzáadása a Windows operációs rendszer rendelkezik támogatott Virtuálisgép-rendszerképek listája|Üres tömb jelzi a választható paraméterek nélkül használható: \[\]|
|Komplex veszélyforrások elleni védelem a Storage-fiókok üzembe helyezése|Szabályzat-hozzárendelés|Következmény|Házirend hatások információkat tekinthet meg [Azure házirend hatások ismertetése](../../../policy/concepts/effects.md)|
|Az SQL Server-kiszolgálók üzembe helyezése a naplózás|Szabályzat-hozzárendelés|(A 0 korlátlan megőrzési jelzi) megőrzési időszak napban megadott értéke|Adatmegőrzési napok (nem kötelező, 180 nap, ha nincs megadva)|
|Az SQL Server-kiszolgálók üzembe helyezése a naplózás|Szabályzat-hozzárendelés|SQL server-naplózás a storage-fiók erőforráscsoportjának neve|Naplózási adatbázis felvezeti ezeket egy naplófájlba, jelentkezzen be az Azure Storage-fiók (storage-fiók létrejön az egyes régiókban, ahol jön létre egy SQL Server által az adott régióban található összes kiszolgáló megosztott). Fontos – a naplózási megfelelő működéséhez nem törli vagy nevezze át az erőforráscsoportot, vagy a storage-fiókok.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|Hálózati biztonsági csoport diagnosztikai tárfiók-előtagja|A hálózati biztonsági csoport helye alkotnak a létrehozott tárfiók neve értékkel egyesítve jön létre ezt az előtagot.|
|Hálózati biztonsági csoportok diagnosztikai beállításainak telepítése|Szabályzat-hozzárendelés|Erőforráscsoport neve hálózati biztonsági csoport diagnosztikai tárfiók (már léteznie kell)|Az erőforráscsoport, amely a tárfiók létrejön. Ennek az erőforráscsoportnak már léteznie kell.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a lépéseket a NIST SP 800-53 előírásain R4 tervezet minta üzembe helyezése, látogasson el a további információt talál a tervezet-vezérlő leképezés a következő cikkeket:

> [!div class="nextstepaction"]
> [800-53 előírásain NIST SP R4 tervezet - áttekintés](./index.md)
> [NIST SP 800-53 előírásain R4 tervezet - vezérlő leképezés](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
