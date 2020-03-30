---
title: Az Azure App konfigurációjának gyakorlati tanácsai | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a legjobban az Azure App Configuration alkalmazást
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348660"
---
# <a name="azure-app-configuration-best-practices"></a>Az Azure App konfigurációjának gyakorlati tanácsai

Ez a cikk ismerteti a gyakori minták és gyakorlati tanácsok az Azure App-konfiguráció használatakor.

## <a name="key-groupings"></a>Kulcscsoportok

Az Alkalmazáskonfiguráció két lehetőséget kínál a kulcsok rendszerezésére:

* Kulcselőtagok
* Címkék

A kulcsok csoportosításához az egyik vagy mindkét beállítás használható.

*A főelőtagok* a kulcsok kezdő részei. A kulcsok készletét logikailag csoportosíthatja, ha ugyanazt az előtagot használja a nevükben. Az előtagok több, határolóval összekapcsolt összetevőt `/`is tartalmazhatnak, például egy URL-elérési úthoz hasonló tangyűjtőt, hogy névteret alkossanak. Az ilyen hierarchiák akkor hasznosak, ha kulcsokat tárol számos alkalmazáshoz, összetevő-szolgáltatáshoz és környezethez egy alkalmazáskonfigurációs tárolóban.

Fontos, hogy tartsa szem előtt, hogy a kulcsok, amit az alkalmazás kód referenciák lekérni az értékeket a megfelelő beállításokat. A kulcsok nem változnak, különben minden alkalommal módosítania kell a kódot.

*A címkék* a kulcsok attribútumai. Egy kulcs változatainak létrehozására használják őket. Címkéket rendelhet például egy kulcs több verziójához. A verzió lehet iteráció, környezet vagy más környezetfüggő információ. Az alkalmazás egy másik címke megadásával kérheti a kulcsértékek egy teljesen eltérő készletét. Ennek eredményeképpen minden kulcshivatkozás változatlan marad a kódban.

## <a name="key-value-compositions"></a>Kulcs-érték kompozíciók

Az Alkalmazáskonfigurálás független entitásként kezeli az összes vele tárolt kulcsot. Az Alkalmazáskonfiguráció nem kísérli meg a kulcsok közötti kapcsolat kikövetkeztetésére vagy a kulcsértékek hierarchia alapján való öröklésére. Több kulcskészletet azonban összesíthet az alkalmazáskódmegfelelő konfigurációs halmozásával összekapcsolt címkék használatával.

Lássunk erre egy példát. Tegyük fel, hogy rendelkezik egy **Asset1**nevű beállítással, amelynek értéke a fejlesztői környezettől függően változhat. Hozzon létre egy "Asset1" nevű kulcsot egy üres címkével és egy "Fejlesztés" nevű címkével. Az első címkén az **Eszköz1**alapértelmezett értékét, az utóbbiban pedig a "Fejlesztés" értéket.

A kódban először lekéri a kulcsértékeket címkék nélkül, majd másodszor is beolvassa ugyanazokat a kulcsértékeket a "Fejlesztés" címkével. Amikor másodszor olvassa be az értékeket, a kulcsok korábbi értékei felülíródnak. A .NET Core konfigurációs rendszer lehetővé teszi, hogy több konfigurációs adathalmazt "halmozzon össze" egymás tetejére. Ha egy kulcs több halmazban is létezik, a program az azt tartalmazó utolsó készletet használja. A modern programozási keretrendszer, például a .NET Core, akkor kap ez a halmozási képesség ingyenes, ha egy natív konfigurációs szolgáltató segítségével éri el az alkalmazás konfigurációját. A következő kódrészlet bemutatja, hogyan valósítható meg a halmozás a .NET Core alkalmazásban:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Címkék használatával különböző konfigurációk különböző környezetekben](./howto-labels-aspnet-core.md) egy teljes példa.

## <a name="app-configuration-bootstrap"></a>Alkalmazáskonfigurációs rendszerindítás

Az App Configuration Store eléréséhez használhatja a kapcsolati karakterláncot, amely elérhető az Azure Portalon. Mivel a kapcsolati karakterláncok hitelesítő adatokat tartalmaznak, titkosnak minősülnek. Ezeket a titkos kulcsokat az Azure Key Vaultban kell tárolni, és a kódnak hitelesítenie kell magát a Key Vault ba azok lekéréséhez.

