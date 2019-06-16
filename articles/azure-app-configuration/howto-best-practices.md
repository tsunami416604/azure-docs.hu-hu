---
title: Az Azure App konfigurálási eljárásoknak megfelelő beállításában |} A Microsoft Docs
description: Legjobb használata az Azure-alkalmazások konfigurálása
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393587"
---
# <a name="azure-app-configuration-best-practices"></a>Alkalmazások konfigurálása az Azure ajánlott eljárások

Ez a cikk ismerteti a gyakori minták és ajánlott eljárások Azure-alkalmazások konfigurálása használatakor.

## <a name="key-groupings"></a>Kulcs csoportosításokat

Alkalmazások konfigurálása a kulcsok rendszerezéséhez két lehetőséget biztosít:

* Kulcs előtagok
* Címkék

Használhatja az egyik vagy mindkét lehetőség a kulcsokat csoportosítására.

*Előtagok kulcs* kulcsok elején részei. Olyan kulcsokat logikailag csoportosíthatja a nevükben ugyanazon előtaggal használatával. Előtagok tartalmazhat egy elválasztó alapján, például a kapcsolódó összetevőket `/`, hasonlóan ahhoz, hogy egy URL-cím, egy névteret. Az ilyen hierarchiák akkor hasznos, ha egy alkalmazás a konfigurációs adattárolónál a kulcsokat számos olyan alkalmazások, a Komponensszolgáltatások és környezetek tárolja.

Egy fontos figyelembe kell venni, kulcsok biztosítják az alkalmazás kódjában hivatkozik az értékeket a megfelelő beállítások beolvasása. Kulcsok nem szabad módosítani, vagy pedig módosítsa a kódot, amely akkor fordul elő, minden alkalommal, amikor kell.

*Címkék* kulcsok egy attribútumot is. Hozzon létre egy kulcsot változatának használhatók. Például címkék rendelhet egy adott kulcs több verzióját. Előfordulhat, hogy egy verziója egy iterációját, a környezeten vagy más környezeti információ. Az alkalmazás kérhetnek a kulcs értékeit egy teljesen külön készlete egy másik címke megadásával. Ennek eredményeképpen az összes hivatkozások a kódban változatlan marad.

## <a name="key-value-compositions"></a>Kulcs-érték összetételű

Alkalmazás konfigurációja független entitásokként vele tárolt összes kulcsok kezeli. Alkalmazások konfigurálása nem próbálja meg, vagy kulcsértékek alapján saját hierarchiájukhoz öröklésére következtetnek ki bármely, a kulcsok közötti kapcsolat. Kulcsok, több példányban azonban összesítheti az alkalmazás kódjában rétegezést megfelelő konfiguráció szolgáltatással párosítva címkék használatával.

Lássunk erre egy példát. Tegyük fel, hogy rendelkezik egy beállítás nevű **Asset1**, amelynek az értéke eltérő lehet a fejlesztési környezet alapján. Létrehozhat egy üres címke és a "Fejlesztés" címke "Asset1" nevű kulcs. Az első címke, az alapértelmezett érték a put **Asset1**, és a egy adott érték a "Fejlesztés" az utóbbi helyezi.

A kódban először kérjen le minden olyan címkék nélkül a kulcs értékeit, és majd lekérheti ugyanazokat a kulcs értékeit, a "Fejlesztés" címkével ellátott még egyszer. A második alkalommal kérheti le az értékeket, ha az előző értékeket a kulcsok felülíródnak. A .NET Core konfigurációs rendszer "halmozódik" egymásra konfigurációs adatok több példányban teszi lehetővé. Ha több kulcs már létezik, az utolsó azt tartalmazó használatos. Az egy modern programozási keretrendszerről, például a .NET Core szolgáltatás halmozási képességhez ingyenes Alkalmazáskonfiguráció eléréséhez natív konfigurációszolgáltató használatakor. A következő kódrészlet azt mutatja be, hogyan implementálható rétegezést egy .NET Core-alkalmazást:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Alkalmazás-konfigurációs rendszerindítási

Egy alkalmazás a konfigurációs adattároló eléréséhez, használhatja a kapcsolati karakterláncot, amely az Azure Portalon érhető el. Kapcsolati karakterláncok tartalmaz a hitelesítő adatokat, mert azokat a rendszer titkos kulcsok. Titkos adatokat kell az Azure Key Vaultban tárolhatók, és a kódot kell hitelesítenie a Key Vault kérheti le azokat.

Jobb megoldás, hogy a felügyelt identitásokból szolgáltatással az Azure Active Directoryban. A felügyelt identitásokból kell az alkalmazás a konfigurációs adattároló csak az alkalmazás konfigurációs végponti URL-Címének bootstrap hozzáférést. Az alkalmazás kódjában ágyazható be az URL-címet (például a *appsettings.json* fájlt). Lásd: [integrálás az Azure által felügyelt identitásokat](howto-integrate-azure-managed-service-identity.md) részleteiről.

## <a name="app-or-function-access-to-app-configuration"></a>Alkalmazások konfigurálása alkalmazás vagy függvény a hozzáférést

Megadhat hozzáférés konfigurálása a web Apps alkalmazások vagy funkciók az alábbi módszerek egyikének használatával:

* Az Azure Portalon az alkalmazás a konfigurációs adattároló az App Service alkalmazás beállításaiban adja meg a kapcsolati karakterláncot.
* A Key Vaultban az alkalmazás a konfigurációs adattárolónál a kapcsolati karakterlánc Store és [App Service-ből hivatkozni rá](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Az Azure által felügyelt identitásokat az alkalmazás a konfigurációs adattároló eléréséhez. További információkért lásd: [integrálás az Azure által felügyelt identitásokat](howto-integrate-azure-managed-service-identity.md).
* Leküldéses konfigurációt alkalmazások konfigurálása az App Service-ben. Alkalmazások konfigurálása az exportálási funkció (az Azure Portalon és az Azure CLI), amely adatokat küld a közvetlenül az App Service biztosítja. Ezzel a módszerrel az alkalmazás kódjának módosítása egyáltalán nem kell.

## <a name="next-steps"></a>További lépések

* [Kulcsok és értékek](./concept-key-value.md)
