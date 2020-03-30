---
title: Gyakori kérdések az Azure-alkalmazások konfigurálása – gyakori kérdések
description: Gyakori kérdések az Azure App konfigurációjával kapcsolatban
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 25187fd055f40e8b32d840ead2a9c54882446b88
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348787"
---
# <a name="azure-app-configuration-faq"></a>Gyakori kérdések az Azure-alkalmazások konfigurálása – gyakori kérdések

Ez a cikk választ ad az Azure App-konfigurációval kapcsolatos gyakori kérdésekre.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Miben különbözik az alkalmazáskonfiguráció az Azure Key Vaulttól?

Az Alkalmazáskonfiguráció segítségével a fejlesztők kezelhetik az alkalmazásbeállításokat, és szabályozhatják a szolgáltatások elérhetőségét. Célja, hogy egyszerűsítse számos feladatot dolgozik komplex konfigurációs adatokat.

Az Alkalmazáskonfiguráció a következőket támogatja:

- Hierarchikus névterek
- Címkézés
- Kiterjedt lekérdezések
- Köteglekérések
- Speciális irányítási műveletek
- Szolgáltatáskezelő felhasználói felület

Az alkalmazáskonfiguráció kiegészíti a Key Vaultot, és a kettőt egymás mellett kell használni a legtöbb alkalmazás-telepítésben.

## <a name="should-i-store-secrets-in-app-configuration"></a>Tároljam a titkos kulcsokat az alkalmazáskonfigurációban?

Bár az alkalmazáskonfiguráció megerősített biztonságot nyújt, a Key Vault továbbra is a legjobb hely az alkalmazástitok tárolására. A Key Vault hardverszintű titkosítást, részletes hozzáférési házirendeket és felügyeleti műveleteket, például tanúsítványelforgatást biztosít.

Létrehozhat olyan alkalmazáskonfigurációs értékeket, amelyek a Key Vaultban tárolt titkos kulcsokra hivatkoznak. További információt a [Key Vault-hivatkozások használata ASP.NET Core alkalmazásban](./use-key-vault-references-dotnet-core.md)című témakörben talál.

## <a name="does-app-configuration-encrypt-my-data"></a>Titkosítja az alkalmazáskonfiguráció az adataimat?

Igen. Az Alkalmazáskonfiguráció titkosítja az összes általa birtokolt kulcsértéket, és titkosítja a hálózati kommunikációt. A kulcsnevek és címkék indexként szolgálnak a konfigurációs adatok beolvasásához, és nincsenek titkosítva.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Miben különbözik az Alkalmazáskonfiguráció az Azure App Service beállításaitól?

