---
title: Minta - ISO 27001 App Service környezet/SQL-adatbázis-munkaterhelés tervezet - telepítés lépéseit
description: Telepítés lépéseit, az ISO 27001 App Service környezet/SQL-adatbázis számítási feladatok tervezet minta.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 80c98170fc136c20d8489cec5d145f96e207bc9f
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201908"
---
# <a name="deploy-the-azure-blueprints-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>A számítási feladatok Azure tervezetek ISO 27001 App Service környezet/SQL Database tervezet minta üzembe helyezése

Az Azure tervezetek ISO 27001 App Service környezet/SQL Database munkaterhelés tervezet minta üzembe helyezéséhez az alábbi lépéseket kell tenni:

> [!div class="checklist"]
> - Üzembe helyezése a [ISO 27001, a megosztott szolgáltatások](../iso27001-shared/index.md) tervezet minta
> - Hozzon létre egy új tervezet arról a minta
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001, a megosztott szolgáltatások tervezet-minta üzembe helyezése

A tervezet-minta is üzembe helyezhetők, mielőtt a [ISO 27001, a megosztott szolgáltatások](../iso27001-shared/index.md) tervezet minta telepíteni kell a cél előfizetésben. Egy sikeres, az ISO 27001, a megosztott szolgáltatások tervezet-minta üzembe helyezés nélkül a tervezet minta hiányozni fognak infrastruktúra függőségeket és a sikertelen üzembe helyezés során.

