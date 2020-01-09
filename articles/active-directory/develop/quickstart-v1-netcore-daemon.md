---
title: Token beszerzése & hív Microsoft Graph (.NET Core Console) (v 1.0) | Azure
description: Az Azure & AD-vel integrált .NET Daemon-alkalmazás létrehozása, amely az Azure AD által védett API-kat hívja meg a OAuth 2,0 használatával
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d44dfe3eb03ff086d3785311c34ab1a6a5b3982a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424054"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Gyors útmutató: jogkivonatok beszerzése és hívása Microsoft Graph a konzol alkalmazás identitásával (v 1.0)

A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.

A [Microsoft Authentication Library (MSAL)](msal-overview.md) lehetővé teszi a fejlesztők számára a tokenek beszerzését a Microsoft Identity platform-végpontból a biztonságos webes API-k eléréséhez. Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik.

## <a name="next-steps"></a>Következő lépések

Az új .NET Daemon-alkalmazások esetében javasoljuk, hogy használja a Microsoft Identity platformot (v 2.0) és a MSAL a jogkivonatok beszerzésére és a biztonságos webes API-k elérésére: gyors útmutató [: token beszerzése és Microsoft Graph API meghívása egy alkalmazás identitásával a konzol alkalmazásban](quickstart-v2-netcore-daemon.md).
