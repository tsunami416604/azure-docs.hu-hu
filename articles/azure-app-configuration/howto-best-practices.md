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
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413622"
---
# <a name="azure-app-configuration-best-practices"></a>Alkalmazások konfigurálása az Azure ajánlott eljárások

Ez a cikk ismerteti a gyakori patterns and practices nevű Azure-alkalmazások konfigurálása használatakor.

## <a name="key-groupings"></a>Kulcs csoportosításokat

Alkalmazások konfigurálása a kulcsok rendszerezéséhez két lehetőséget biztosít: kulcs előtagok vagy címkéket. Használhatja az egyik vagy mindkét.

Kulcs előtagok a kulcsok elején részeit. Olyan kulcsokat logikailag csoportosíthatja a nevükben ugyanazon előtaggal használatával. Előtagok összekapcsolása egy elválasztó alapján például több összetevőből is tartalmazhat `/`, hasonlóan ahhoz, hogy egy URL-cím, egy névteret. Az ilyen hierarchiák akkor hasznos, ha egy alkalmazás a konfigurációs adattárolónál a kulcsokat számos olyan alkalmazások, a Komponensszolgáltatások és környezetek tárolja. Egy fontos figyelembe kell venni, kulcsok biztosítják az alkalmazás kódjában hivatkozik az értékeket a megfelelő beállítások beolvasása. A kulcs nem kell módosítani, különben kód végbemenő minden alkalommal, amikor módosítani kell.

Címkék egy olyan attribútum, a kulcsok. Hozzon létre egy kulcsot változatának használhatók. Például címkék rendelhet egy adott kulcs több verzióját. A verzió lehet egy iterációját, környezet és más környezeti információ. Az alkalmazás kulcs-értékeket egy teljesen külön készlete kérhetnek csak egy másik címke megadásával. Az összes hivatkozások változatlan marad.

## <a name="key-value-compositions"></a>Kulcs-érték összetételű

Alkalmazás konfigurációja független entitásokként vele tárolt összes kulcsok kezeli. Nem próbál a kikövetkeztetni a kulcsok közötti minden olyan kapcsolat, vagy saját hierarchiájukhoz alapján kulcsérték öröklik. Több példányban kulcsok összesítheti, azonban a címkék használatával összefüggő az alkalmazás kódjában rétegezést megfelelő konfigurációval.

Lássunk erre egy példát. A beállítás **Asset1** amelynek értéke a "Fejlesztés" környezet eltérőek lehetnek. Létrehozhat egy üres címke és a egy úgynevezett "Fejlesztés" címke "Asset1" nevű kulcs. Az alapértelmezett értéket a **Asset1** a korábbi és a "Fejlesztés" adott érték lesz az utóbbi a. A kódban akkor először kérjen le olyan címke, majd a "Fejlesztés" címke ugyanazokkal a kulcsokkal bármely korábbi értékeinek felülírása nélkül a kulcs értékeit. Ha egy modern, például a .NET Core programozási keretrendszert használ, beszerezheti a halmozási funkció ingyenes Alkalmazáskonfiguráció eléréséhez natív konfigurációszolgáltató használatakor. A következő kódrészlet azt mutatja be, hogyan implementálható rétegezést egy .NET Core-alkalmazást.

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

Egy alkalmazás a konfigurációs adattároló eléréséhez, használhatja a kapcsolati karakterláncot, amely az Azure Portalon érhető el. Kapcsolati karakterláncok tartalmazhat a hitelesítő adatokat, és a titkos kulcsok minősül. A Key Vaultban tárolni van szükségük. Jobb megoldás az Azure által felügyelt identitás. Ezzel a módszerrel csak akkor kell Alkalmazáskonfiguráció végpont URL-címe, elindíthat a konfigurációs adattárolónál a hozzáférést. Az alkalmazás kódjában ágyazható be az URL-címet (például a *appsettings.json* fájlt). Lásd: [integrálás az Azure által felügyelt identitásokat](howto-integrate-azure-managed-service-identity.md) további részletekért.

## <a name="web-app-or-function-access-to-app-configuration"></a>Webalkalmazás vagy függvény hozzáférést az alkalmazások konfigurálása

A kapcsolati karakterláncot, az alkalmazás beállításait, az App Service az Azure Portalon keresztül megadhat az alkalmazás a konfigurációs adattároló. A Key Vaultban is tárolhatja és [App Service-ből hivatkozni rá](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Is használhatja az Azure-identitás a konfigurációs adattároló eléréséhez. Lásd: [integrálás az Azure által felügyelt identitásokat](howto-integrate-azure-managed-service-identity.md) további részletekért.

Azt is megteheti küldhet konfigurációs alkalmazások konfigurálása az App Service-ben. Alkalmazások konfigurálása az exportálási funkció (az Azure portal és CLI), amely adatokat küld a közvetlenül az App Service biztosít. Ezzel a módszerrel nem kell minden alkalmazás kódjának módosítása.

## <a name="next-steps"></a>További lépések

* [Kulcsok és értékek](./concept-key-value.md)
