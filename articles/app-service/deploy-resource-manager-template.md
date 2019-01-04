---
title: Útmutató a sablonok – az Azure App Service-alkalmazások telepítése |} A Microsoft Docs
description: Javaslatok webalkalmazások telepítése Azure Resource Manager-sablonok létrehozására.
services: app-service
documentationcenter: app-service
author: tfitzmac
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1431ba658a6eb898553804f0c81b3babb23f4fe2
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015202"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Útmutatás a webes alkalmazások üzembe helyezése Azure Resource Manager-sablonok használatával

Ez a cikk az Azure App Service-megoldások üzembe helyezése Azure Resource Manager-sablonok létrehozására vonatkozó javaslatokat nyújt. Ezek a javaslatok segíthetnek a gyakori problémák elkerülése érdekében.

## <a name="define-dependencies"></a>Függőségek megadása

A web apps függőségek meghatározása egy adott webalkalmazáson belül az erőforrásokat együttműködését ismeretét igényli. Helytelen megrendelés függőségek ad meg, ha, telepítési hibák vezethet, vagy hozzon létre, amely a központi telepítés megáll versenyhelyzet.

> [!WARNING]
> Ha egy MSDeploy webhelybővítmény adja meg a sablonban, be kell konfigurációs erőforrásokat, az MSDeploy erőforrástól függ-e. Konfigurációs módosítások miatt a helyet, hogy indítsa újra a aszinkron módon történik. Azáltal, hogy a konfigurációs erőforrásokat MSDeploy függ, akkor győződjön meg arról, hogy MSDeploy befejezését, mielőtt a hely újraindul. A függőségek nélkül az a hely MSDeploy központi telepítési folyamata során újraindulhat. Egy példa a sablonhoz, lásd: [WordPress webalkalmazás üzembe helyezése függőségi sablon](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Az alábbi képen látható a különféle App Service-erőforrások esetében a függőségi sorrend:

![Webes alkalmazás függőségei](media/web-sites-rm-template-guidance/web-dependencies.png)

A következő sorrendben erőforrások központi telepítése:

**1. szint**
* App Service-csomag.
* Bármely egyéb kapcsolódó erőforrásokat, például adatbázisok és a storage-fiókok.

**2. szintű**
* Web app – az App Service-csomag függ.
* Az Azure Application Insights-példányt, amely a kiszolgálófarm--célozza az App Service-csomag függ.

**3. szint**
* Verziókövetés – a web app függ.
* MSDeploy-webhelybővítményt – a web app függ.
* Application Insights-példány a kiszolgálófarm--célzó attól függ, hogy a webalkalmazás.

**4. réteg**
* App Service-tanúsítvány – függ, hogy a verziókövetés, vagy MSDeploy, ha jelen. Ellenkező esetben a webes alkalmazás függ.
* Konfigurációs beállítások (kapcsolati karakterláncok, web.config értékek, Alkalmazásbeállítások) – függ verziókövetés vagy MSDeploy amennyiben vagy telepítve. Ellenkező esetben a webes alkalmazás függ.

**5. szint**
* Gazdagép neve kötések--függ, hogy a tanúsítványt, ha van ilyen. Ellenkező esetben attól függ, a magasabb szintű erőforrás.
* Webhelybővítményekkel – ha van ilyen konfigurációs beállításoktól függ. Ellenkező esetben attól függ, a magasabb szintű erőforrás.

A megoldás általában csak néhány, az erőforrások és rétegek tartalmaz. Hiányzó szinten képezze le az eggyel magasabb szint alacsonyabb erőforrásokat.

Az alábbi példa bemutatja egy sablon részét. A konfiguráció a kapcsolati karakterlánc értékét az MSDeploy bővítmény függ. Az MSDeploy bővítmény attól függ, a webalkalmazáshoz és adatbázishoz. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

A fenti kódot használó alkalmazást kész minta: [sablon: Egyszerű Umbraco-webalkalmazás összeállítása](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple).

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy-hibákkal kapcsolatos információk keresése

Ha a Resource Manager-sablon MSDeploy használ, a központi telepítési hibaüzenetek nehezen érthető lehet. További információ a sikertelen üzembe helyezés után próbálja meg az alábbi lépéseket:

1. Nyissa meg a hely [Kudu konzol](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Keresse meg a D:\home\LogFiles\SiteExtensions\MSDeploy mappában.
3. Keresse meg a appManagerStatus.xml és appManagerLog.xml fájlokat. Az első fájl állapota naplózza. A második fájl a hibával kapcsolatos információkat naplózza. Ha a hiba nem egyértelmű, hogy, megadhatja azt, még a fórum a Súgó kérése során.

## <a name="choose-a-unique-web-app-name"></a>Válassza ki a webalkalmazás egyedi neve

A név a webalkalmazás globálisan egyedinek kell lennie. Használhat egy elnevezési konvenciója, valószínű, hogy egyedi legyen, vagy használhatja a [uniqueString függvény](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) , amelyek segítik a létrehozni egy egyedi nevet.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Webalkalmazás-tanúsítvány a Key Vault üzembe helyezése

Ha a sablon tartalmaz egy [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) , amely SSL-kötés és a tanúsítvány a Key Vaultban tárolt, meg kell győződnie arról, hogy az App Service identity is hozzáférjen a tanúsítványhoz.

A globális Azure-ban, az App Service-ben egyszerű szolgáltatás azonosítója rendelkezik **abfa0a7c-a6b6-4736-8310-5855508787cd**. A Key Vault hozzáférési jogot az App Service szolgáltatás egyszerű, használja:

```azurepowershell-interactive
Set-AzureRmKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Az Azure Government, az App Service-ben egyszerű szolgáltatás azonosítója rendelkezik **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Ez az előző példában használja.

Válassza ki a Key Vault **tanúsítványok** és **létrehozás/importálás** feltölteni a tanúsítványt.

![Tanúsítvány importálása](media/web-sites-rm-template-guidance/import-certificate.png)

A sablonban, adja meg a tanúsítvány nevét a `keyVaultSecretName`.

Egy példa a sablonhoz, lásd: [üzembe helyezése a webalkalmazásban tanúsítványt a Key Vault titkos kulcsából, és SSL-kötés létrehozásához használhatja](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault).

## <a name="next-steps"></a>További lépések

* Webalkalmazások sablonnal oktatóanyagért lásd: [kiépítése és kiszámítható módon az Azure mikroszolgáltatások üzembe helyezéséhez](deploy-complex-application-predictably.md).
* JSON-szintaxist és a sablonok erőforrástípusok tulajdonságaival kapcsolatos tudnivalókért lásd: [Azure Resource Manager sablonreferenciája](/azure/templates/).