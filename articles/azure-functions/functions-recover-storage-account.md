---
title: A Azure Functions-futtatókörnyezet hibáinak megoldása nem érhető el.
description: Ismerje meg, hogyan lehet elhárítani egy érvénytelen Storage-fiókot.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 358f26af8d990d29f226978387fdf8093d2b8644
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612972"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>A "functions Runtime nem érhető el" hibáinak megoldása


## <a name="error-text"></a>Hiba szövege
Ez a dokumentum a functions portálon megjelenő következő hiba megoldására szolgál.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Összefoglalás
Ez a probléma akkor fordul elő, ha a Azure Functions-futtatókörnyezet nem indítható el. Ennek a hibának a leggyakoribb oka az, hogy a Function alkalmazás elveszíti a hozzáférést a Storage-fiókjához. [A Storage-fiókra vonatkozó követelményekről itt olvashat bővebben](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Hibaelhárítás
Elsajátítjuk a négy leggyakoribb hibát, az egyes esetek azonosítását és megoldását.

1. Storage-fiók törölve
1. A Storage-fiók alkalmazásának beállításai törölve
1. A Storage-fiók hitelesítő adatai érvénytelenek
1. A Storage-fiók nem érhető el
1. Napi végrehajtási kvóta megtelt
1. Az alkalmazás tűzfal mögött van


## <a name="storage-account-deleted"></a>Storage-fiók törölve

Minden Function alkalmazás működéséhez szükség van egy Storage-fiókra. Ha a fiók törölve lett, a függvény nem fog működni.

### <a name="how-to-find-your-storage-account"></a>A Storage-fiók megkeresése

Első lépésként tekintse meg a Storage-fiók nevét az alkalmazás beállításaiban. `AzureWebJobsStorage` vagy `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` tartalmazza a Storage-fiók nevét, amely egy kapcsolatok karakterláncában van becsomagolva. További részletek az [Alkalmazásbeállítások referenciájában itt](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage) olvashatók

Keresse meg a Storage-fiókját a Azure Portalban, és ellenőrizze, hogy még létezik-e. Ha törölték, újra létre kell hoznia egy Storage-fiókot, és cserélnie kell a Storage-kapcsolódási karakterláncokat. A függvény kódja elveszett, és újra kell telepíteni újra.

## <a name="storage-account-application-settings-deleted"></a>A Storage-fiók alkalmazásának beállításai törölve

Az előző lépésben, ha nem rendelkezik a Storage-fiókhoz tartozó kapcsolatok karakterláncával, valószínűleg törölték vagy felülírták őket. Az Alkalmazásbeállítások törlésére leggyakrabban az üzembehelyezési pontok használatakor vagy Azure Resource Manager parancsfájlokban van szükség az alkalmazás beállításainak megadásához.

### <a name="required-application-settings"></a>Szükséges Alkalmazásbeállítások

* Szükséges
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* A felhasználási terv funkcióihoz szükséges
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Az Alkalmazásbeállítások itt olvashatók](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Segédletek

* Ezen beállítások bármelyike esetében ne a "tárolóhely beállítása" beállítást. Az üzembe helyezési pontok cseréjekor a függvény megtöri.
* Ezeket a beállításokat ne módosítsa automatikus központi telepítések részeként.
* Ezeket a beállításokat meg kell adni, és érvényesnek kell lenniük a létrehozáskor. Egy automatikus központi telepítés, amely nem tartalmazza ezeket a beállításokat, nem működőképes alkalmazást eredményez, még akkor is, ha a beállításokat a tény után adja hozzá a rendszer.

## <a name="storage-account-credentials-invalid"></a>A Storage-fiók hitelesítő adatai érvénytelenek

A tárolási kulcsok újragenerálása esetén frissíteni kell a fenti Storage-fiókhoz tartozó kapcsolatok karakterláncait. [További információ a Storage Key managementről](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>A Storage-fiók nem érhető el

A függvényalkalmazásnak képesnek kell lennie hozzáférni a Storage-fiókhoz. A Storage-fiókhoz való hozzáférést blokkoló gyakori problémák a következők:

* A App Service környezetekben üzembe helyezett alkalmazások a megfelelő hálózati szabályok nélkül, a Storage-fiók felé irányuló és onnan érkező forgalom engedélyezéséhez.
* A Storage-fiók tűzfala engedélyezve van, és nincs konfigurálva a függvények felé irányuló és onnan érkező forgalom engedélyezéséhez. [További információ a Storage-fiók tűzfalának konfigurálásáról](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Napi végrehajtási kvóta megtelt

Ha a napi végrehajtási kvóta konfigurálva van, a függvényalkalmazás átmenetileg le lesz tiltva, és számos portál vezérlő elérhetetlenné válik. 

* Az ellenőrzéshez jelölje be a platform szolgáltatásai > függvényalkalmazás beállítások a portálon című részt. A következő üzenet jelenik meg, ha túllépi a kvótát
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Távolítsa el a kvótát, és indítsa újra az alkalmazást a probléma megoldásához.

## <a name="app-is-behind-a-firewall"></a>Az alkalmazás tűzfal mögött van

A függvény futtatókörnyezete nem érhető el, ha a Function alkalmazást [belső terheléselosztású app Service Environment](../app-service/environment/create-ilb-ase.md) üzemelteti, és úgy van konfigurálva, hogy blokkolja a bejövő internetes forgalmat, vagy [bejövő IP-korlátozásokat](functions-networking-options.md#inbound-ip-restrictions) konfigurál az Internet-hozzáférés blokkolására. A Azure Portal közvetlenül a futó alkalmazásban hívja meg a függvények listáját, és http-hívást kezdeményez a KUDU-végponthoz. A platform szintjének beállításai a `Platform Features` lapon továbbra is elérhetők lesznek.

* A bevezetői konfiguráció ellenőrzéséhez navigáljon az alhálózathoz, ahol a NSG található, és ellenőrizze a bejövő szabályokat, hogy engedélyezze az alkalmazáshoz hozzáférő számítógép nyilvános IP-címéről érkező forgalmat. A portált egy olyan számítógépről is használhatja, amely az alkalmazást futtató virtuális hálózathoz vagy a virtuális hálózatban futó virtuális géphez van csatlakoztatva. [További információ a bejövő szabályok konfigurálásáról](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)

## <a name="next-steps"></a>Következő lépések

Most, hogy a függvényalkalmazás visszatért és működőképes, tekintse át a gyors üzembe helyezést és a fejlesztői referenciákat, amelyekkel újra megkezdheti a munkát.

* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)  
  Lásson azonnal munkához, és hozza létre az első függvényét az Azure Functions gyorsindítójával. 
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)  
  Részletesebb műszaki információkat tartalmaz az Azure Functions futtatókörnyezettel kapcsolatban, valamint segédanyagokat függvények kódolásához, illetve eseményindítók és kötések meghatározásához.
* [Az Azure Functions tesztelése](functions-test-a-function.md)  
  Különböző függvénytesztelési eszközöket és technikákat ír le.
* [Az Azure Functions méretezése](functions-scale.md)  
  Az Azure Functions szolgáltatáshoz elérhető szolgáltatáscsomagokat ismerteti, köztük a Használatalapú futtatási csomagot, és segít a megfelelő csomag kiválasztásában. 
* [További információ az Azure App Service szolgáltatásról](../app-service/overview.md)  
  Az Azure Functions az Azure App Service használatával biztosítja az olyan alapvető funkciókat, mint az üzembe helyezések, a környezeti változók és a diagnosztika. 
