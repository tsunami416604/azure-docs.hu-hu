---
title: Azure-alkalmazás konfigurálása – GYIK | Microsoft Docs
description: Az Azure-alkalmazás konfigurálásával kapcsolatos gyakori kérdések
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
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904098"
---
# <a name="azure-app-configuration-faq"></a>Azure-alkalmazás konfigurálása – gyakori kérdések

Ez a cikk az Azure-alkalmazás konfigurálásával kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Miben tér el az App Configuration az Azure Key Vaulttól?

Az alkalmazás konfigurációja különböző használati esetekhez készült: segít a fejlesztőknek az Alkalmazásbeállítások és a vezérlési funkciók rendelkezésre állásának kezelésében. Célja, hogy leegyszerűsítse az összetett konfigurációs adatok használatának számos feladatát.

Az alkalmazás konfigurációja a következőket támogatja:

- Hierarchikus névterek
- címkézés
- Részletes lekérdezések
- Kötegelt lekérés
- Speciális felügyeleti műveletek
- Egy szolgáltatás-felügyeleti felhasználói felület

Az alkalmazás konfigurációja kiegészíti a Key Vault, a kettőt pedig a legtöbb alkalmazás-telepítésnél egymás mellett kell használni.

## <a name="should-i-store-secrets-in-app-configuration"></a>A titkokat az alkalmazás konfigurációjában kell tárolni?

Bár az alkalmazás konfigurációja megerősített biztonságot nyújt, Key Vault még mindig az alkalmazási titkok tárolására szolgáló legjobb hely. A Key Vault hardveres titkosítást, részletes hozzáférési házirendeket és felügyeleti műveleteket biztosít, például a tanúsítvány elforgatását.

Létrehozhat olyan alkalmazás-konfigurációs értékeket, amelyek a Key Vaultban tárolt titkokra hivatkoznak. További információ: [Key Vault referenciák használata ASP.net Core alkalmazásban](./use-key-vault-references-dotnet-core.md).

## <a name="does-app-configuration-encrypt-my-data"></a>Titkosítja az alkalmazás konfigurációja az adataimat?

Igen. Az alkalmazás konfigurációja titkosítja az összes általa birtokolt kulcs értékét, és titkosítja a hálózati kommunikációt. A kulcsok nevei indexként használatosak a konfigurációs adatok beolvasásához, és nincsenek titkosítva.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Miben különbözik az alkalmazás konfigurációja a Azure App Service beállításaitól?

Azure App Service lehetővé teszi az egyes App Service-példányok Alkalmazásbeállítások megadását. Ezeket a beállításokat környezeti változókként adja át az alkalmazás kódjának. Ha szükséges, társíthat egy beállítást egy adott üzembe helyezési ponthoz. További információ: az [Alkalmazásbeállítások konfigurálása](/azure/app-service/configure-common#configure-app-settings).

Ezzel szemben az Azure-alkalmazás konfigurációja lehetővé teszi olyan beállítások megadását, amelyek több alkalmazás között megoszthatók, beleértve a App Serviceon futó alkalmazásokat is. Ezek a beállítások az alkalmazás kódjában érhetők el a .NET és a Java konfigurációs szolgáltatón keresztül, az Azure SDK-ban, vagy közvetlenül a REST API-kon keresztül.

A beállításokat App Service és az alkalmazás konfigurációja között is importálhatja és exportálhatja. Ez lehetővé teszi, hogy gyorsan beállítson egy új alkalmazás-konfigurációs tárolót a meglévő App Service beállítások alapján, vagy egyszerűen megoszthatja a konfigurációt egy olyan meglévő alkalmazással, amely a App Service beállításokra támaszkodik.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Vannak korlátozások az alkalmazás konfigurációjában tárolt kulcsokra és értékekre?

Egyetlen kulcs-érték elemhez 10KB korlát van.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hogyan tárolhatom a konfigurációkat több környezethez (tesztelés, előkészítés, gyártás stb.)?

Jelenleg szabályozhatja, hogy ki és hogyan férhet hozzá az alkalmazás-konfigurációhoz egy áruházi szinten. Használjon külön tárolót minden olyan környezethez, amelyhez eltérő engedélyek szükségesek. Ez a megközelítés biztosítja a legjobb biztonsági elkülönítést.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Mik az alkalmazások konfigurációjának használatának ajánlott módjai?

Lásd: [ajánlott eljárások](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Mennyibe kerül az alkalmazás konfigurációja?

A szolgáltatás ingyenesen használható a nyilvános előzetes verzióban.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hogyan kaphatok értesítéseket az új kiadásokról és az alkalmazás konfigurálásával kapcsolatos egyéb információkról?

Fizessen elő a [GitHub-hirdetmények](https://github.com/Azure/AppConfiguration-Announcements)tárházában.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hogyan jelenthetem a problémát vagy javaslatot?

Közvetlenül a [githubon](https://github.com/Azure/AppConfiguration/issues)érhet el minket.

## <a name="next-steps"></a>Következő lépések

* [Az Azure-alkalmazások konfigurációjának ismertetése](./overview.md)
