---
title: "Azure-webalkalmazásokban sablonok telepítésének |} Microsoft Docs"
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
ms.openlocfilehash: eff0b0e6258217fa8fbf7f15606f98d70fecd7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="guidance-on-deploying-web-apps-with-azure-resource-manager-templates"></a>Webalkalmazások Azure Resource Manager-sablonok telepítésével kapcsolatos útmutatás

Ebben a cikkben az alkalmazás szolgáltatási megoldások telepítése Azure Resource Manager-sablonok létrehozásával kapcsolatos ajánlások. Ezek a javaslatok segíthetnek a gyakori problémák elkerülése érdekében.

## <a name="define-dependencies"></a>Függőségek megadása

Hogyan működnek együtt az erőforrások a webalkalmazáson belül ismeretét függőségek meghatározása a Web Apps van szükség. Függőségek nem megfelelő sorrendben adja meg, ha központi telepítési hibákat okozhatnak, vagy hozzon létre, amely a központi telepítés lefagy versenyhelyzet.

> [!WARNING]
> Ha MS Deploy hely kiterjesztése a sablonban, be kell minden olyan konfiguráció erőforrásnál erőforrástól függ a MS Deploy szerint. Konfigurációs módosítások miatt a webhely újraindításához aszinkron módon. Azáltal, hogy a konfigurációs erőforrások MS Deploy függ, akkor győződjön meg arról MS Deploy befejeződése előtt a hely újraindul. A függőségek nélkül a hely újraindulhat MS Deploy telepítési folyamat során. Egy példa-sablon, lásd: [Wordpress sablon webalkalmazás telepítése függőség](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

A következő kép bemutatja a függőségi sorrend számos App Service-erőforrás.

![Webes alkalmazás függőségei](media/web-sites-rm-template-guidance/web-dependencies.png)

A következő sorrendben erőforrások telepítése:

**1. rétegbeli**
* App Service-csomag
* Bármely egyéb kapcsolódó erőforrások, például adatbázisok vagy a storage-fiókok

**2. szintű**
* Webalkalmazás - függ az app service-csomag
* Az Application Insights a kiszolgálófarm - célzó függ az app service-csomag

**3. szint**
* Forrás-vezérlés – függ a webes alkalmazás
* MSDeploy hely bővítmény - webalkalmazás függ.
* A kiszolgálófarm - célzó az Application Insights webalkalmazás függ.

**Réteg 4**
* App Service-tanúsítvány – függ a verziókövetési rendszerrel vagy MSDeploy Ha vagy található; Ellenkező esetben függ a webes alkalmazás
* (Kapcsolati karakterláncok, webes konfigurációs értékeire, Alkalmazásbeállítások) - konfigurációs beállítások attól függ, a verziókövetési rendszerrel vagy MSDeploy Ha vagy található; Ellenkező esetben függ a webes alkalmazás

**Réteg 5**
* Gazdagép neve kötések - tanúsítványt, ha elérhető; függ Ellenkező esetben egy magasabb szintű erőforrás
* Bővítmények helyrendszer - konfigurációs beállításokat, ha elérhető; függ Ellenkező esetben egy magasabb szintű erőforrás

A megoldás általában csak néhányat ezen erőforrások és a rétegek tartalmaz. A következő magasabb réteg alacsonyabb erőforrások hozzárendelése hiányzó rétege számára.

A következő példa bemutatja, egy sablon részét. A kapcsolódási karakterlánc konfigurációs érték az MSDeploy bővítmény függ. Az MSDeploy-bővítmény a web app és az adatbázis függ.

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
2. Lépjen abba a mappába, `D:\home\LogFiles\SiteExtensions\MSDeploy`.
3. Keresse meg a *appManagerStatus.xml* és *appManagerLog.xml* fájlokat. Az első ilyen fájlt naplózza az állapotát. A második fájl a hibával kapcsolatos információkat naplózza. Ha a hiba nem egyértelmű Önnek, megadhatja azt a fórumon segítség kérése során.

## <a name="unique-web-app-name"></a>Egyedi webes alkalmazás neve

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