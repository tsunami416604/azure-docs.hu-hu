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
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164931"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Alkalmazások migrálása a Microsoft hitelesítési tárba (MSAL)

A Microsoft Authentication Library (MSAL) és az Azure AD Authentication Library (ADAL) egyaránt az Azure AD-entitások hitelesítésére és az Azure AD-jogkivonatok igénylésére szolgál. Eddig a legtöbb fejlesztő dolgozott együtt az Azure ad for Developers platformmal (v 1.0) az Azure AD-identitások (munkahelyi és iskolai fiókok) hitelesítéséhez az Azure AD Authentication Library (ADAL) használatával. A MSAL használata:

- A Microsoft Identity platform végpontjának használatával a Microsoft-identitások (Azure AD-identitások és Microsoft-fiókok, valamint közösségi és helyi Azure AD B2C fiókok) szélesebb körét lehet hitelesíteni.
- A felhasználók a legjobb egyszeri bejelentkezési élményt kapják meg.
- Az alkalmazás lehetővé teszi a növekményes hozzáférést, és megkönnyíti a feltételes hozzáférés támogatását.
- Élvezheti az innováció előnyeit.

A **MSAL mostantól a Microsoft Identity platformmal való használatra javasolt hitelesítési függvénytár**. A ADAL-on nem lesznek új funkciók implementálva. Az erőfeszítések a MSAL javítására összpontosítanak.

A következő cikkek ismertetik a MSAL és a ADAL kódtára közötti különbségeket, és segítséget nyújtanak a MSAL-re való Migrálás során:
- [Migrálás MSAL.NET-re](msal-net-migration.md)
- [Migrálás MSAL.js-re](msal-compare-msal-js-and-adal-js.md)
- [Migrálás MSAL.Androidra](migrate-android-adal-msal.md)
- [Migrálás MSAL.iOS vagy macOS rendszerre](migrate-objc-adal-msal.md)
- [Migrálás MSAL Pythonba](migrate-python-adal-msal.md)
- [Migrálás Javához készült MSAL-be](migrate-adal-msal-java.md)
- [Xamarin-alkalmazások migrálása közvetítők használatával MSAL.NET rendszerre](msal-net-migration-ios-broker.md)
