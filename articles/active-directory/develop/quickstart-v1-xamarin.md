---
title: Azure AD-Xamarin – első lépések | Microsoft Docs
description: Az Azure AD-vel integrált Xamarin-alkalmazásokat készíthet a bejelentkezéshez, és meghívja az Azure AD által védett API-kat a OAuth használatával.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6197a27b9a94de0f9a10407215e5755689cd0b56
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920820"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Gyors útmutató: Microsoft-bejelentkezést integráló Xamarin-alkalmazás létrehozása

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

## <a name="next-steps"></a>Következő lépések

Az új Xamarin alkalmazások esetében javasoljuk, hogy a Microsoft Identity platformot (v 2.0) és a MSAL használja a jogkivonatok beszerzéséhez és a biztonságos webes API-k eléréséhez. Az első lépésekhez lásd: a [Microsoft Identity és a Microsoft Graph integrálása egy Xamarin Forms-alkalmazásba a MSAL használatával](https://github.com/azure-samples/active-directory-xamarin-native-v2#integrate-microsoft-identity-and-the-microsoft-graph-into-a-xamarin-forms-app-using-msal) (a választható lépések nélkül).
