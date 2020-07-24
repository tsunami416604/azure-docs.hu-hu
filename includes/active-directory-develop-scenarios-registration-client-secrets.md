---
title: fájl belefoglalása
description: fájl belefoglalása a bizalmas ügyfél-forgatókönyvekhez (démon, webalkalmazás, webes API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102717"
---
## <a name="register-secrets-or-certificates"></a>Titkok vagy tanúsítványok regisztrálása

Mint minden bizalmas ügyfélalkalmazás, regisztrálnia kell egy titkos vagy egy tanúsítványt. Az alkalmazási titkokat a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) interaktív felületén vagy parancssori eszközökkel (például a PowerShell-lel) is regisztrálhatja.

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Az ügyfél titkos kulcsainak regisztrálása az alkalmazás regisztrációs portáljának használatával

Az ügyfél hitelesítő adatainak kezelése az alkalmazások **tanúsítványainak & Secrets** lapján történik:

![Tanúsítványok & Secrets oldal](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Az *ügyfél titkos kulcsának* létrehozásához válassza az **új ügyfél titkot** az alkalmazás regisztrációjában a Azure Portal. Amikor létrehozza az ügyfél titkos kulcsát, a titkos karakterláncot a **tanúsítványok & titkok** panelről való navigálás előtt _kell_ rögzítenie. A titkos kód karakterlánca soha nem jelenik meg újra.
- Az alkalmazás regisztrálása során a **tanúsítvány feltöltése** gombra kattintva töltheti fel a tanúsítványt. Az Azure AD csak azokat a tanúsítványokat támogatja, amelyek közvetlenül regisztrálva vannak az alkalmazásban, és nem követik a tanúsítványláncot.

Részletekért lásd: rövid útmutató [: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez | Adja meg a hitelesítő adatokat az alkalmazáshoz](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Ügyfél-titkok regisztrálása a PowerShell használatával

Azt is megteheti, hogy az alkalmazást a parancssori eszközök használatával regisztrálja az Azure AD-ben. Az [Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) minta bemutatja, hogyan regisztrálhat egy alkalmazás titkát vagy tanúsítványát egy Azure ad-alkalmazással:

- Az alkalmazás titkos kódjának regisztrálásával kapcsolatos további információkért lásd: [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- A tanúsítványok alkalmazással való regisztrálásával kapcsolatos részletekért lásd: [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
