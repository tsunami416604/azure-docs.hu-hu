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
ms.openlocfilehash: 3181609bf34a04de4e31b73429f9bc5fa3fe3408
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411896"
---
# <a name="azure-app-configuration-faq"></a>Azure-alkalmazás konfigurációja – gyakori kérdések

Ez a cikk címek gyakran használják az Azure App konfigurációjával kapcsolatos kérdésekre.

## <a name="how-is-app-configuration-different-from-key-vault"></a>Miben különbözik konfigurálása a Key Vault?

Használati esetek külön készlete készült alkalmazások konfigurálása: segít a fejlesztőknek Alkalmazásbeállítások felügyelheti és ellenőrizheti a szolgáltatás rendelkezésre állási. A cél egyszerűsítése érdekében számos feladata az összetett konfigurációs adatok használatát. Támogatja a hierarchikus névtér, a címkézés, kiterjedt lekérdezések, kötegelt lekéréséhez, és a speciális felügyeleti műveletek és kezelőfelületet. Alkalmazáskonfiguráció kiegészíti a Key Vault, a két egymás mellett kell használható a legtöbb központi alkalmazástelepítéshez.

## <a name="should-i-store-secrets-in-app-configuration"></a>Ezért titkos kulcsokat is tárolja az Alkalmazáskonfigurációt?

Alkalmazáskonfiguráció megerősített biztonságot nyújt, míg a Key Vault még mindig a legjobb hely az alkalmazás titkainak tárolásához. Hardver-titkosítást, a részletes hozzáférés-házirendek és a felügyeleti műveleteket, például a tanúsítvány rotációja biztosít.

## <a name="does-app-configuration-encrypt-my-data"></a>Alkalmazáskonfiguráció titkosítja az adataimat?

Igen. Alkalmazások konfigurálása az összes kulcsérték tartalmazza, és a hálózati kommunikáció titkosítja. A kulcsnevek indexek, konfigurációs adatainak beolvasása céljából használja, és nem titkosítottak.

## <a name="does-app-configuration-support-azure-virtual-network-vnet"></a>Támogatja az Alkalmazáskonfigurációt az Azure Virtual Network (VNET)?

még nem. VNET-támogatás általánosan elérhető tervezünk.

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
