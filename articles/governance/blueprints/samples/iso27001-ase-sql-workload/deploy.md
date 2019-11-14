---
title: ISO 27001-es bevezetési/SQL munkaterhelés-tervezet minta – üzembe helyezési lépések
description: Az ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet lépéseinek üzembe helyezése minta, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 03/14/2019
ms.topic: sample
ms.openlocfilehash: edcf9920f3a65c182240da735b1883b3d67da650
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032136"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Az ISO 27001 App Service Environment/SQL Database munkaterhelés tervrajzi minta üzembe helyezése

Az Azure-tervezetek üzembe helyezéséhez ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet mintáját a következő lépésekkel kell elvégezni:

> [!div class="checklist"]
> - Az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta üzembe helyezése
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Shared Services Blueprint minta üzembe helyezése

A tervrajzi minta üzembe helyezése előtt az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint mintát kell központilag telepíteni a cél előfizetésre. Az ISO 27001 Shared Services Blueprint minta sikeres üzembe helyezése nélkül ez a tervrajz-minta hiányzó infrastrukturális függőségeket jelez, és az üzembe helyezés során sikertelen lesz.

> [!IMPORTANT]
> Ezt a tervrajz-mintát ugyanahhoz az előfizetéshez kell rendelni, mint az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg az **ISO 27001: beadási/SQL munkaterhelés** tervrajz mintát _más minták_ alatt, és válassza **a minta használata**lehetőséget.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet az ISO 27001-es vagy az SQL-alapú számítási terv mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza ki az oldal tetején található _összetevők fület_ , vagy a **következőt:** összetevők az oldal alján.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás az ISO 27001 szabványból is áthelyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az ISO 27001 Blueprint Sample-ből lett közzétéve." Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a terv definíciójában található számos összetevő használja a konzisztencia biztosításához.

     - **Szervezet neve**: adjon meg egy rövid nevet a szervezetnek. Ez a tulajdonság elsősorban az erőforrások elnevezésére használatos.
     - **Megosztott szolgáltatás előfizetési azonosítója**: előfizetés azonosítója, ahol az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta van hozzárendelve.
     - **Alapértelmezett alhálózat-címzési előtag**: a virtuális hálózat alapértelmezett alhálózatának CIDR-jelölése.
       Az alapértelmezett érték a _10.1.0.0/16_.
     - **Munkaterhelés helye**: meghatározza, hogy az összetevők milyen helyen legyenek telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméteres lehetőséget biztosítanak ahhoz, hogy az adott összetevő üzembe helyezése megtörténjen.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Log Analytics erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolva** – összefűzi a **szervezet nevét** a `-workload-log-rg` az erőforráscsoport egyedivé tételéhez.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Log Analytics sablon|Resource Manager-sablon|Szolgáltatásszint|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték a _PerNode_.|
|Log Analytics sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Log Analytics sablon|Resource Manager-sablon|Hely|A Log Analytics munkaterület létrehozásához használt régió. Az alapértelmezett érték az _USA 2. nyugati_régiója.|
|Hálózati erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolva** – összefűzi a **szervezet nevét** a `-workload-net-rg` az erőforráscsoport egyedivé tételéhez.|
|Hálózati erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Hálózati biztonsági csoport sablonja|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Azure tűzfal magánhálózati IP-címe|Az [Azure tűzfal](../../../../firewall/overview.md)magánhálózati IP-címének konfigurálása. Az _ISO 27001: Shared Services_ összetevő-paraméterben definiált CIDR-jelölésnek kell lennie **Azure Firewall alhálózati címek előtagja**. Az alapértelmezett érték a _10.0.4.4_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Megosztott szolgáltatások előfizetés-azonosítója|A számítási feladatok és a megosztott szolgáltatások közötti VNET-társítást engedélyező érték.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network címzési előtag|A virtuális hálózat CIDR-jelölése. Az alapértelmezett érték a _10.1.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat alapértelmezett címének előtagja|A virtuális hálózat alapértelmezett alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.1.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|IP-cím HOZZÁADÁSával|Az első virtuális gép IP-címe. Ezt az értéket használja a rendszer egyéni VNET DNS-ként.|
|Key Vault erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolva** – összefűzi a **szervezet nevét** a `-workload-kv-rg` az erőforráscsoport egyedivé tételéhez.|
|Key Vault erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Key Vault sablon|Resource Manager-sablon|HRE objektum azonosítója|Annak a fióknak a HRE-azonosítója, amelyhez hozzáférést kell adni a Key Vault-példányhoz. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_melletti "kattintson ide a másoláshoz" ikont.|
|Key Vault sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault SKU|A létrehozott Key Vault SKU-jának meghatározása. Az alapértelmezett érték a _prémium_.|
|Key Vault sablon|Resource Manager-sablon|Azure SQL Server-rendszergazdai Felhasználónév|Az Azure SQL Server eléréséhez használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Azure SQL Database sablonban**. Az alapértelmezett érték az _SQL-Admin-User_.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolva** – összefűzi a **szervezet nevét** a `-workload-azsql-rg` az erőforráscsoport egyedivé tételéhez.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server-rendszergazdai Felhasználónév|Az Azure-SQL Server felhasználóneve. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték az _SQL-Admin-User_.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" kifejezést, és cserélje le a `{subscriptionId}`t az előfizetés-azonosítójával, és `{orgName}` a **szervezet neve** Blueprint paraméterrel.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault titkos kulcs neve)|A SQL Server-rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonság **Azure SQL Server rendszergazdai Felhasználónév**.|
|Azure SQL Database sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Azure SQL Database sablon|Resource Manager-sablon|HRE rendszergazdai objektum azonosítója|Azon felhasználó HRE-azonosítója, amely Active Directory-rendszergazdaként lesz hozzárendelve. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_melletti "kattintson ide a másoláshoz" ikont.|
|Azure SQL Database sablon|Resource Manager-sablon|HRE-rendszergazdai bejelentkezés|Jelenleg a Microsoft-fiókok (például a live.com vagy a outlook.com) nem állíthatók be rendszergazdaként. Csak a szervezeten belüli felhasználók és biztonsági csoportok állíthatók be rendszergazdaként. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon másolja a _felhasználónevet_.|
|App Service Environment erőforráscsoport|Erőforráscsoport|Name (Név)|**Zárolva** – összefűzi a **szervezet nevét** a `-workload-ase-rg` az erőforráscsoport egyedivé tételéhez.|
|App Service Environment erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|App Service Environment sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték a _contoso.com_.|
|App Service Environment sablon|Resource Manager-sablon|Bemutató hely|App Service Environment hely. Az alapértelmezett érték az _USA 2. nyugati_régiója.|
|App Service Environment sablon|Resource Manager-sablon|Application Gateway napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [Iso 27001 app Service Environment/SQL Database munkaterhelés tervezete – áttekintés](./index.md)
> [ISO 27001 app Service Environment/SQL Database munkaterhelés terv – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.