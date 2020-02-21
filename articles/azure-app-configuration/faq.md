---
title: Azure-alkalmazás konfigurálása – gyakori kérdések
description: Az Azure-alkalmazás konfigurálásával kapcsolatos gyakori kérdések
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 60ba0a7723861d6e642a23418dda6a1daa57f14e
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523492"
---
# <a name="azure-app-configuration-faq"></a>Azure-alkalmazás konfigurálása – gyakori kérdések

Ez a cikk az Azure-alkalmazás konfigurálásával kapcsolatos gyakori kérdésekre ad választ.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Miben különbözik az alkalmazások konfigurációjának Azure Key Vault?

Az alkalmazás konfigurációja segítségével a fejlesztők kezelhetik az Alkalmazásbeállítások és a vezérlési funkciók rendelkezésre állását. Célja, hogy leegyszerűsítse az összetett konfigurációs adatok használatának számos feladatát.

Az alkalmazás konfigurációja a következőket támogatja:

- Hierarchikus névterek
- címkézés
- Részletes lekérdezések
- Kötegelt lekérés
- Speciális felügyeleti műveletek
- Egy szolgáltatás-felügyeleti felhasználói felület

Az alkalmazás konfigurációja kiegészíti a Key Vault, a kettőt pedig a legtöbb alkalmazás-telepítés egymás mellett kell használni.

## <a name="should-i-store-secrets-in-app-configuration"></a>A titkokat az alkalmazás konfigurációjában kell tárolni?

Bár az alkalmazás konfigurációja megerősített biztonságot nyújt, Key Vault még mindig az alkalmazási titkok tárolására szolgáló legjobb hely. A Key Vault hardveres titkosítást, részletes hozzáférési házirendeket és felügyeleti műveleteket biztosít, például a tanúsítvány elforgatását.

