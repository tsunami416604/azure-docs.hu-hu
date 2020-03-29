---
title: Áttelepítés a Microsoft hitelesítési könyvtárba (MSAL)
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library (MSAL) és az Azure AD authentication library (ADAL) közötti különbségeket, valamint az MSAL-ra való áttelepítés módját.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164931"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Alkalmazások áttelepítése a Microsoft authentication library (MSAL) könyvtárba

A Microsoft Authentication Library (MSAL) és az Azure AD authentication Library (ADAL) egyaránt használható az Azure AD-entitások hitelesítésére és jogkivonatok igénylésére az Azure AD-től. Eddig a legtöbb fejlesztő az Azure AD for developers platformmal (1.0-s) együttműködve hitelesítette az Azure AD-identitásokat (munkahelyi és iskolai fiókokat) az Azure AD hitelesítési könyvtár (ADAL) használatával történő jogkivonatok kérésével. Az MSAL használata:

- A Microsoft-identitások szélesebb körét (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi fiókok az Azure AD B2C-n keresztül) hitelesítheti, mivel a Microsoft identitásplatform-végpontot használja.
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják.
- Az alkalmazás engedélyezheti a növekményes jóváhagyást, és a feltételes hozzáférés támogatása egyszerűbb.
- Ön hasznot húz az innovációból.

**Az MSAL mostantól a Microsoft identity platformmal használható ajánlott hitelesítési könyvtár.** Az ADAL-on nem lesznek új funkciók. Az erőfeszítések az MSAL javítására összpontosítanak.

Az alábbi cikkek ismertetik az MSAL és az ADAL könyvtárak közötti különbségeket, és segítenek az MSAL-ra való áttelepítésben:
- [Migrálás MSAL.NET-re](msal-net-migration.md)
- [Migrálás MSAL.js-re](msal-compare-msal-js-and-adal-js.md)
- [Migrálás MSAL.Androidra](migrate-android-adal-msal.md)
- [Áttelepítés az MSAL.iOS / macOS rendszerre](migrate-objc-adal-msal.md)
- [Migrálás MSAL Pythonba](migrate-python-adal-msal.md)
- [Migrálás Javához készült MSAL-be](migrate-adal-msal-java.md)
- [Xamarin-alkalmazások migrálása közvetítők használatával MSAL.NET rendszerre](msal-net-migration-ios-broker.md)
