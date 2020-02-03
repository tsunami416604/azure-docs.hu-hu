---
title: A Azure Functions-futtatókörnyezet hibáinak megoldása nem érhető el.
description: Ismerje meg, hogyan lehet elhárítani egy érvénytelen Storage-fiókot.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963886"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>A "functions Runtime nem érhető el" hibáinak megoldása

Ez a cikk a "functions Runtime nem érhető el" hibaüzenetet jeleníti meg, amikor megjelenik a Azure Portal. Ha ez a hiba történik, a portálon megjelenik a következő hibaüzenet.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Ez akkor fordul elő, ha a Azure Functions-futtatókörnyezet nem indítható el. Ennek a hibának a leggyakoribb oka az, hogy a Function alkalmazás elveszíti a hozzáférést a Storage-fiókjához. További információ: a [Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

A cikk további részében a hiba következő okának elhárítása segít, beleértve az egyes esetek azonosítását és megoldását.

+ [A tárfiókot törölték](#storage-account-deleted)
+ [A tárfiók alkalmazásbeállításait törölték](#storage-account-application-settings-deleted)
+ [A tárfiók hitelesítő adatai érvénytelenek](#storage-account-credentials-invalid)
+ [A tárfiók nem érhető el](#storage-account-inaccessible)
+ [A napi végrehajtási kvóta túllépve](#daily-execution-quota-full)
+ [Az alkalmazás tűzfal mögött van](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Storage-fiók törölve

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törölve lett, a függvény nem fog működni.

### <a name="how-to-find-your-storage-account"></a>A Storage-fiók megkeresése

Első lépésként tekintse meg a Storage-fiók nevét az alkalmazás beállításaiban. `AzureWebJobsStorage` vagy `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` tartalmazza a Storage-fiók nevét, amely egy kapcsolatok karakterláncában van becsomagolva. További részletekért tekintse meg az [Alkalmazásbeállítások hivatkozását](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Keresse meg a Storage-fiókját a Azure Portalban, és ellenőrizze, hogy még létezik-e. Ha törölték, újra létre kell hoznia egy Storage-fiókot, és cserélnie kell a Storage-kapcsolódási karakterláncokat. A függvény kódja elveszett, és újra kell telepíteni újra.

## <a name="storage-account-application-settings-deleted"></a>A Storage-fiók alkalmazásának beállításai törölve

Az előző lépésben, ha nem rendelkezik a Storage-fiókhoz tartozó kapcsolatok karakterláncával, valószínűleg törölték vagy felülírták. Az Alkalmazásbeállítások törlésére leggyakrabban az üzembehelyezési pontok használatakor vagy Azure Resource Manager parancsfájlokban van szükség az alkalmazás beállításainak megadásához.

### <a name="required-application-settings"></a>Szükséges Alkalmazásbeállítások

* Szükséges
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* A felhasználási terv funkcióihoz szükséges
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Az Alkalmazásbeállítások itt olvashatók](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Segédletek

* Ezeknél a beállításoknál ne a "tárolóhely beállítása" lehetőséget. Az üzembe helyezési pontok cseréjekor a Function alkalmazás megszakad.
* Ne módosítsa ezeket a beállításokat az automatikus telepítések részeként.
* Ezeket a beállításokat meg kell adni, és érvényesnek kell lenniük a létrehozáskor. Egy automatikus központi telepítés, amely nem tartalmazza ezeket a beállításokat, a függvény alkalmazás nem fog futni, még akkor sem, ha később hozzáadják a beállításokat.

## <a name="storage-account-credentials-invalid"></a>A Storage-fiók hitelesítő adatai érvénytelenek

A tárolási kulcsok újragenerálása esetén frissíteni kell a fenti Storage-fiókhoz tartozó kapcsolatok karakterláncait. [További információ a Storage Key managementről](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>A Storage-fiók nem érhető el

A Function alkalmazásnak képesnek kell lennie hozzáférni a Storage-fiókhoz. A Storage-fiókhoz való hozzáférést blokkoló gyakori problémák a következők:

+ a megfelelő hálózati szabályok nélkül központilag telepített App Service környezetekre (bevezetésre) vonatkozó alkalmazásokat a Storage-fiókba irányuló és onnan érkező forgalom engedélyezéséhez.

+ A Storage-fiók tűzfala engedélyezve van, és nincs konfigurálva a függvények felé irányuló és onnan érkező forgalom engedélyezéséhez. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Napi végrehajtási kvóta megtelt

Ha a napi végrehajtási kvóta konfigurálva van, a Function alkalmazás átmenetileg le lesz tiltva, ami miatt a portál számos vezérlője elérhetetlenné válik. 

+ A [Azure Portalban](https://portal.azure.com)való ellenőrzéshez nyissa meg a **Platform funkcióit** > **függvényalkalmazás beállításait** a Function alkalmazásban. Amikor a beállított **napi használati kvótát** használja, a következő üzenet jelenik meg:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ A probléma megoldásához távolítsa el vagy növelje meg a napi kvótát, és indítsa újra az alkalmazást. Ellenkező esetben az alkalmazás végrehajtása a következő napig le lesz tiltva.

## <a name="app-is-behind-a-firewall"></a>Az alkalmazás tűzfal mögött van

A függvény futtatókörnyezete nem érhető el, ha a Function alkalmazást [belső terheléselosztású app Service Environment](../app-service/environment/create-ilb-ase.md) üzemelteti, és úgy van konfigurálva, hogy blokkolja a bejövő internetes forgalmat, vagy [bejövő IP-korlátozásokat](functions-networking-options.md#inbound-ip-restrictions) konfigurál az Internet-hozzáférés blokkolására. A Azure Portal közvetlenül a futó alkalmazásban hívja meg a függvények listáját, és HTTP-hívásokat kezdeményez a KUDU-végponthoz. A platform szintjének beállításai a `Platform Features` lapon továbbra is elérhetők lesznek.

A bevezető konfiguráció ellenőrzéséhez navigáljon annak az alhálózatnak a NSG, ahol a központilag található, és ellenőrizze a bejövő szabályokat, hogy engedélyezzék az alkalmazáshoz hozzáférő számítógép nyilvános IP-címéről érkező forgalmat. A portált egy olyan számítógépről is használhatja, amely az alkalmazást futtató virtuális hálózathoz vagy a virtuális hálózatban futó virtuális géphez van csatlakoztatva. [További információ a bejövő szabályok konfigurálásáról](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Következő lépések

Ismerje meg a Function apps figyelését:

> [!div class="nextstepaction"]
> [Figyelő Azure Functions](functions-monitoring.md)
