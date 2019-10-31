---
title: ISO 27001 megosztott szolgáltatások tervezetének mintája – üzembe helyezési lépések
description: Az ISO 27001 megosztott szolgáltatások tervezetének üzembe helyezési mintája, beleértve a tervrajz-összetevő paraméterének részleteit.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 43b3eb76de4e180f002379198d363a6852ab9e3b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162490"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Shared Services Blueprint minta üzembe helyezése

Az Azure BluePrints ISO 27001 Shared Services Blueprint minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg az **ISO 27001: Shared Services** Blueprint mintát _más minták_ alatt, és válassza **a minta használata**lehetőséget.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet az ISO 27001 Shared Services Blueprint minta példányának.
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
     - **Megosztott szolgáltatások alhálózati címének előtagja**: adja meg a CIDR-jelölési értéket a központilag telepített erőforrások egyesítéséhez.
     - **Megosztott szolgáltatások helye**: meghatározza, hogy az összetevők milyen helyen legyenek telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméteres lehetőséget biztosítanak ahhoz, hogy az adott összetevő üzembe helyezése megtörténjen.
     - **Engedélyezett hely (szabályzat: terv kezdeményezése ISO 27001 esetén)** : érték, amely az erőforráscsoportok és az erőforrások megengedett helyét jelzi.
     - Virtuálisgép **-ügynökök log Analytics munkaterülete (szabályzat: az ISO 27001-es terv kezdeményezése)** : a munkaterület erőforrás-azonosítóját adja meg. Ez a paraméter egy `concat` függvényt használ az erőforrás-azonosító létrehozásához.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_ .|
