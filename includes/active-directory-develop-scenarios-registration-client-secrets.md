---
title: fájl belefoglalása
description: adatfájl (démon, webalkalmazás, webes API-t) a főoldalakon bizalmas ügyfél forgatókönyv
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074650"
---
## <a name="registration-of-secrets-or-certificates"></a>Titkos kódok és tanúsítványok regisztrálása

Mint minden bizalmas ügyfélalkalmazáshoz, regisztrálnia kell egy titkos kulcsot vagy a tanúsítványt. A titkos alkalmazáskulcsok keresztül interaktív felhasználói élménye is regisztrálni a [az Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), vagy a parancssori eszközök (például a PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Ügyfél titkok az alkalmazásregisztrációs portálon regisztrálása

Az ügyfél-hitelesítő adatok kezelése történik a **tanúsítványok és titkos kulcsok** oldal egy alkalmazás:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- az Azure AD-ben a bizalmas ügyfélalkalmazás a regisztráció során hozza létre az alkalmazás titkos (is a megnevezett ügyfél titkos kódot). Ezt a létrehozást történik, ha kiválasztja **új titkos ügyfélkulcsot**. Ezen a ponton kell másolnia a titkos karakterláncot a vágólapra az alkalmazásában, kiválasztása előtt **mentése**. Ez a karakterlánc többé nem kell bemutatni.
- a tanúsítvány feltöltése az alkalmazás regisztrációs felületén a **tanúsítvány feltöltése** gomb

További információkért lásd: [a rövid útmutató: Webes API-k eléréséhez ügyfélalkalmazás konfigurálása |} Hitelesítő adatok hozzáadása az alkalmazáshoz](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Regisztrálás az ügyfél titkos kulcsok a PowerShell használatával

Az alkalmazás azt is megteheti, regisztrálhat az Azure ad-vel parancssori eszközök használatával. A [active-directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) minta megmutatja, hogyan szeretne regisztrálni Azure AD-alkalmazást egy alkalmazás titkos kulcs vagy egy tanúsítvány:

- Egy alkalmazás titkos regisztrálásával kapcsolatos részletekért lásd: [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- További információ a tanúsítvány regisztrálása az alkalmazással: [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)