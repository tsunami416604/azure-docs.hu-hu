---
title: Az Azure-függvények futtatása csomagból
description: Az Azure Functions futásidejű futtatni a funkciókat egy központi telepítési csomag fájl, amely tartalmazza a függvényalkalmazás projekt fájlokat.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: d40896d6a4659945dbeda9ca965366f0b2ca4bd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365271"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Az Azure-függvények futtatása csomagfájlból

Az Azure-ban a függvények futtathatók közvetlenül egy központi telepítési csomag fájla a függvényalkalmazásban. A másik lehetőség a fájlok `d:\home\site\wwwroot` telepítése a függvényalkalmazás könyvtárában.

Ez a cikk a függvények csomagból való futtatásának előnyeit ismerteti. Azt is bemutatja, hogyan engedélyezheti ezt a funkciót a függvényalkalmazásban.

> [!IMPORTANT]
> Amikor a funkciókat egy Linux függvényalkalmazásba helyezi egy [Prémium csomagban,](functions-scale.md#premium-plan)mindig a csomagfájlból kell futtatnia, és [közzé kell tennie az alkalmazást az Azure Functions Core Tools használatával.](functions-run-local.md#project-file-deployment)

## <a name="benefits-of-running-from-a-package-file"></a>A csomagfájlból való futtatás előnyei
  
A csomagfájlból való futtatásnak számos előnye van:

+ Csökkenti a fájlmásolási zárolási problémák kockázatát.
+ Éles alkalmazásba telepíthető (újraindítással).
+ Biztos lehet benne, hogy az alkalmazásban futó fájlok közül.
+ Javítja az [Azure Resource Manager-telepítések teljesítményét.](functions-infrastructure-as-code.md)
+ Csökkentheti a hidegindítási időt, különösen a nagy npm csomagfákkal rendelkező JavaScript függvények esetében.

További információt [ebben a közleményben](https://github.com/Azure/app-service-announcements/issues/84)talál.

## <a name="enabling-functions-to-run-from-a-package"></a>A függvények futtatásának engedélyezése csomagból

Ahhoz, hogy a függvényalkalmazás egy csomagból `WEBSITE_RUN_FROM_PACKAGE` fusson, csak adjon hozzá egy beállítást a függvényalkalmazás beállításaihoz. A `WEBSITE_RUN_FROM_PACKAGE` beállítás hoz a következő értékek egyike lehet:

| Érték  | Leírás  |
|---------|---------|
| **`1`**  | Windows rendszeren futó függvényalkalmazásokhoz ajánlott. Futtassa a függvényalkalmazás mappájában lévő `d:\home\data\SitePackages` csomagfájlból. Ha nem [a zip-telepítéssel telepíti](#integration-with-zip-deployment)a rendszert, `packagename.txt`akkor a mappának is rendelkeznie kell egy fájlnévvel. Ez a fájl csak a mappában lévő csomagfájl nevét tartalmazza, szóköz nélkül. |
|**`<URL>`**  | A futtatni kívánt csomagfájl helye. Blob storage használatakor egy [megosztott hozzáférésű aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, hogy a Functions futásidejű hozzáférést biztosítson a csomaghoz. Az Azure [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) segítségével csomagfájlokat tölthet fel a Blob storage-fiókjába. Url-cím megadásakor a frissített csomag közzététele után is szinkronizálnia kell az [eseményindítókat.](functions-deployment-technologies.md#trigger-syncing) |

> [!CAUTION]
> Ha egy függvényalkalmazást futtat Windows rendszeren, a külső URL-beállítás rosszabb hidegindítási teljesítményt eredményez. A függvényalkalmazás Windows rendszerbe való `WEBSITE_RUN_FROM_PACKAGE` telepítésekor be kell állítania és közzé kell tennie `1` a zip-telepítéssel.

Az alábbiakban egy függvényalkalmazás látható, amely az Azure Blob storage-ban üzemeltetett .zip fájlból való futtatásra van konfigurálva:

![WEBSITE_RUN_FROM_ZIP alkalmazás beállítása](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a .zip csomagfájlok támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip telepítéssel

[A zip-telepítés][Zip deployment for Azure Functions] az Azure App Service egyik szolgáltatása, `wwwroot` amely lehetővé teszi a függvényalkalmazás-projekt központi telepítését a címtárban. A projekt .zip központi telepítési fájlként van csomagolva. Ugyanazok az API-k használhatók a csomag `d:\home\data\SitePackages` mappába való központi telepítéséhez. A `WEBSITE_RUN_FROM_PACKAGE` program alkalmazásbeállítási értékével `1`a zip telepítési `d:\home\data\SitePackages` API-k a csomagot `d:\home\site\wwwroot`a mappába másolják ahelyett, hogy kibontanák a fájlokat a alkalmazásba. Ez is `packagename.txt` teremt a reszelő. Az újraindítás után a csomag `wwwroot` csak olvasható fájlrendszerként van csatlakoztatva. A zip-telepítésről további információt az [Azure Functions zip-telepítéscímű témakörben talál.](deployment-zip-push.md)

## <a name="adding-the-website_run_from_package-setting"></a>A WEBSITE_RUN_FROM_PACKAGE beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Hibaelhárítás

- A Futtatás `wwwroot` csomagból írásvédetté válik, így hibaüzenet jelenik meg, amikor fájlokat ír ebbe a könyvtárba.
- A tar és gzip formátumok nem támogatottak.
- Ez a szolgáltatás nem a helyi gyorsítótárral működik.
- A jobb hidegindítási teljesítmény érdekében használja`WEBSITE_RUN_FROM_PACKAGE`a helyi Zip opciót ( =1).
- A Futtatás csomagból nem kompatibilis`SCM_DO_BUILD_DURING_DEPLOYMENT=true`a központi telepítés testreszabási lehetőségével ( ), a buildlépés figyelmen kívül lesz hagyva a telepítés során.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
