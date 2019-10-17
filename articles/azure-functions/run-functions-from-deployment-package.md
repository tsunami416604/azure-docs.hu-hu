---
title: Azure Functions futtatása csomagból | Microsoft Docs
description: A Azure Functions futtatókörnyezettel futtassa a függvényeket a Function app-projektfájlok fájljait tartalmazó központi telepítési csomagfájl csatlakoztatásával.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 549768473460dcb97b66c3589d71c02039220605
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389949"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions futtatása csomagfájl

Az Azure-ban a függvényeket közvetlenül egy központi telepítési csomagból is futtathatja a Function alkalmazásban. A másik lehetőség, hogy telepíti a fájlokat a Function app `d:\home\site\wwwroot` könyvtárába.

Ez a cikk a függvények csomagból való futtatásának előnyeit ismerteti. Azt is bemutatja, hogyan engedélyezheti ezt a funkciót a Function alkalmazásban.

> [!IMPORTANT]
> Ha egy [prémium](functions-scale.md#premium-plan)szintű csomagban telepíti a függvényeket egy Linux-függvény alkalmazásba, mindig a csomagfájl alapján kell futtatnia, és [az alkalmazást a Azure functions Core Tools használatával kell közzétennie](functions-run-local.md#project-file-deployment).

## <a name="benefits-of-running-from-a-package-file"></a>A csomagfájl futtatásának előnyei
  
A csomagfájl számos előnnyel jár:

+ Csökkenti a fájlmásolás-zárolási problémák kockázatát.
+ Üzembe helyezhető egy éles alkalmazásban (újraindítással).
+ Biztos lehet benne, hogy az alkalmazásban futó fájlok vannak.
+ Javítja Azure Resource Manager üzemelő [példányok](functions-infrastructure-as-code.md)teljesítményét.
+ Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

További információkért tekintse meg [ezt a bejelentést](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>A függvények csomagból való futtatásának engedélyezése

Ha engedélyezni szeretné, hogy a Function app egy csomagból fusson, csak `WEBSITE_RUN_FROM_PACKAGE` beállítást adjon hozzá a Function app-beállításokhoz. A `WEBSITE_RUN_FROM_PACKAGE` beállítás a következő értékek egyike lehet:

| Value (Díj)  | Leírás  |
|---------|---------|
| **`1`**  | A Windows rendszeren futó Function apps esetében ajánlott. Futtasson egy Package fájlból a Function alkalmazás `d:\home\data\SitePackages` mappájából. Ha nem [telepíti a zip-telepítést](#integration-with-zip-deployment), ez a beállítás azt igényli, hogy a mappához `packagename.txt` nevű fájl is tartozhat. Ez a fájl csak a mappában lévő csomagfájl nevét tartalmazza szóköz nélkül. |
|**`<url>`**  | A futtatni kívánt csomag adott fájljának helye. A blob Storage használatakor egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, hogy a functions futtatókörnyezet hozzáférhessen a csomaghoz. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába.         |

> [!CAUTION]
> Ha Windows rendszeren futtat egy Function alkalmazást, a külső URL-cím beállításával rosszabb az indítási teljesítmény. A Function-alkalmazás Windows rendszeren való telepítésekor a `WEBSITE_RUN_FROM_PACKAGE` értéket `1` értékre kell beállítania, és közzé kell tennie a zip-telepítéssel.

Az alábbi példa egy, az Azure Blob Storage-ban üzemeltetett. zip-fájlból való futtatásra konfigurált Function-alkalmazást mutat be:

![WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a. zip csomagfájl támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip-telepítéssel

A [zip-telepítés][Zip deployment for Azure Functions] a Azure app Service egyik funkciója, amely lehetővé teszi a Function app-projekt üzembe helyezését a `wwwroot` könyvtárba. A projekt. zip telepítési fájlként van csomagolva. Ugyanezek az API-k használhatók a csomag `d:\home\data\SitePackages` mappába való üzembe helyezéséhez is. Az `1` `WEBSITE_RUN_FROM_PACKAGE` Alkalmazásbeállítások értékével a zip telepítési API-k a `d:\home\data\SitePackages` mappába másolják a csomagot ahelyett, hogy a fájlokat a `d:\home\site\wwwroot` fájlba kicsomagoljuk. Emellett létrehozza a `packagename.txt` fájlt is. Az újraindítás után a csomag írásvédett fájlrendszerként `wwwroot`-ra van csatlakoztatva. További információ a zip-telepítésről: [a Azure functions zip központi telepítése](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>A WEBSITE_RUN_FROM_PACKAGE-beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Hibakeresés

- A csomagból való futtatás @no__t – 0 írásvédett, így hibaüzenetet kap, amikor fájlokat ír a könyvtárba.
- A tar és a gzip formátum nem támogatott.
- Ez a funkció nem a helyi gyorsítótárral együtt működik.
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást (`WEBSITE_RUN_FROM_PACKAGE` = 1).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
