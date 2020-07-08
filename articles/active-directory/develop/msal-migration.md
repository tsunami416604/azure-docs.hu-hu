---
title: Migrálás a Microsoft hitelesítési tárba (MSAL)
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library (MSAL) és az Azure AD Authentication Library (ADAL) közötti különbséget, valamint a MSAL-re való áttelepítést.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 52a4a7131c85231107a2a23a1916016776b219fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367427"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Alkalmazások migrálása a Microsoft hitelesítési tárba (MSAL)

Számos fejlesztő a Azure Active Directory Authentication Library (ADAL) használatával épített és telepített alkalmazásokat. A Microsoft Authentication Library (MSAL) használatát javasoljuk az Azure AD-entitások hitelesítéséhez és engedélyezéséhez.

A ADAL helyett a MSAL használatával:

- Az identitások szélesebb körét is hitelesítheti:
  - Azure AD-identitások
  - Microsoft-fiókok
  - Közösségi és helyi fiókok Azure AD B2C használatával
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- Az alkalmazás engedélyezheti a növekményes hozzájárulásukat.
- A feltételes hozzáférés támogatása egyszerűbb.
- Az innováció előnyeit élvezheti. Mivel az összes Microsoft fejlesztési erőfeszítés most már a MSAL összpontosít, a ADAL-ben nem lesznek új funkciók implementálva.

A **MSAL mostantól az ajánlott hitelesítési függvénytár a Microsoft Identity platformmal való használatra**.

## <a name="migration-guidance"></a>Útmutató a migráláshoz

A következő cikkek segítséget nyújtanak a MSAL való Migrálás során:

- [Migrálás MSAL.Androidra](migrate-android-adal-msal.md)
- [Migrálás MSAL.iOS vagy macOS rendszerre](migrate-objc-adal-msal.md)
- [Migrálás MSAL Javába](migrate-adal-msal-java.md)
- [Migrálás MSAL.js-re](msal-compare-msal-js-and-adal-js.md)
- [Migrálás MSAL.NET-re](msal-net-migration.md)
- [Migrálás MSAL Pythonba](migrate-python-adal-msal.md)
- [Xamarin-alkalmazások migrálása közvetítők használatával MSAL.NET rendszerre](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

__K: a ADAL elavult?__  
V: Igen. 2020. június 30-ig a továbbiakban nem fogunk új funkciókat hozzáadni a ADAL-hez. A kritikus biztonsági javításokat továbbra is a ADAL, 2022. június 30-ig fogjuk hozzáadni.

__K: Hogyan tudni, hogy mely alkalmazások használják a ADAL-t?__  
A: Ha az alkalmazás forráskódját használja, a fenti áttelepítési útmutatók alapján meghatározhatja, hogy az alkalmazás melyik függvénytárat és hogyan telepítse át az MSAL-re. Ha nincs hozzáférése az alkalmazás forráskódhoz, [megnyithat egy támogatási kérést](developer-support-help-options.md#open-a-support-request) a regisztrált alkalmazások listájának beszerzéséhez és az egyes alkalmazások által használt könyvtárhoz.

__K: a meglévő ADAL-alkalmazások továbbra is működni fognak?__  
A: a meglévő alkalmazásai módosítás nélkül továbbra is működőképesek lesznek. Ha azt tervezi, hogy 2022. június 30-án továbbra is megtartja őket, érdemes frissíteni őket a MSAL, hogy biztonságban maradjanak, de a MSAL való Migrálás nem szükséges a meglévő funkciók fenntartása érdekében.

__K: Miért érdemes befektetni a MSAL-re?__  
A: a MSAL olyan új szolgáltatásokat tartalmaz, amelyek nem a ADAL, beleértve a növekményes belefoglalást, az egyszeri bejelentkezést és a jogkivonat-gyorsítótárazási kezelést. A ADAL-től eltérően a MSAL a 2022. június 30-ig továbbra is a biztonsági javításokat fogja kapni. [További információk](msal-overview.md).

__K: olyan eszközt fog kibocsátani, amely segít áthelyezni az alkalmazásokat a ADAL-ből a MSAL-be?__  
V: Nem. A kódtárak közötti különbségek szükségessé teszik az erőforrások kiépítését az eszköz fejlesztésére és karbantartására, amelyeket egyébként a MSAL javításával kellene elkölteni. Azonban biztosítjuk az előző áttelepítési útmutatókat, amelyekkel elvégezheti a szükséges módosításokat az alkalmazásban.

__K: Hogyan működik a MSAL a AD FS?__  
A: a MSAL.NET bizonyos forgatókönyveket támogat a AD FS 2019-es hitelesítéshez. Ha az alkalmazásnak a jogkivonatokat közvetlenül a AD FS korábbi verziójából kell beszerezni, akkor továbbra is a ADAL kell maradnia. [További információk](msal-net-adfs-support.md).

__K: Hogyan segítséget nyújt az alkalmazás áttelepítéséhez?__  
Válasz: Tekintse meg a jelen cikk [áttelepítési útmutató](#migration-guidance) című szakaszát. Ha az alkalmazás platformjának elolvasása után további kérdései vannak, Stack Overflow közzéteheti a címkével, `[adal-deprecation]` vagy megnyithatja a problémát a könyvtár GitHub-tárházában. Az egyes könyvtárak tárházára mutató hivatkozásokat az MSAL áttekintését ismertető cikk [nyelvek és keretrendszerek](msal-overview.md#languages-and-frameworks) című szakaszában találja.

## <a name="next-steps"></a>További lépések

- [Alkalmazások frissítése a Microsoft Authentication Library és a Microsoft Graph API használatára](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [További információ a Microsoft Identity platformról (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Tekintse át a MSAL-kód mintáit](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)