Egy jobb lehetőség a felügyelt identitások funkció használata az Azure Active Directoryban. Felügyelt identitások esetén csak az alkalmazás konfigurációs végpont URL-címére van szükség az alkalmazáskonfigurációs áruház eléréséhez való hozzáférés rendszerindításhoz. Az URL-címet beágyazhatja az alkalmazáskódjába (például az *appsettings.json* fájlba). A részletekért olvassa el az [Integrálás az Azure felügyelt identitásaival.](howto-integrate-azure-managed-service-identity.md)

## <a name="app-or-function-access-to-app-configuration"></a>Alkalmazás- vagy funkcióhozzáférés az alkalmazáskonfigurációhoz

Az alábbi módszerek bármelyikével hozzáférést biztosíthat a webalkalmazások hoz vagy funkciókhoz az alkalmazáskonfigurációhoz:

* Az Azure Portalon keresztül adja meg a kapcsolati karakterláncot az alkalmazás konfigurációs tárolóaz App Service alkalmazásbeállításait.
* Tárolja a kapcsolati karakterláncot az alkalmazáskonfigurációs tárolóhoz a Key Vaultban, és [hivatkozzon rá az App Service-ből.](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)
* Azure felügyelt identitások az App Configuration Store eléréséhez. További információ: [Integrálás az Azure felügyelt identitásaival](howto-integrate-azure-managed-service-identity.md)című témakörben talál.
* A konfiguráció leküldése az alkalmazáskonfigurációból az App Service-be. Az Alkalmazáskonfiguráció egy exportálási funkciót biztosít (az Azure Portalon és az Azure CLI-ben), amely közvetlenül az App Service-be küldi az adatokat. Ezzel a módszerrel egyáltalán nem kell módosítania az alkalmazáskódot.

## <a name="reduce-requests-made-to-app-configuration"></a>Az alkalmazáskonfigurációval kapcsolatos kérelmek csökkentése

Az alkalmazáskonfigurációra vonatkozó túlzott kérések szabályozást vagy túlterhelési díjakat eredményezhetnek. A kérelmek számának csökkentése:

* Növelje a frissítési időtúlértéket, különösen akkor, ha a konfigurációs értékek nem változnak gyakran. Adjon meg egy új frissítési időoutot a [ `SetCacheExpiration` módszerrel.](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)

* Egyetlen *jelzőgombot*figyel, nem pedig az egyes billentyűket. Csak akkor frissítse az összes konfigurációt, ha a jelzőkulcs megváltozik. Lásd: [Dinamikus konfiguráció használata egy ASP.NET Core alkalmazásban](enable-dynamic-configuration-aspnet-core.md) egy példa.

* Az Azure Event Grid használatával értesítéseket kaphat a konfiguráció változásakor, és nem folyamatosan lekérdezi a módosításokat. További információ: [Az Azure-alkalmazás konfigurációs eseményeinek irányított](./howto-app-configuration-event.md) irányítása egy webes végpontra című témakörben talál további információt

## <a name="importing-configuration-data-into-app-configuration"></a>Konfigurációs adatok importálása az alkalmazáskonfigurációba

Az Alkalmazáskonfiguráció lehetőséget kínál a konfigurációs beállítások tömeges [importálására](https://aka.ms/azconfig-importexport1) az aktuális konfigurációs fájlokból az Azure Portal vagy a CLI használatával. Ugyanazokat a beállításokat használhatja az alkalmazáskonfigurációból, például a kapcsolódó üzletek közötti értékek exportálásához is. Ha folyamatos szinkronizálást szeretne beállítani a GitHub-tártárral, használhatja a [GitHub-műveletet,](https://aka.ms/azconfig-gha2) hogy továbbra is használhassa a meglévő forrásvezérlési eljárásokat, miközben az alkalmazáskonfiguráció előnyeit is igénybe veheti.

## <a name="next-steps"></a>További lépések

* [Kulcsok és értékek](./concept-key-value.md)
