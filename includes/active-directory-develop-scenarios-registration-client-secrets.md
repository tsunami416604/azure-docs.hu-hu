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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8f98808aa0f8a2c32e2117447824114747091a82
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912385"
---
## <a name="registration-of-secrets-or-certificates"></a>Titkok vagy tanúsítványok regisztrálása

A bizalmas ügyfélalkalmazások esetében hasonlóan regisztrálnia kell egy titkos vagy egy tanúsítványt. Az alkalmazási titkokat a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)interaktív felületén vagy parancssori eszközökkel (például a PowerShell-lel) is regisztrálhatja.

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Ügyfél-titkok regisztrálása az alkalmazás regisztrációs portálján

Az ügyfél hitelesítő adatainak kezelése a **tanúsítványok & Secrets** oldalon történik egy alkalmazáshoz:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Az Azure AD a bizalmas ügyfélalkalmazás regisztrálása során létrehozta az alkalmazás titkos kulcsát (más néven az ügyfél titkát). Ez a létrehozás akkor történik meg, amikor **új ügyfél-titkos kulcsot**választ. Ezen a ponton a **Save (Mentés) gombra**kattintva a vágólapon lévő titkos karakterláncot kell átmásolnia az alkalmazásban való használathoz. Ez a karakterlánc többé nem jelenik meg.
- a tanúsítvány feltöltése az alkalmazás regisztrálásával történik a **tanúsítvány feltöltése** gomb használatával. Az Azure AD csak az alkalmazásban közvetlenül regisztrált tanúsítványokat támogatja, és nem követi a tanúsítványkezelőket.

Részletekért lásd: [gyors útmutató: Ügyfélalkalmazás konfigurálása a webes API-k eléréséhez | Hitelesítő adatok hozzáadása az alkalmazáshoz](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Ügyfél-titkok regisztrálása a PowerShell-lel

Azt is megteheti, hogy az alkalmazást az Azure AD-ben a parancssori eszközök használatával regisztrálja. Az [Active-Directory-dotnetcore-Daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) minta bemutatja, hogyan regisztrálhat egy alkalmazás titkát vagy tanúsítványt egy Azure ad-alkalmazással:

- Az alkalmazás titkos kódjának regisztrálásával kapcsolatos további információkért lásd: [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- A tanúsítványok alkalmazással való regisztrálásával kapcsolatos további információkért lásd: [AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
