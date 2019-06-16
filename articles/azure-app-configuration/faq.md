---
title: Azure-alkalmazás konfigurációja – gyakori kérdések |} A Microsoft Docs
description: Gyakori kérdések az Azure-alkalmazások konfigurálása
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
ms.openlocfilehash: e321c0b473b110597b5b87a6e67666737116daa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393351"
---
# <a name="azure-app-configuration-faq"></a>Azure-alkalmazás konfigurációja – gyakori kérdések

Ez a cikk foglalkozik az Azure-alkalmazások konfigurálása – gyakori kérdések.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>Miben tér el az App Configuration az Azure Key Vaulttól?

Használati esetek külön készlete készült alkalmazások konfigurálása: segít a fejlesztőknek Alkalmazásbeállítások felügyelheti és ellenőrizheti a szolgáltatás rendelkezésre állási. A cél egyszerűsítése érdekében számos feladata az összetett konfigurációs adatok használatát.

Alkalmazáskonfiguráció támogatja:

- Hierarchikus névtér
- Címkézés
- Széles körű lekérdezések
- A Batch lekéréséhez
- Speciális felügyeleti műveleteket
- A szolgáltatás-felügyelet felhasználói felületét

Alkalmazáskonfiguráció kiegészíti a Key Vaultban, és a két egymás mellett a legtöbb központi alkalmazástelepítéshez használandó.

## <a name="should-i-store-secrets-in-app-configuration"></a>Ezért titkos kulcsokat is tárolja az Alkalmazáskonfigurációt?

Alkalmazáskonfiguráció megerősített biztonságot nyújt, bár a Key Vault még mindig a legjobb hely az alkalmazás titkainak tárolásához. Key Vault lehetővé teszi a hardver-titkosítást, részletes hozzáférés-házirendek és felügyeleti műveletek, például a tanúsítvány rotációja.

## <a name="does-app-configuration-encrypt-my-data"></a>Alkalmazáskonfiguráció titkosítja az adataimat?

Igen. Alkalmazáskonfiguráció tart fenn minden kulcsérték titkosítja, és hogy titkosítja a hálózati kommunikáció. A kulcsnevek indexeket, konfigurációs adatainak beolvasása céljából használja, és nem titkosított.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hogyan kell tárolni a konfiguráció több környezethez (tesztelési, átmeneti, éles és így tovább)?

Jelenleg szabályozhatja, ki férhet hozzá konfigurálása tároló szinten. Használjon külön tárolhatják az egyes környezetekhez, amely a különböző engedélyekkel kell rendelkeznie. Ez a megközelítés biztosítja a legjobb biztonsági elkülönítés.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Mik azok a javasolt módszereket használhatja az Alkalmazáskonfigurációt?

Lásd: [ajánlott eljárások](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Alkalmazáskonfiguráció mennyibe?

A szolgáltatás használata a nyilvános előzetes verzió ideje alatt ingyenes.

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hogyan lehet probléma jelentéséhez vagy adjon meg egy javaslatot?

Akkor is kapcsolatba lépni velünk közvetlenül a [GitHub](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>További lépések

* [Azure-alkalmazás konfigurálásával kapcsolatban](./overview.md)