Az Azure App Service lehetővé teszi, hogy minden egyes App Service-példányhoz alkalmazásbeállításokat határozzon meg. Ezek a beállítások környezeti változókként kerülnek átadásra az alkalmazáskódnak. Ha szeretné, társíthat egy beállítást egy adott központi telepítési helyhez. További információt az [Alkalmazásbeállítások konfigurálása](/azure/app-service/configure-common#configure-app-settings)című témakörben talál.

Ezzel szemben az Azure App Configuration lehetővé teszi, hogy olyan beállításokat határozzon meg, amelyek több alkalmazás között oszthatók meg. Ez magában foglalja az App Service-ben futó alkalmazásokat, valamint más platformokat is. Az alkalmazáskód a .NET és a Java konfigurációs szolgáltatóin keresztül, az Azure SDK-n keresztül vagy közvetlenül a REST API-kon keresztül éri el ezeket a beállításokat.

A beállításokat az App Service és az App Configuration között is importálhatja és exportálhatja. Ez a funkció lehetővé teszi, hogy gyorsan beállítegy új App Configuration Store a meglévő App Service-beállítások alapján. A konfigurációt megoszthatja egy meglévő alkalmazással is, amely az App Service-beállításokra támaszkodik.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Vannak-e méretkorlátozások az alkalmazáskonfigurációban tárolt kulcsokra és értékekre vonatkozóan?

Egyetlen kulcsérték-elem esetén legfeljebb 10 KB lehet.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hogyan tárolhatom a konfigurációkat több környezetben (teszt, előkészítés, éles környezet) ?

Ön szabályozhatja, hogy ki férhet hozzá az alkalmazáskonfigurációhoz egy üzletenkénti szinten. Használjon külön tárolót minden olyan környezethez, amely különböző engedélyeket igényel. Ez a megközelítés biztosítja a legjobb biztonsági elkülönítést.

Ha nincs szüksége a környezetek közötti biztonsági elkülönítésre, címkék kelthet a konfigurációs értékek megkülönböztetésére. [Címkék használatával különböző konfigurációk különböző környezetekben](./howto-labels-aspnet-core.md) egy teljes példa.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Melyek az alkalmazáskonfiguráció használatának ajánlott módjai?

Tekintse meg [az ajánlott eljárásokat](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Mennyibe kerül az alkalmazáskonfiguráció?

Két tarifacsomag létezik:

- Ingyenes szint
- Standard szint.

Ha a standard szint bevezetése előtt létrehozott egy áruházat, az általános elérhetőség után automatikusan átkerült az ingyenes szintre. Választhat, hogy a standard szintre frissít, vagy az ingyenes szinten marad.

Nem lehet leminősíteni egy áruházat a Standard szintről az ingyenes szintre. Létrehozhat egy új tárolót az ingyenes szinten, majd importálhatja a konfigurációs adatokat az adott tárolóba.

## <a name="which-app-configuration-tier-should-i-use"></a>Melyik alkalmazáskonfigurációs réteget használjam?

Mindkét alkalmazáskonfigurációs szint alapvető funkciókat kínál, beleértve a konfigurációs beállításokat, a szolgáltatásjelzőket, a Key Vault-hivatkozásokat, az alapvető felügyeleti műveleteket, a metrikákat és a naplókat.

A következő szempontok a szint kiválasztásával kapcsolatosak.

- **Előfizetésenkénti erőforrások:** Az erőforrás egyetlen konfigurációs tárolóból áll. Minden előfizetés egyetlen konfigurációs tárolóra korlátozódik az ingyenes csomagban. Az előfizetések korlátlan számú konfigurációs tárolóval rendelkezhetnek a standard csomagban.
- **Erőforrásonkénti tárterület:** Az ingyenes szinten minden konfigurációs tároló 10 MB tárra van korlátozva. A standard szinten minden konfigurációs tároló legfeljebb 1 GB tárhelyet használhat.
- **Kulcselőzmények:** Az alkalmazás konfigurációja a kulcsokon végrehajtott összes módosítás előzményeit tárolja. Az ingyenes szinten ez az előzmények hét napig tárolódnak. A standard szinten ez az előzmények 30 napig tárolódnak.
- **Napi kérések:** Az ingyenes áruházak naponta legfeljebb 1000 kérelemre jogosultak. Amint egy áruház eléri az 1000 kérést, a 429-es HTTP-állapotkódot adja vissza az összes kérelemhez az UTC éjfélig.

    A standard szintű üzletek esetében az első 200 000 kérés t tartalmazza a napi díj. A további kérelmek et túllépésként számlázunk.

- **Szolgáltatásiszint-szerződés:** A standard szint 99,9%-os SLA-val rendelkezik. Az ingyenes szint nem rendelkezik SLA-val.
- **Biztonsági funkciók:** Mindkét réteg alapvető biztonsági funkciókat tartalmaz, beleértve a Microsoft által felügyelt kulcsokkal történő titkosítást, a HMAC-en vagy az Azure Active Directoryn keresztüli hitelesítést, az RBAC-támogatást és a felügyelt identitást. A Standard szint fejlettebb biztonsági funkciókat kínál, beleértve a Privát kapcsolat támogatását és az ügyfél által felügyelt kulcsokkal való titkosítást.
- **Költség**: A standard szintű üzletek napi használati díja van. A napi allokáción túli kérelmekért túlóradíjat is felszámítunk. Az ingyenes áruház használata nem jár költséggel.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Frissíthetek egy áruházat az ingyenes szintről a standard szintre? Leminősíthetek egy áruházat a Standard szintről az ingyenes szintre?

Az ingyenes szintről bármikor frissíthet a Standard szintre.

Nem lehet leminősíteni egy áruházat a Standard szintről az ingyenes szintre. Létrehozhat egy új tárolót az ingyenes szinten, majd importálhatja a [konfigurációs adatokat az adott tárolóba.](howto-import-export-data.md)

## <a name="are-there-any-limits-on-the-number-of-requests-made-to-app-configuration"></a>Korlátozva vannak az alkalmazáskonfigurációra irányuló kérelmek száma?

Az ingyenes csomag konfigurációs tárolói naponta legfeljebb 1000 kérelemre korlátozódnak. A standard szintű konfigurációs tárolók ideiglenes szabályozást tapasztalhatnak, ha a kérelem sebessége meghaladja az óránkénti 20 000 kérelmet.

Amikor egy áruház eléri a korlátot, a 429-es HTTP-állapotkódot adja vissza az összes kérelemhez, amíg az időszak le nem jár. A `retry-after-ms` válasz fejléce a kérelem újbóli megkísérlése előtt javasolt várakozási időt (ezredmásodpercben) ad meg.

Ha az alkalmazás rendszeresen http-állapotkód 429 válaszok, fontolja meg újratervezése, hogy csökkentse a kérelmek számát. További információ: [Az alkalmazáskonfigurációra irányuló kérelmek csökkentése](./howto-best-practices.md#reduce-requests-made-to-app-configuration)

## <a name="my-application-receives-http-status-code-429-responses-why"></a>Az alkalmazásom 429-es HTTP-állapotkódot kap. Hogy miért?

A következő körülmények között kap egy 429-es HTTP-állapotkódot:

* Az ingyenes csomagban lévő áruház napi kérelemkorlátjának túllépése.
* Ideiglenes szabályozás a standard szintű áruház magas igénylési aránya miatt.
* Túlzott sávszélesség-használat.
* Kulcs létrehozására vagy módosítására tett kísérlet a tárolási ajánlat túllépése esetén.

Ellenőrizze a 429-es válasz törzsét a kérés sikertelensítésének konkrét oka miatt.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hogyan kaphatok bejelentéseket az új kiadásokról és az alkalmazáskonfigurációval kapcsolatos egyéb információkról?

Iratkozzon fel [a GitHub-bejelentések repo- ra.](https://github.com/Azure/AppConfiguration-Announcements)

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hogyan jelenthetek egy problémát, vagy nem tudok javaslatot tenni?

Közvetlenül a [GitHubon](https://github.com/Azure/AppConfiguration/issues)érhet el minket.

## <a name="next-steps"></a>További lépések

* [Tudnivalók az Azure App Configurationről](./overview.md)