|\[előzetes verzió\]: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_ .|
|\[előzetes verzió\]: Log Analytics Agent telepítése Windows VM Scale Sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_ .|
|\[előzetes verzió\]: Log Analytics ügynök központi telepítése Windows rendszerű virtuális gépekre|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_ .|
|Engedélyezett erőforrástípusok|Szabályzat-hozzárendelés|Engedélyezett erőforrástípusok|Az üzembe helyezhető erőforrástípusok listája. Ez a lista a megosztott szolgáltatásokban üzembe helyezett összes erőforrás-típusból tevődik össze.|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett tárolási SKU-ket|A diagnosztikai naplók Storage-fiókjának SKU-ban engedélyezett listája. Az alapértelmezett érték: _["Standard_LRS"]_ .|
|Engedélyezett virtuális gépek SKU-i|Szabályzat-hozzárendelés|A telepítendő virtuálisgép-SKU-ket tartalmazó lista. Az alapértelmezett érték a következő: _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Tervezet kezdeményezés ISO 27001|Szabályzat-hozzárendelés|A diagnosztikai naplók naplózására szolgáló erőforrástípusok|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)között.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Név|**Zárolva** – a **szervezet nevének** összefűzése `-sharedsvsc-log-rg` értékkel, hogy az erőforráscsoport egyedi legyen.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Földrajzi egység|**Zárolt** – a terv paramétert használja.|
|Log Analytics sablon|Resource Manager-sablon|Szolgáltatáscsomag|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték a _PerNode_.|
|Log Analytics sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Log Analytics sablon|Resource Manager-sablon|Földrajzi egység|A Log Analytics munkaterület létrehozásához használt régió. Az alapértelmezett érték az _USA 2. nyugati_régiója.|
|Hálózati erőforráscsoport|Erőforráscsoport|Név|**Zárolva** – a **szervezet nevének** összefűzése `-sharedsvcs-net-rg` értékkel, hogy az erőforráscsoport egyedi legyen.|
|Hálózati erőforráscsoport|Erőforráscsoport|Földrajzi egység|**Zárolt** – a terv paramétert használja.|
|Azure Firewall sablon|Resource Manager-sablon|Azure tűzfal magánhálózati IP-címe|Az [Azure tűzfal](../../../../firewall/overview.md)magánhálózati IP-címének konfigurálása. Ez az érték a megosztott szolgáltatások alhálózatán alapértelmezett útválasztási táblázatként is használatos. A **Azure Firewall alhálózati**CIDR definiált jelölésnek kell lennie. Az alapértelmezett érték a _10.0.4.4_.|
|Azure Firewall sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Hálózati biztonsági csoport sablonja|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network címzési előtag|A virtuális hálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network DDoS elleni védelem engedélyezése|A DDoS Protection beállítása a virtuális hálózathoz. Az alapértelmezett érték _true (igaz_).|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Megosztott szolgáltatások alhálózati címének előtagja|A megosztott szolgáltatások alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.0.0.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|DMZ alhálózati címének előtagja|A DMZ-alhálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.1.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat Application Gateway-címének előtagja|Az Application Gateway-alhálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.2.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network átjáró alhálózati címének előtagja|A virtuális hálózati átjáró alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.0.3.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat Azure Firewall-címének előtagja|Az [Azure tűzfal](../../../../firewall/overview.md) alhálózatának CIDR-jelölése. Tartalmaznia kell az **Azure tűzfal magánhálózati IP-** paraméterét.|
|Key Vault erőforráscsoport|Erőforráscsoport|Név|**Zárolva** – a **szervezet nevének** összefűzése `-sharedsvcs-kv-rg` értékkel, hogy az erőforráscsoport egyedi legyen.|
|Key Vault erőforráscsoport|Erőforráscsoport|Földrajzi egység|**Zárolt** – a terv paramétert használja.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox-rendszergazdai Felhasználónév|A Jumpbox tartozó Felhasználónév. Meg kell egyeznie a **Jumpbox-sablonban**szereplő tulajdonság értékével. Az alapértelmezett érték a _JB-Admin-User_.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox rendszergazdai SSH-kulcs vagy jelszó|A Jumpbox lévő fiók kulcsa vagy jelszava. Meg kell egyeznie a **Jumpbox-sablonban**szereplő tulajdonság értékével. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|A Active Directory virtuális gép eléréséhez és más virtuális gépek tartományhoz való csatlakoztatásához használt Felhasználónév. Meg kell egyeznie a **tartományi rendszergazda felhasználói** tulajdonság értékével **Active Directory tartományi szolgáltatások sablonban**. Az alapértelmezett érték a _tartományi rendszergazda – felhasználó_.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava|A tartományi rendszergazda felhasználói jelszava. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|HRE objektum azonosítója|Annak a fióknak a HRE-azonosítója, amelyhez hozzáférést kell adni a Key Vault-példányhoz. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_melletti "kattintson ide a másoláshoz" ikont.  |
|Key Vault sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault SKU|A létrehozott Key Vault SKU-jának meghatározása. Az alapértelmezett érték a _prémium_.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Név|**Zárolva** – a **szervezet nevének** összefűzése `-sharedsvcs-jb-rg` értékkel, hogy az erőforráscsoport egyedi legyen.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Földrajzi egység|**Zárolt** – a terv paramétert használja.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox-rendszergazdai Felhasználónév|A Jumpbox virtuális gépek eléréséhez használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _JB-Admin-User_.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszava (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" kifejezést, és cserélje le a `{subscriptionId}` értéket az előfizetés-azonosítójával, és `{orgName}` értéket a **szervezet nevével** terv paraméter.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszava (Key Vault Secret Name)|A Jumpbox-rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonságában a **Jumpbox rendszergazdai felhasználóneve**.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox operációs rendszer|Meghatározza a Jumpbox virtuális gép operációs rendszerét. Az alapértelmezett érték a _Windows_.|
|Active Directory tartományi szolgáltatások erőforráscsoport|Erőforráscsoport|Név|**Zárolva** – a **szervezet nevének** összefűzése `-sharedsvcs-adds-rg` értékkel, hogy az erőforráscsoport egyedi legyen.|
|Active Directory tartományi szolgáltatások erőforráscsoport|Erőforráscsoport|Földrajzi egység|**Zárolt** – a terv paramétert használja.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|A Jumpbox HOZZÁADÁSához használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _Add-admin-User_.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" kifejezést, és cserélje le a `{subscriptionId}` értéket az előfizetés-azonosítójával, és `{orgName}` értéket a **szervezet nevével** terv paraméter.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava (Key Vault titkos kulcs neve)|A tartományi rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonságának **tartományi rendszergazdai felhasználóneve**.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték a _contoso.com_.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználó|A rendszergazdai AD-fiókhoz tartozó Felhasználónév és az eszközök AD-tartományhoz való csatlakoztatása. Meg kell egyeznie az **ad admin username** tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _tartományi rendszergazda – felhasználó_.|
|Active Directory tartományi szolgáltatások sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava|Adja meg a Jelszó tárolásának Key Vault részleteit. Nincs alapértelmezett érték, és nem hagyható üresen.|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 Shared Services Blueprint-minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [Iso 27001 megosztott szolgáltatások tervrajza – áttekintés](./index.md)
> [ISO 27001 megosztott szolgáltatások tervezete – vezérlés leképezése](./control-mapping.md)

További cikkek a tervrajzokról és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
