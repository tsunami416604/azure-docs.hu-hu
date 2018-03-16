---
title: "Azure-webalkalmazásokban telepítésének sablonokkal |} Microsoft Docs"
description: "A webes alkalmazások telepítése Azure Resource Manager-sablonok létrehozásával kapcsolatos ajánlások."
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc816bb6e95d2800d79124dfac60b55e88eaa500
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Webalkalmazások telepítésének Azure Resource Manager-sablonok segítségével

Ebben a cikkben az Azure App Service-megoldások telepítése Azure Resource Manager-sablonok létrehozásával kapcsolatos ajánlások. Ezek a javaslatok segíthetnek a gyakori problémák elkerülése érdekében.

## <a name="define-dependencies"></a>Függőségek megadása

Hogyan működnek együtt az erőforrások a webalkalmazáson belül ismeretét web Apps függőségek meghatározása szükséges. Függőségek nem megfelelő sorrendben adja meg, ha előfordulhat, hogy, telepítési hibákat okozhatnak, vagy hozzon létre, amely a központi telepítés lefagy versenyhelyzet.

> [!WARNING]
> Ha MSDeploy hely kiterjesztése a sablonban, be kell minden olyan konfiguráció erőforrásnál erőforrástól függ a MSDeploy szerint. Konfigurációs módosítások miatt a webhely újraindításához aszinkron módon. Azáltal, hogy a konfigurációs erőforrások MSDeploy függ, akkor győződjön meg arról, hogy MSDeploy befejeződése előtt a hely újraindul. A függőségek nélkül a hely újraindulhat MSDeploy telepítési folyamata során. Egy példa-sablon, lásd: [WordPress sablon webalkalmazás telepítése függőség](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A következő kép bemutatja a függőségi sorrend különböző App Service-erőforrások:

![Webes alkalmazás függőségei](media/web-sites-rm-template-guidance/web-dependencies.png)

A következő sorrendben erőforrások telepítése:

**1. rétegbeli**
* App Service-csomag.
* Bármely egyéb kapcsolódó erőforrások, például adatbázisok vagy a storage-fiókok.

**2. szintű**
* Webes alkalmazás – az App Service-csomag függ.
* Azure Application Insights-példányt, amelynek célpontja a kiszolgálófarm – az App Service-csomag függ.

**3. szint**
* Forrás-vezérlés – a webes alkalmazás függ.
* MSDeploy hely bővítmény – a webes alkalmazás függ.
* Az Application Insights példányának, amelynek célpontja a kiszolgálófarm--a webes alkalmazás függ.

**Réteg 4**
* App Service-tanúsítvány – függ a verziókövetési rendszerrel, vagy MSDeploy, ha jelen. Ellenkező esetben a webes alkalmazás függ.
* Beállítások (kapcsolati karakterláncok, web.config értékek, Alkalmazásbeállítások) – a verziókövetési rendszerrel vagy függ MSDeploy Ha vagy található. Ellenkező esetben a webes alkalmazás függ.

**Réteg 5**
* Gazdagép neve kötések--attól függ, a tanúsítványt, ha van ilyen. Ellenkező esetben egy magasabb szintű erőforrás függ.
* Bővítmények--hely konfigurációs beállításait, ha van ilyen függ. Ellenkező esetben egy magasabb szintű erőforrás függ.

A megoldás általában csak néhányat ezen erőforrások és a rétegek tartalmaz. Hiányzó rétege számára az eggyel magasabb réteg alacsonyabb erőforrások hozzárendelése.

A következő példa bemutatja, egy sablon részét. A konfiguráció a kapcsolati karakterlánc értékét a MSDeploy bővítmény függ. Az MSDeploy-bővítmény a web app és az adatbázis függ.

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy hibák kapcsolatos információk megkereséséhez

Ha a Resource Manager-sablon MSDeploy használ, a központi telepítés hibaüzenetek megértése nehéz feladat lehet. További információért a sikertelen telepítés után, kövesse az alábbi lépéseket:

1. Lépjen a webhely [Kudu konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Keresse meg azt a mappát a D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Keresse meg a appManagerStatus.xml és appManagerLog.xml fájlokat. Az első ilyen fájlt naplózza az állapotát. A második fájl a hibával kapcsolatos információkat naplózza. Ha a hiba nem egyértelmű Önnek, ha Ön most segítsége a fórumon megadhatja azt.

## <a name="choose-a-unique-web-app-name"></a>Válasszon egy egyedi webes alkalmazás neve

A webalkalmazás nevének globálisan egyedinek kell lennie. Elnevezési is használhat, amely egyedinek kell lennie, vagy használhatja a [uniqueString függvény](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) segít generálása egy egyedi nevet.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>További lépések

* A webes alkalmazásokat telepítsen a sablon oktatóanyagok esetén lásd: [kiépítése és mikroszolgáltatások kiszámítható módon tudja az Azure-ban telepítheti](app-service-deploy-complex-application-predictably.md).