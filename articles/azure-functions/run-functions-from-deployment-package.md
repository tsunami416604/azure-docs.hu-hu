---
title: Azure Functions futtatása csomagból | Microsoft Docs
description: A Azure Functions futtatókörnyezettel futtassa a függvényeket a Function app-projektfájlok fájljait tartalmazó központi telepítési csomagfájl csatlakoztatásával.
services: functions
documentationcenter: na
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: b6a2347ff79268cdaf54993952d59bd700b781bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095964"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Functions futtatása csomagfájl

Az Azure-ban a függvényeket közvetlenül egy központi telepítési csomagból is futtathatja a Function alkalmazásban. A másik lehetőség, hogy a fájlokat `d:\home\site\wwwroot` a Function alkalmazás könyvtárába telepíti.

Ez a cikk a függvények csomagból való futtatásának előnyeit ismerteti. Azt is bemutatja, hogyan engedélyezheti ezt a funkciót a Function alkalmazásban.

> [!IMPORTANT]
> Ha egy [prémium](functions-scale.md#premium-plan)szintű csomagban telepíti a függvényeket egy Linux-függvény alkalmazásba, mindig a csomagfájl alapján kell futtatnia, és [az alkalmazást a Azure functions Core Tools használatával](functions-run-local.md#project-file-deployment)kell közzétennie.

## <a name="benefits-of-running-from-a-package-file"></a>A csomagfájl futtatásának előnyei
  
A csomagfájl számos előnnyel jár:

+ Csökkenti a fájlmásolás-zárolási problémák kockázatát.
+ Üzembe helyezhető egy éles alkalmazásban (újraindítással).
+ Biztos lehet benne, hogy az alkalmazásban futó fájlok vannak.
+ Javítja Azure Resource Manager üzemelő [példányok](functions-infrastructure-as-code.md)teljesítményét.
+ Csökkentheti a hideg kezdési időpontokat, különösen a JavaScript-függvények esetében nagyméretű NPM-csomagokkal.

További információkért tekintse meg [ezt](https://github.com/Azure/app-service-announcements/issues/84)a bejelentést.

## <a name="enabling-functions-to-run-from-a-package"></a>A függvények csomagból való futtatásának engedélyezése

Ha engedélyezni szeretné, hogy a Function alkalmazás egy csomagból fusson, egyszerűen `WEBSITE_RUN_FROM_PACKAGE` adjon hozzá egy beállítást a Function app-beállításokhoz. A `WEBSITE_RUN_FROM_PACKAGE` beállítás a következő értékek egyike lehet:

| Value  | Leírás  |
|---------|---------|
| **`1`**  | A Windows rendszeren futó Function apps esetében ajánlott. Futtasson egy Package fájlból a `d:\home\data\SitePackages` Function alkalmazás mappájából. Ha nem [telepíti a zip-telepítést](#integration-with-zip-deployment), ez a beállítás azt igényli, hogy a mappában is legyen `packagename.txt`egy nevű fájl. Ez a fájl csak a mappában lévő csomagfájl nevét tartalmazza szóköz nélkül. |
|**`<url>`**  | A futtatni kívánt csomag adott fájljának helye. A blob Storage használatakor egy [megosztott hozzáférési aláírással (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) rendelkező privát tárolót kell használnia, hogy a functions futtatókörnyezet hozzáférhessen a csomaghoz. A [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) használatával tölthet fel csomagokat a blob Storage-fiókjába.         |

> [!CAUTION]
> Ha Windows rendszeren futtat egy Function alkalmazást, a külső URL-cím beállításával rosszabb az indítási teljesítmény. Ha a Function alkalmazást a Windows rendszerre telepíti, be kell `WEBSITE_RUN_FROM_PACKAGE` állítania `1` és közzé kell tennie a zip-telepítéssel.

Az alábbi példa egy, az Azure Blob Storage-ban üzemeltetett. zip-fájlból való futtatásra konfigurált Function-alkalmazást mutat be:

![WEBSITE_RUN_FROM_ZIP](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Jelenleg csak a. zip csomagfájl támogatottak.

## <a name="integration-with-zip-deployment"></a>Integráció a zip-telepítéssel

A [zip-telepítés][Zip deployment for Azure Functions] a Azure app Service szolgáltatása, amely lehetővé teszi a Function app-projekt üzembe `wwwroot` helyezését a címtárban. A projekt. zip telepítési fájlként van csomagolva. Ugyanazokat az API-kat használhatja a csomag `d:\home\data\SitePackages` telepítéséhez a mappába. Az Alkalmazásbeállítások `d:\home\data\SitePackages` `d:\home\site\wwwroot`értékeként a zip -telepítésiAPI-kafájlokmappábamásoljákacsomagot.`1` `WEBSITE_RUN_FROM_PACKAGE` Létrehozza a `packagename.txt` fájlt is. A Function alkalmazás ezután a csomagból indul újra, és `wwwroot` csak olvasható lesz. További információ a zip-telepítésről: [a Azure functions zip központi telepítése](deployment-zip-push.md).

## <a name="adding-the-website_run_from_package-setting"></a>A WEBSITE_RUN_FROM_PACKAGE-beállítás hozzáadása

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

- A Futtatás a csomagból `wwwroot` csak olvasható, így hibaüzenetet kap, amikor fájlokat ír a könyvtárba.
- A tar és a gzip formátum nem támogatott.
- Ez a funkció nem a helyi gyorsítótárral együtt működik.
- A jobb hidegindító teljesítmény érdekében használja a helyi zip-beállítást (`WEBSITE_RUN_FROM_PACKAGE`= 1).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
