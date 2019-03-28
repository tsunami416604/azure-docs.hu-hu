---
title: Hogyan háríthatók el az Azure Functions Runtime nem érhető el.
description: Ismerje meg, hogyan háríthatók el egy érvénytelen tárfiók.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521720"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Hogyan háríthatók el a "a functions futtatókörnyezete nem érhető el"


## <a name="error-text"></a>Hiba szövege
Ezt a dokumentumot célja, ha a Functions portálon jelenik meg a következő hiba elhárítása.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Összegzés
Ez a probléma akkor fordul elő, amikor az Azure Functions Runtime nem indítható el. Ez a hiba fordul elő a leggyakoribb oka a függvényalkalmazás a tárfiókhoz való hozzáférés elvesztése. [További információ a tárolási fiók követelményeit Itt](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Hibaelhárítás
Végigvezetjük a négy leggyakoribb hibák azonosítása és megoldása minden esetben.

1. Storage-fiók törölve
1. Tárfiók alkalmazás beállításainak törlése
1. Tárfiók hitelesítő adatainak érvénytelen
1. A Tárfiók nem érhető el
1. Napi végrehajtási kvóta teljes

## <a name="storage-account-deleted"></a>Storage-fiók törölve

Minden függvényalkalmazáshoz szükség van egy storage-fiókot a művelethez használandó. Ha törli a fiókot, a függvény nem fog működni.

### <a name="how-to-find-your-storage-account"></a>A tárfiók megkeresése

Indítsa el a tárfiók nevét az alkalmazás beállításaiban keresésekor. Akár `AzureWebJobsStorage` vagy `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` burkolja egy kapcsolati karakterláncot a tárfiók nevét fogja tartalmazni. Olvassa el a további részletekről az [alkalmazás beállítás-referenciában találhat](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Keresse meg a storage-fiókot az Azure Portalon tekintheti meg, ha még létezik. Ha törölték, hozza létre újból a tárfiókot, és cserélje le a tárolási kapcsolatok karakterláncainak kell. A függvénykód elvész, és újra telepíteni kell.

## <a name="storage-account-application-settings-deleted"></a>Tárfiók alkalmazás beállításainak törlése

Az előző lépésben Ha Ön egy tárfiók kapcsolati sztringje nem rendelkezett voltak valószínűleg törölték, vagy felülírja. Alkalmazásbeállítások törlése leggyakrabban történik, állítsa be az alkalmazásbeállításokat az üzembe helyezési pontok vagy az Azure Resource Manager-parancsfájlok használata esetén.

### <a name="required-application-settings"></a>Szükséges alkalmazás beállításai

* Szükséges
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Használatalapú csomag funkciók szükséges
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Olvassa el ezeket az alkalmazásbeállításokat Itt](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Útmutatás

* Ellenőrzi az ezen beállítások bármelyike "tárhelybeállítás". Ha Ön üzembehelyezési pontok cseréje a függvény megszakadnak.
* Ne módosítsa ezeket a beállításokat, az automatikus központi telepítések részeként.
* Ezek a beállítások a létrehozáskor megadott és érvényes kell lennie. Egy automatikus központi telepítési, amely nem tartalmazza ezeket a beállításokat nem működőképes alkalmazást, azt eredményezi, még akkor is, ha a beállításokat utólag kerülnek.

## <a name="storage-account-credentials-invalid"></a>Tárfiók hitelesítő adatainak érvénytelen

A fenti Tárfiók kapcsolati karakterláncokat kell frissíteni, ha, a tárelérési kulcsok újragenerálása. [További információ a tárolási kulcskezelés Itt](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>A tárfiók nem érhető el

A Függvényalkalmazás kell tudni hozzáférni a tárfiókhoz. Gyakori problémák, amelyek blokkolják a hozzáférést egy tárfiókhoz funkciók a következők:

* Függvényalkalmazások üzembe helyezett App Service Environment anélkül, hogy a megfelelő hálózati szabályok engedélyezi az adatforgalmat, és a storage-fiókból
* A storage-fiók tűzfal engedélyezve van, és nincs konfigurálva, hogy a funkciók és kimenő forgalma. [További információ a tárfiók tűzfal konfigurálása ide](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Napi végrehajtási kvóta teljes

Ha egy napi végrehajtási kvóta beállítva, a Függvényalkalmazás ideiglenesen le lesz tiltva, és számos, a portál-vezérlőelemek nem lesz elérhető. 

* Győződjön meg arról, hogy ellenőrizze nyissa meg a Platform szolgáltatásai > Függvényalkalmazás-beállításokat a portálon. A következő üzenet akkor jelenik meg, ha túllépte a kvótát
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Távolítsa el a kvótát, és indítsa újra az alkalmazást a probléma megoldásához.

## <a name="next-steps"></a>További lépések

Most, hogy a Függvényalkalmazás vissza és működési tekintse meg a gyors útmutatóink és fejlesztői történő használatának, és újra futtatni a!

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
