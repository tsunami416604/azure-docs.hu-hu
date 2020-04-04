---
title: Alkalmazások telepítése sablonokkal
description: Útmutatást talál az Azure Resource Manager-sablonok létrehozásához az App Service-alkalmazások kiépítéséhez és üzembe helyezéséhez.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 0a282a412823207e5f662441158000e8c6121796
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637928"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Útmutató a webalkalmazások Azure Resource Manager-sablonok használatával történő üzembe helyezéséhez

Ez a cikk javaslatokat tartalmaz az Azure Resource Manager-sablonok létrehozásához az Azure App Service-megoldások üzembe helyezéséhez. Ezek a javaslatok segíthetnek elkerülni a gyakori problémákat.

## <a name="define-dependencies"></a>Függőségek definiálása

A webalkalmazások függőségeinek meghatározása megköveteli annak megértését, hogy a webalkalmazáson belüli erőforrások hogyan működnek. Ha helytelen sorrendben adja meg a függőségeket, telepítési hibákat okozhat, vagy olyan versenyállapotot hozhat létre, amely leállítja a központi telepítést.

> [!WARNING]
> Ha egy MSDeploy helybővítményt tartalmaz a sablonban, akkor minden konfigurációs erőforrást az MSDeploy erőforrástól függően kell beállítania. A konfiguráció módosításai hatására a hely aszinkron módon újraindul. Azáltal, hogy a konfigurációs erőforrások az MSDeploy-től függ, győződjön meg arról, hogy az MSDeploy befejeződik a hely újraindítása előtt. Ezek nélkül a függőségek nélkül a hely újraindulhat az MSDeploy telepítési folyamata során. Például sablon, lásd: [WordPress sablon webtelepítés-függőség](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

Az alábbi képen a különböző App Service-erőforrások függőségi sorrendje látható:

![Webalkalmazás-függőségek](media/web-sites-rm-template-guidance/web-dependencies.png)

Az erőforrásokat a következő sorrendben telepíti:

**Első réteg**
* App Service-csomag.
* Bármely más kapcsolódó erőforrások, például adatbázisok vagy tárfiókok.

**Második réteg**
* Web alkalmazás – az App Service-csomagtól függ.
* A kiszolgálófarmot megcélozó Azure Application Insights-példány – az App Service-csomagtól függ.

**3. szint**
* Forrásvezérlő – a webalkalmazástól függ.
* MSDeploy helybővítmény – a webalkalmazástól függ.
* A webalkalmazást megcélozó Azure Application Insights-példány – a webalkalmazástól függ.

**4.**
* Az App Service-tanúsítvány – a forrás-ellenőrzéstől vagy az MSDeploy-től függ, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.
* A konfigurációs beállítások (kapcsolati karakterláncok, web.config értékek, alkalmazásbeállítások) – a forrás-szabályozástól vagy az MSDeploy szolgáltatástól függenek, ha van ilyen. Ellenkező esetben a webalkalmazástól függ.

**5. szint**
* Az állomásnév-kötések a tanúsítványtól függnek, ha vannak ilyenek. Ellenkező esetben egy magasabb szintű erőforrástól függ.
* Helybővítmények – a konfigurációs beállításoktól függ, ha vannak ilyenek. Ellenkező esetben egy magasabb szintű erőforrástól függ.

A megoldás általában csak néhány ilyen erőforrások és szintek. Hiányzó szintek esetén rendelje hozzá az alacsonyabb erőforrásokat a következő magasabb szinthez.

A következő példa egy sablon egy részét mutatja be. A kapcsolati karakterlánc konfigurációjának értéke az MSDeploy bővítménytől függ. Az MSDeploy bővítmény a webalkalmazástól és az adatbázistól függ. 

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

A fenti kódot használó használatra kész mintát a [Sablon: Egyszerű Umbraco webalkalmazás létrehozása című témakörben talál.](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)

## <a name="find-information-about-msdeploy-errors"></a>Az MSDeploy-hibákkal kapcsolatos információk keresése

Ha az Erőforrás-kezelő sablon az MSDeploy-t használja, a telepítési hibaüzenetek nehezen érthetők lehetnek. Ha egy sikertelen telepítés után további információkat szeretne kapni, próbálkozzon az alábbi lépésekkel:

1. Ugrás az oldal [Kudu konzoljára.](https://github.com/projectkudu/kudu/wiki/Kudu-console)
2. Tallózással keresse meg a mappát a D:\home\LogFiles\SiteExtensions\MSDeploy mappában.
3. Keresse meg az appManagerStatus.xml és az appManagerLog.xml fájlokat. Az első fájl naplózza az állapotot. A második fájl naplózza a hibával kapcsolatos információkat. Ha a hiba nem egyértelmű az Ön számára, akkor is, ha segítséget kér a [fórumon](https://docs.microsoft.com/answers/topics/azure-webapps.html).

## <a name="choose-a-unique-web-app-name"></a>Egyedi webalkalmazás-név kiválasztása

A webalkalmazás nevének globálisan egyedinek kell lennie. Használhatja a névhasználati konvenció, amely valószínűleg egyedi, vagy használhatja a [uniqueString függvényt,](../azure-resource-manager/templates/template-functions-string.md#uniquestring) hogy segítsen egy egyedi név létrehozásában.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Webalkalmazás-tanúsítvány telepítése a Key Vaultból

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ha a sablon tartalmaz egy [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) erőforrást a TLS/SSL-kötéshez, és a tanúsítvány egy Key Vaultban van tárolva, meg kell győződnie arról, hogy az App Service-identitás hozzáférhet a tanúsítványhoz.

A globális Azure-ban az App Service principal **abfa0a7c-a6b6-4736-8310-585508787cd**azonosítóval rendelkezik. Az App Service egyszerű szolgáltatás key vault-hozzáférésének engedélyezéséhez használja a következőket:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Az Azure Government rendszerben az App Service Principal **azonosítója 6a02c803-dafd-4136-b4c3-5a6f318b4714.** Használja ezt az azonosítót az előző példában.

A Kulcstárban válassza **a Tanúsítványok** és **létrehozás/importálás** lehetőséget a tanúsítvány feltöltéséhez.

![Tanúsítvány importálása](media/web-sites-rm-template-guidance/import-certificate.png)

A sablonban adja meg a tanúsítvány `keyVaultSecretName`nevét a hoz.

Például sablon, [lásd: Telepítse n: Web App tanúsítvány key vault titkos, és használja az SSL-kötés létrehozásához.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)

## <a name="next-steps"></a>További lépések

* A webalkalmazások sablonnal történő üzembe helyezéséről a [Mikroszolgáltatások kikényszerítése és üzembe helyezése az Azure-ban.For](deploy-complex-application-predictably.md)a tutorial on a tutorial on deploy web apps with a template, for provision provision and deploy microservices predictably in Azure.
* A Sablonokban lévő JSON-szintaxisról és az erőforrástípusok tulajdonságairól az [Azure Resource Manager sablonhivatkozása .](/azure/templates/)
