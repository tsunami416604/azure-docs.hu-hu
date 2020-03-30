---
title: 'Hibaelhárítás: Az Azure Functions Runtime nem érhető el'
description: Ismerje meg, hogyan hárítható el egy érvénytelen tárfiók hibaelhárítása.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063781"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Hibaelhárítási hiba: "Az Azure Functions Runtime nem érhető el"

Ez a cikk segítséget nyújt az Azure Portalon megjelenő következő hibakarakterláncok elhárításához:

> "Hiba: Az Azure Functions Runtime nem érhető el. Kattintson ide a tárolási konfigurációval kapcsolatos részletekért."

Ez a probléma akkor fordul elő, ha az Azure Functions Runtime nem tud elindulni. A probléma leggyakoribb oka az, hogy a függvényalkalmazás elvesztette a hozzáférést a tárfiókhoz. További információ: [Storage-fiók követelmények](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

A cikk további részében elháríthatja a hiba következő okait, például az egyes esetadatok azonosítását és megoldását.

## <a name="storage-account-was-deleted"></a>A tárfiók törölve lett

Minden függvényalkalmazás működéséhez tárfiók szükséges. Ha törli a fiókot, a funkció nem fog működni.

Először keresse meg a tárfiók nevét az alkalmazás beállításaiban. Vagy `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` tartalmazza a tárfiók nevét egy kapcsolati karakterláncba csomagolva. További információ: [App settings reference for Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Keresse meg a tárfiókot az Azure Portalon, hogy még mindig létezik-e. Ha törölték, hozza létre újra a tárfiókot, és cserélje ki a tárolási kapcsolat karakterláncait. A függvénykód elvész, és újra kell telepítenie.

## <a name="storage-account-application-settings-were-deleted"></a>A tárfiók alkalmazásbeállításai törlődtek

Az előző lépésben, ha nem találja a tárfiók kapcsolati karakterlánc, valószínűleg törölték vagy felülírták. Az alkalmazásbeállítások törlése leggyakrabban akkor történik, ha üzembe helyezési tárolóhelyeket vagy Azure Resource Manager-parancsfájlokat használ az alkalmazásbeállítások beállításához.

### <a name="required-application-settings"></a>Szükséges alkalmazásbeállítások

* Szükséges:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* A felhasználási terv funkcióihoz szükséges:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

További információ: [App settings reference for Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Útmutatás

* Ne ellenőrizze a "bővítőhely beállítását" a következő beállítások egyikét sem. Ha a telepítési tárolóhelyek cseréje, a függvényalkalmazás megszakad.
* Ne módosítsa ezeket a beállításokat az automatikus központi telepítések részeként.
* Ezeket a beállításokat meg kell adni, és a létrehozás időpontjában érvényesnek kell lenniük. Egy automatikus központi telepítés, amely nem tartalmazza ezeket a beállításokat, olyan függvényalkalmazást eredményez, amely nem fog futni, még akkor is, ha a beállítások később kerülnek hozzáadásra.

## <a name="storage-account-credentials-are-invalid"></a>A tárfiók hitelesítő adatai érvénytelenek

A korábban tárgyalt tárfiók kapcsolati karakterláncait frissíteni kell a tárolókulcsok újragenerálása esetén. A tárolásikulcs-kezelésről további információt az [Azure Storage-fiók létrehozása című témakörben](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)talál.

## <a name="storage-account-is-inaccessible"></a>A tárfiók nem érhető el

A függvényalkalmazásnak hozzá kell tudnia férni a tárfiókhoz. A függvényalkalmazás tárfiókhoz való hozzáférését tiltó gyakori problémák a következők:

* A függvényalkalmazás az App Service-környezetben a megfelelő hálózati szabályok nélkül van telepítve, hogy lehetővé tegye a tárfiókba irányuló és onnan érkező forgalmat.

* A tárfiók tűzfala engedélyezve van, és nincs beállítva a függvények közötti és onnan érkező forgalom engedélyezésére. További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>A napi végrehajtási kvóta megtelt

Ha napi végrehajtási kvótával rendelkezik, a függvényalkalmazás ideiglenesen le van tiltva, ami miatt a portál vezérlők közül sok elérhetetlenné válik. 

A kvóta ellenőrzése az [Azure Portalon](https://portal.azure.com)válassza **platform funkciók** > **funkció alkalmazás beállításai** a függvényalkalmazásban. Ha túllépte a beállított **napi használati kvótát,** a következő üzenet jelenik meg:

  > "A Függvény alkalmazás elérte a napi használati kvótát, és a következő 24 órás időkeretig leállították."

A probléma megoldásához távolítsa el vagy növelje a napi kvótát, majd indítsa újra az alkalmazást. Ellenkező esetben az alkalmazás végrehajtása a következő napig le van tiltva.

## <a name="app-is-behind-a-firewall"></a>Az alkalmazás tűzfal mögött van

A függvény futásidejű lehet, hogy nem érhető el a következő okok miatt:

* A függvényalkalmazás egy [belső terheléselosztásos App Service-környezetben](../app-service/environment/create-ilb-ase.md) található, és úgy van beállítva, hogy blokkolja a bejövő internetes forgalmat.

* A függvényalkalmazás [bejövő IP-korlátozásokkal](functions-networking-options.md#inbound-ip-restrictions) rendelkezik, amelyek az internet-hozzáférés letiltására vannak konfigurálva. 

Az Azure Portal hívásokat kezdeményez közvetlenül a futó alkalmazás a függvények listájának lekéréséhez, és http-hívásokat kezdeményez a Kudu végpontra. A **Platform szolgáltatások** lapon található platformszintű beállítások továbbra is elérhetők.

Az App Service-környezet konfigurációjának ellenőrzése:
1. Nyissa meg annak az alhálózatnak a hálózati biztonsági csoportját (NSG), amelyben az App Service-környezet található.
1. Ellenőrizze a bejövő szabályokat, hogy a forgalom, amely a számítógép nyilvános IP-címéről, ahol az alkalmazás eléréséhez érkezik. 
   
A portált olyan számítógépről is használhatja, amely az alkalmazást futtató virtuális hálózathoz vagy a virtuális hálózaton futó virtuális géphez csatlakozik. 

A bejövő szabályok konfigurálásáról az [App Service-környezet hálózati szempontjaicímű hálózati szempontok](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)című szakaszában talál további információt.

## <a name="next-steps"></a>További lépések

További információ a funkcióalkalmazások figyeléséről:

> [!div class="nextstepaction"]
> [Az Azure Functions monitorozása](functions-monitoring.md)
