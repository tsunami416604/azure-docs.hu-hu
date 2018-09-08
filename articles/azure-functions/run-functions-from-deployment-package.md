---
title: Egy csomag az Azure Functions futtatása |} A Microsoft Docs
description: Az Azure Functions futtatókörnyezettel futtathatja a függvényeit csatlakoztatja a központi telepítési csomag fájl tartalmaz, a függvény alkalmazás soubory projektu rendelkezik.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: a3c42dc0f90b16b14eca2c47608fb90238dd0f72
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095470"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Az Azure Functions futtatása egy csomagfájlt

> [!NOTE]
> A cikkben leírt funkciók jelenleg előzetes verziójú; nem érhető Functions Linux rendszeren.

Az Azure-ban a függvények futtathatja közvetlenül a központi telepítési csomag fájlból a függvényalkalmazásban. A másik lehetőség, hogy a függvény projektfájlokat a telepíti a `d:\home\site\wwwroot` könyvtárát a függvényalkalmazást.

Ez a cikk ismerteti a csomagból a függvények futtatásának előnyeit. Azt is bemutatja, hogyan engedélyezi ezt a funkciót a függvényalkalmazásban.

## <a name="benefits-of-running-from-a-package-file"></a>Az alkalmazáscsomag-fájl futtatásának előnyeit
  
Az alkalmazáscsomag-fájl futtatásával több előnye is van:

+ Csökkenti a fájlmásolás zárolás problémák kockázatát.
+ (Az újraindítás) éles alkalmazások telepíthetők.
+ Bizonyos is lehet, hogy az alkalmazás fut fájlok.
+ Növeli a teljesítményt [Azure Resource Manager üzembe helyezések](functions-infrastructure-as-code.md).
+ Előfordulhat, hogy csökkentse a JavaScript-függvények hidegindítási idején.

További információkért lásd: [bejelentéssel](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Futtatható egy csomagot a funkciók engedélyezése

Ahhoz, hogy a függvényalkalmazás futtatásához egy csomagból, egyszerűen hozzáadhat egy `WEBSITE_RUN_FROM_ZIP` beállítása a függvényalkalmazás beállításait. A `WEBSITE_RUN_FROM_ZIP` beállítás a következő értékek egyike lehet:

| Érték  | Leírás  |
|---------|---------|
|**`<url>`**  | Szeretne futtatni egy adott csomag-fájl helyét. A Blob storage használata esetén használjon egy privát tároló egy [közös hozzáférésű Jogosultságkód (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) ahhoz, hogy a Functions futtatókörnyezete eléréséhez a csomaghoz. Használhatja a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) csomag fájlok feltöltése a Blob storage-fiók.         |
| **`1`**  | Futtassa a csomag-fájlból a `d:\home\data\SitePackages` mappában található a függvényalkalmazást. Ez utóbbi lehetőség megköveteli a mappát, rendelkeznie kell egy fájlt `packagename.txt`. Ez a fájl csak a mappában, anélkül, hogy minden szóközt az alkalmazáscsomag-fájl nevét tartalmazza. |

Az alábbiakban látható egy függvényalkalmazást az Azure Blob storage-ban üzemeltetett .zip-fájlként való futtatásra konfigurálva:

![WEBSITE_RUN_FROM_ZIP Alkalmazásbeállítás](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a .zip csomag fájlok is támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip-telepítés

[Üzembe helyezés zip] [ Zip deployment for Azure Functions] Azure App Service-ben, amely lehetővé teszi a függvényalkalmazás projektjét, szolgáltatása a `wwwroot` könyvtár. A projekt üzembe helyezési .zip fájlként van csomagolva. Az API-kkal segítségével helyezze üzembe a csomagot, hogy a `d:\home\data\SitePackages` mappát. Az a `WEBSITE_RUN_FROM_ZIP` alkalmazás beállítás értékét `1`, a zip-üzembe helyezési API-k másolja a csomagot a `d:\home\data\SitePackages` ahelyett, hogy a fájlok kibontása `d:\home\site\wwwroot`. Azt is létrehoz a `packagename.txt` fájlt. A függvényalkalmazás van, majd futtassa a csomag egy újraindítás után, és `wwwroot` csak olvashatóvá válik. Zip fejlesztésével kapcsolatos további információkért lásd: [Azure Functions üzembe helyezés Zip](deployment-zip-push.md).

## <a name="adding-the-websiterunfromzip-setting"></a>A WEBSITE_RUN_FROM_ZIP beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