> [!IMPORTANT]
> A tervezet minta az azonos előfizetésben kell hozzárendelni a [ISO 27001, a megosztott szolgáltatások](../iso27001-shared/index.md) tervezet minta.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először meg a tervezet minta létrehoz egy új tervezet alapszintű, a minta az környezet.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **ISO 27001: ASE/SQL számítási feladatok** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a az ISO 27001 ASE/SQL számítási feladatok tervezet minta példányának nevét.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Az összetevők számos rendelkezik, paraméterek, amelyeket később fogunk meghatározni. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet minta másolatát is beállítható, hogy a környezet és az igényeinek, azonban, hogy a módosítás lehet, hogy esniük, az ISO 27001 szabványnak.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. A jobb oldalon az új lap, adja meg egy **verzió** a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója közzé az ISO 27001 tervezet mintából." Válassza ki **közzététel** az oldal alján.

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

   - Hozzárendelés zárolása

     Válassza ki a tervezet zárolás, a környezet beállítása. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás lehetőséget.

   - Tervparaméterek

     Ebben a szakaszban definiált paraméterek segítségével számos, az összetevőket a tervezetdefiníciót a konzisztenciát.

     - **Szervezet neve**: Adjon meg egy rövid nevet a szervezet számára. Ez a tulajdonság erőforrások elnevezési elsősorban.
     - **Előfizetés-azonosító megosztott**: Előfizetés-azonosító, a [ISO 27001, a megosztott szolgáltatások](../iso27001-shared/index.md) tervezet minta hozzá van rendelve.
     - **Alapértelmezett alhálózati cím előtagja**: A virtuális hálózat alapértelmezett alhálózat CIDR-jelölésrendszerben.
       Alapértelmezett érték _10.1.0.0/16_.
     - **Számítási feladatok hely**: Meghatározza, hogy milyen helyet az összetevők telepítve vannak. Nem minden szolgáltatás az összes hely érhető el. Ilyen szolgáltatások üzembe helyezése összetevők lehetőséget nyújtanak olyan paramétert a lehívandó összetevő telepítése a helyen.

   - Összetevő paraméterei

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. A teljes listát vagy összetevő paraméterek és ezek leírását: [összetevő paramétereket tartalmazó](#artifact-parameters-table).

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján. A tervezet-hozzárendelést jön létre, és összetevő telepítési kezdődik. Üzembe helyezés eltarthat nagyjából egy óra. Üzembe helyezés állapotának ellenőrzéséhez nyissa meg a tervezet-hozzárendelést.

> [!WARNING]
> Az Azure-tervek szolgáltatás és a beépített tervezet minták **díjmentes**. Az Azure-erőforrások [termék díjszabása](https://azure.microsoft.com/pricing/). Használja a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) a tervezet minta által üzembe helyezett erőforrások futtatásával járó költségeket megbecsülheti.

## <a name="artifact-parameters-table"></a>Összetevő paraméterek táblában

A következő táblázat felsorolja a tervezet összetevő paraméterek:

|Elemnév|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Log Analytics-erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-workload-log-rg` , hogy az erőforráscsoport egyedi.|
|Log Analytics-erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Log Analytics-sablon|Resource Manager-sablon|Szolgáltatásszint|A Log Analytics-munkaterület szintjének beállítása. Alapértelmezett érték _PerNode_.|
|Log Analytics-sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Log Analytics-sablon|Resource Manager-sablon|Hely|A Log Analytics-munkaterület létrehozásához használt régió. Alapértelmezett érték _USA 2. nyugati_.|
|Hálózati erőforrás-csoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-workload-net-rg` , hogy az erőforráscsoport egyedi.|
|Hálózati erőforrás-csoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Hálózati biztonsági csoport – sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Azure Firewall magánhálózati IP-címe|Konfigurálja a magánhálózati IP-címét a [Azure tűzfal](../../../../firewall/overview.md). A meghatározott CIDR jelölésrendszer szerint kell _ISO 27001: Megosztott szolgáltatások_ összetevő paraméter **Azure tűzfal alhálózati cím előtagja**. Alapértelmezett érték _címe: 10.0.4.4_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Megosztott szolgáltatások előfizetés-azonosítója|Engedélyezze a virtuális hálózatok közötti társviszony olyan számítási feladatok és a megosztott szolgáltatások között használt érték.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Virtuális hálózat címelőtagja|A virtuális hálózat CIDR-jelölésrendszerben. Alapértelmezett érték _10.1.0.0/16_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|Alhálózati cím alapértelmezett előtagja|A virtuális hálózat alapértelmezett alhálózat CIDR-jelölésrendszerben. Alapértelmezett érték _10.1.0.0/16_.|
|Virtuális hálózat és útvonaltábla – sablon|Resource Manager-sablon|ADDS IP address|Az első ad hozzá virtuális IP-címe. Ez az érték használható egyéni virtuális hálózat DNS-t.|
|A Key Vault-erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-workload-kv-rg` , hogy az erőforráscsoport egyedi.|
|A Key Vault-erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Kulcstartósablon|Resource Manager-sablon|AAD-objektum-azonosító|A fiókot, amelyet hozzá kell férnie a Key Vault-példány AAD-objektum azonosítója. Nem alapértelmezett értékét, és nem lehet üres. Keresse meg ezt az értéket az Azure Portalról, keresse meg és válassza ki a "Felhasználók" alatt _szolgáltatások_. Használja a _neve_ be a fiók neve szűrését, és válassza ki azt a fiókot. Az a _felhasználói profil_ lapra, jelölje be a "Kattintson a másoláshoz" ikon mellett a _Objektumazonosító_.|
|Kulcstartósablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Kulcstartósablon|Resource Manager-sablon|Key Vault-termékváltozat|Adja meg a létrehozott Key Vault-Termékváltozat. Alapértelmezett érték _prémium_.|
|Kulcstartósablon|Resource Manager-sablon|Azure SQL Server-rendszergazda felhasználóneve|Az Azure SQL-kiszolgáló eléréséhez használt felhasználónév. Meg kell egyeznie az azonos tulajdonság értéke **Azure SQL Database sablon**. Alapértelmezett érték _sql-rendszergazda_.|
|Az Azure SQL Database erőforrás-csoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-workload-azsql-rg` , hogy az erőforráscsoport egyedi.|
|Az Azure SQL Database erőforrás-csoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|Azure SQL Database-sablon|Resource Manager-sablon|Azure SQL Server-rendszergazda felhasználóneve|A felhasználónév az Azure SQL Serverhez. Meg kell egyeznie az azonos tulajdonság értéke **Key Vault sablon**. Alapértelmezett érték _sql-rendszergazda_.|
|Azure SQL Database-sablon|Resource Manager-sablon|Az Azure SQL-kiszolgáló-rendszergazdai jelszót (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-Azonosítóját. Használat "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv", és cserélje le `{subscriptionId}` meg előfizetési azonosítóját és `{orgName}` az a  **Szervezet neve** tervezetet paraméter.|
|Azure SQL Database-sablon|Resource Manager-sablon|Az Azure SQL-kiszolgáló-rendszergazdai jelszót (a kulcstartó titkos kód nevét)|Felhasználónév, az SQL Server rendszergazdájával. Egyeznie kell azzal az értékkel **Key Vault sablon** tulajdonság **Azure SQL-kiszolgáló-rendszergazdai felhasználónevet**.|
|Azure SQL Database-sablon|Resource Manager-sablon|Naplófájlok megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|
|Azure SQL Database-sablon|Resource Manager-sablon|AAD-rendszergazdai objektum azonosítója|Aad-ben objektum azonosítója, amelyet a get hozzá lesz rendelve, egy Active Directory-rendszergazdával. Nem alapértelmezett értékét, és nem lehet üres. Keresse meg ezt az értéket az Azure Portalról, keresse meg és válassza ki a "Felhasználók" alatt _szolgáltatások_. Használja a _neve_ be a fiók neve szűrését, és válassza ki azt a fiókot. Az a _felhasználói profil_ lapra, jelölje be a "Kattintson a másoláshoz" ikon mellett a _Objektumazonosító_.|
|Azure SQL Database-sablon|Resource Manager-sablon|AAD-rendszergazdai bejelentkezési név|Jelenleg Microsoft-fiókok (például live.com vagy outlook.com) nem állítható be rendszergazdaként Csak a felhasználók és a biztonsági csoporttal a szervezeten belül állíthatók be rendszergazdaként Nem alapértelmezett értékét, és nem lehet üres. Keresse meg ezt az értéket az Azure Portalról, keresse meg és válassza ki a "Felhasználók" alatt _szolgáltatások_. Használja a _neve_ be a fiók neve szűrését, és válassza ki azt a fiókot. Az a _felhasználói profil_ lapon, másolja a _felhasználónév_.|
|App Service Environment-környezet erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolt** -fűzi össze a **szervezetnevet** a `-workload-ase-rg` , hogy az erőforráscsoport egyedi.|
|App Service Environment-környezet erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a tervezet paraméterrel.|
|App Service Environment-sablon|Resource Manager-sablon|Tartománynév|Az Active Directory, a példaszkript által létrehozott neve. Alapértelmezett érték _contoso.com_.|
|App Service Environment-sablon|Resource Manager-sablon|ASE helye|App Service-környezet helyére. Alapértelmezett érték _USA 2. nyugati_.|
|App Service Environment-sablon|Resource Manager-sablon|Application Gateway-napló megőrzése (nap)|Adatok megőrzése napokban. Alapértelmezett érték _365_.|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az ISO 27001 App Service környezet/SQL-adatbázis-munkaterhelés tervezet minta üzembe helyezése a lépéseket, keresse fel az architektúra és a vezérlő leképezés a következő cikkeket:

> [!div class="nextstepaction"]
> [ISO 27001 App Service környezet/SQL-adatbázis számítási feladatok tervezet - áttekintés](./index.md)
> [ISO 27001 App Service környezet/SQL-adatbázis-munkaterhelés tervezet - vezérlő leképezés](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.