---
title: 'Hibakeresési hiba: Azure Functions-futtatókörnyezet nem érhető el'
description: Ismerje meg, hogyan lehet elhárítani egy érvénytelen Storage-fiókot.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 0b6778a08bf04367f2a0ef10f7cd4fe29a52dd61
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579011"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Hibakeresési hiba: "a Azure Functions-futtatókörnyezet nem érhető el"

Ez a cikk segítséget nyújt a Azure Portalban megjelenő következő hibaüzenetek hibakeresésében:

> "Hiba: a Azure Functions-futtatókörnyezet nem érhető el. Kattintson ide a tárolási konfiguráció részleteinek megtekintéséhez.

Ez a probléma akkor fordul elő, ha a Azure Functions-futtatókörnyezet nem indítható el. A probléma leggyakoribb oka, hogy a Function alkalmazás elvesztette a hozzáférést a Storage-fiókjához. További információ: a [Storage-fiókra vonatkozó követelmények](./functions-create-function-app-portal.md#storage-account-requirements).

A cikk további részében a hiba következő okának elhárítása segít, beleértve az egyes esetek azonosítását és megoldását.

## <a name="storage-account-was-deleted"></a>A Storage-fiók törölve

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törölve lett, a függvény nem fog működni.

Első lépésként tekintse meg a Storage-fiók nevét az alkalmazás beállításaiban. `AzureWebJobsStorage`Vagy `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` tartalmazza a Storage-fiók nevét, amely egy kapcsolatok karakterláncában van becsomagolva. További információ: [az Alkalmazásbeállítások referenciája Azure functions](./functions-app-settings.md#azurewebjobsstorage).

Keresse meg a Storage-fiókját a Azure Portalban, és ellenőrizze, hogy még létezik-e. Ha törölték, hozza létre újra a Storage-fiókot, és cserélje le a tárolási kapcsolatok karakterláncait. A függvény kódja elveszett, és újra kell telepítenie.

## <a name="storage-account-application-settings-were-deleted"></a>A Storage-fiók alkalmazásának beállításai törölve

Ha az előző lépésben nem találja a Storage-fiókhoz tartozó kapcsolatok karakterláncát, a rendszer valószínűleg törölte vagy felülírta azt. Az Alkalmazásbeállítások törlésére leggyakrabban akkor kerül sor, ha üzembe helyezési pontokat használ, vagy Azure Resource Manager parancsfájlokat az Alkalmazásbeállítások megadásához.

### <a name="required-application-settings"></a>Szükséges Alkalmazásbeállítások

* Szükséges
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* A használat és a Prémium csomag funkcióihoz szükséges:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

További információ: [az Alkalmazásbeállítások referenciája Azure functions](./functions-app-settings.md).

### <a name="guidance"></a>Útmutató

* Ezeknél a beállításoknál ne a "tárolóhely beállítása" lehetőséget. Ha az üzembe helyezési pontokat cseréli, a Function alkalmazás megszakítja a műveletet.
* Ne módosítsa ezeket a beállításokat az automatikus telepítések részeként.
* Ezeket a beállításokat meg kell adni, és érvényesnek kell lenniük a létrehozáskor. Egy automatikus központi telepítés, amely nem tartalmazza ezeket a beállításokat, a függvény alkalmazás nem fog futni, még akkor sem, ha később hozzáadják a beállításokat.

## <a name="storage-account-credentials-are-invalid"></a>A Storage-fiók hitelesítő adatai érvénytelenek

A tárolási kulcsok újragenerálása esetén frissíteni kell a korábban tárgyalt Storage-fiókhoz tartozó kapcsolatok karakterláncait. További információ a Storage Key Management szolgáltatásról: [Azure Storage-fiók létrehozása](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>A Storage-fiók nem érhető el

A Function alkalmazásnak képesnek kell lennie hozzáférni a Storage-fiókhoz. A Function app egy Storage-fiókhoz való hozzáférését letiltó gyakori problémák a következők:

* A Function alkalmazást a rendszer a megfelelő hálózati szabályok nélkül helyezi üzembe a App Service Environment a Storage-fiókba irányuló és onnan érkező forgalom engedélyezéséhez.

* A Storage-fiók tűzfala engedélyezve van, és nincs konfigurálva a függvények felé irányuló és onnan érkező forgalom engedélyezéséhez. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A napi végrehajtási kvóta megtelt

Ha a napi végrehajtási kvóta konfigurálva van, a Function alkalmazás átmenetileg le lesz tiltva, ami miatt a portál számos vezérlője elérhetetlenné válik. 

A [Azure Portal](https://portal.azure.com)kvótájának ellenőrzéséhez válassza a **platform szolgáltatásai**  >  **függvényalkalmazás beállítások** elemet a Function alkalmazásban. Ha a beállított **napi használati kvótát** használja, a következő üzenet jelenik meg:

  > "A függvényalkalmazás elérte a napi használati kvótát, és a következő 24 órás időkereten belül le lett állítva."

A probléma megoldásához távolítsa el vagy növelje meg a napi kvótát, majd indítsa újra az alkalmazást. Ellenkező esetben az alkalmazás végrehajtása a következő napig le lesz tiltva.

## <a name="app-is-behind-a-firewall"></a>Az alkalmazás tűzfal mögött van

Előfordulhat, hogy a függvény futtatókörnyezete a következő okok valamelyike miatt nem érhető el:

* A Function alkalmazást [belső terheléselosztású app Service Environment](../app-service/environment/create-ilb-ase.md) üzemelteti, és úgy van konfigurálva, hogy letiltsa a bejövő internetes forgalmat.

* A Function alkalmazás [bejövő IP-korlátozásokkal](functions-networking-options.md#inbound-access-restrictions) rendelkezik, amelyek az Internet-hozzáférés blokkolására vannak konfigurálva. 

A Azure Portal közvetlenül a futó alkalmazásban hívja meg a függvények listáját, és HTTP-hívásokat kezdeményez az kudu-végpontra. A platform **szolgáltatásai** lap platform szintű beállításai továbbra is elérhetők.

A App Service Environment konfiguráció ellenőrzése:
1. Nyissa meg annak az alhálózatnak a hálózati biztonsági csoportját (NSG), ahol a App Service Environment található.
1. Ellenőrizze, hogy a bejövő szabályok lehetővé teszik-e az alkalmazáshoz hozzáférő számítógép nyilvános IP-címéről érkező forgalom engedélyezését. 
   
A portált olyan számítógépről is használhatja, amely az alkalmazást futtató virtuális hálózathoz vagy a virtuális hálózatban futó virtuális géphez van csatlakoztatva. 

A bejövő szabályok konfigurálásával kapcsolatos további információkért tekintse [meg az App Service Environment hálózatkezelési szempontjainak](../app-service/environment/network-info.md#network-security-groups)hálózati biztonsági csoportjai című szakaszát.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a Function apps figyelését:

> [!div class="nextstepaction"]
> [Az Azure Functions monitorozása](functions-monitoring.md)