Létrehozhat olyan alkalmazás-konfigurációs értékeket, amelyek a Key Vaultban tárolt titkokra hivatkoznak. További információ: [Key Vault referenciák használata ASP.net Core alkalmazásban](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Titkosítja az alkalmazás konfigurációja az adataimat?

Igen. Az alkalmazás konfigurációja titkosítja az összes általa birtokolt kulcs értékét, és titkosítja a hálózati kommunikációt. A kulcsok nevei és címkéi indexként használatosak a konfigurációs adatok beolvasásához, és nincsenek titkosítva.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Miben különbözik az alkalmazás konfigurációja a Azure App Service beállításaitól?

Azure App Service lehetővé teszi az egyes App Service-példányok Alkalmazásbeállítások megadását. Ezeket a beállításokat környezeti változókként adja át az alkalmazás kódjának. Ha kívánja, társíthat egy beállítást egy adott üzembe helyezési ponthoz. További információ: az [Alkalmazásbeállítások konfigurálása](/azure/app-service/configure-common#configure-app-settings).

Ezzel szemben az Azure-alkalmazás konfigurációja lehetővé teszi olyan beállítások megadását, amelyek több alkalmazás között megoszthatók. Ide tartoznak a App Serviceon futó alkalmazások, valamint a többi platform is. Az alkalmazás kódja az Azure SDK-n keresztül, vagy közvetlenül a REST API-kon keresztül fér hozzá ezekhez a beállításokhoz a .NET és a Java konfigurációs szolgáltatóján keresztül.

A beállításokat App Service és az alkalmazás konfigurációja között is importálhatja és exportálhatja. Ez a funkció lehetővé teszi, hogy gyorsan beállítson egy új alkalmazás-konfigurációs tárolót a meglévő App Service beállítások alapján. A konfigurációt megoszthatja egy meglévő alkalmazással is, amely a App Service beállításokra támaszkodik.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Vannak korlátozások az alkalmazás konfigurációjában tárolt kulcsokra és értékekre?

Egy kulcs-érték elemhez legfeljebb 10 KB mennyiség használható.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hogyan tárolhatom a konfigurációkat több környezethez (tesztelés, előkészítés, gyártás stb.)?

Ön határozza meg, hogy ki férhet hozzá az alkalmazás konfigurálásához egy áruházbeli szinten. Használjon külön tárolót minden olyan környezethez, amelyhez eltérő engedélyek szükségesek. Ez a megközelítés biztosítja a legjobb biztonsági elkülönítést.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Mik az alkalmazások konfigurációjának használatának ajánlott módjai?

Lásd: [ajánlott eljárások](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Mennyibe kerül az alkalmazás konfigurációja?

Kétféle árképzési szint létezik: 

- Ingyenes szintű
- Standard szint.

Ha a standard szint bevezetését megelőzően hozta létre a tárolót, a rendszer automatikusan áthelyezi az ingyenes szintet az általánosan elérhetővé tételig. Megadhatja, hogy a standard szintre frissítsen, vagy az ingyenes szinten maradjon.

A standard szintről az ingyenes szintre nem lehet visszaminősíteni egy áruházat. Létrehozhat egy új tárolót az ingyenes szinten, majd importálhatja a konfigurációs adattárakat a tárolóba.

## <a name="which-app-configuration-tier-should-i-use"></a>Melyik alkalmazás-konfigurációs szintet érdemes használni?

Mindkét alkalmazás-konfigurációs szint alapvető funkciókat kínál, beleértve a konfigurációs beállításokat, a funkciók jelzőit, Key Vault hivatkozásokat, az alapszintű felügyeleti műveleteket, a metrikákat és a naplókat.

A következő szempontok a rétegek kiválasztására vonatkoznak.

- **Erőforrások/előfizetés**: az erőforrások egyetlen konfigurációs tárolóból állnak. Az egyes előfizetések az ingyenes szinten egyetlen konfigurációs tárolóra korlátozódnak. Az előfizetések korlátlan számú konfigurációs tárolót használhatnak a standard szinten.
- **Tárterület/erőforrás**: az ingyenes szinten minden konfigurációs tároló 10 MB tárterületre korlátozódik. A standard szinten az egyes konfigurációs tárolók akár 1 GB tárterületet is használhatnak.
- **Legfontosabb előzmények**: az alkalmazás konfigurációja tárolja a kulcsokon végrehajtott összes módosítás előzményeit. Az ingyenes szinten az előzményeket hét napig tároljuk. A standard szinten az előzményeket 30 napig tároljuk.
- **Kérések száma**naponta: az ingyenes szintű áruházak napi 1 000 kérelemre korlátozódnak. Ha a tároló eléri a 1 000-es kérelmeket, a rendszer a 429-as HTTP-állapotkódot fogja visszaadni az összes kérelemhez az éjféli UTC-ig

    A standard szintű tárolók esetében az első 200 000-kérelmeket naponta felszámítjuk. A további kérések számlázása a túlterhelésnek megfelelően történik.

- **Szolgáltatói szerződés**: a standard szint 99,9%-os rendelkezésre állást biztosít. Az ingyenes szint nem rendelkezik SLA-val.
- **Biztonsági funkciók**: mindkét réteg tartalmaz alapszintű biztonsági funkciókat, beleértve a Microsoft által kezelt kulcsokkal történő titkosítást, a HMAC vagy a Azure Active Directory, a RBAC-támogatás és a felügyelt identitás használatával történő hitelesítést. A standard szint fejlettebb biztonsági funkciókat kínál, többek között a privát kapcsolatok támogatását és a titkosítást az ügyfél által felügyelt kulcsokkal.
- **Költség**: a standard szintű tárolók napi használati díjat számítanak fel. A napi kiosztást megelőzően a kérések túlterhelését is felszámítjuk. Ingyenes szintű tároló használata díjmentes.

## <a name="can-i-upgrade-a-store-from-the-free-tier-to-the-standard-tier-can-i-downgrade-a-store-from-the-standard-tier-to-the-free-tier"></a>Frissíthetek áruházat az ingyenes szintről a standard szintre? Visszaválthatok egy áruházat a standard szintről az ingyenes szintre?

Az ingyenes szintről a standard szintre bármikor frissíthet.

A standard szintről az ingyenes szintre nem lehet visszaminősíteni egy áruházat. Létrehozhat egy új tárolót az ingyenes szinten, majd [importálhatja a konfigurációs adattárakat a tárolóba](howto-import-export-data.md).

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hogyan kaphatok értesítéseket az új kiadásokról és az alkalmazás konfigurálásával kapcsolatos egyéb információkról?

Fizessen elő a [GitHub-hirdetmények](https://github.com/Azure/AppConfiguration-Announcements)tárházában.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hogyan jelenthetem a problémát vagy javaslatot?

Közvetlenül a [githubon](https://github.com/Azure/AppConfiguration/issues)érhet el minket.

## <a name="next-steps"></a>Következő lépések

* [Az Azure-alkalmazások konfigurációjának ismertetése](./overview.md)
