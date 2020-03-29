---
title: fájl belefoglalása
description: tartalmazza a fájlt a bizalmas ügyfél forgatókönyv céloldalak (démon, web app, web API)
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
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773382"
---
## <a name="register-secrets-or-certificates"></a>Titkos kulcsok vagy tanúsítványok regisztrálása

Ami a bizalmas ügyfélalkalmazást illeti, regisztrálnia kell egy titkos kulcsot vagy tanúsítványt. Az alkalmazástitkos kulcsok regisztrálhatók az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) az interaktív felületen vagy a parancssori eszközök (például a PowerShell) használatával.

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Ügyféltitok regisztrálása az alkalmazásregisztrációs portál használatával

Az ügyfél hitelesítő adatainak kezelése az alkalmazások **tanúsítványok & titkos kulcsok** lapján történik:

![Tanúsítványok & titkos kulcsok lap](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Az alkalmazás titkos (más néven az ügyfél titkos) az Azure AD által létrehozott a bizalmas ügyfélalkalmazás regisztrációja során. Ez a generáció akkor történik, ha az Új ügyféltitok lehetőséget **választja.** Ezen a ponton át kell másolnia a titkos karakterláncot a vágólapra, hogy az alkalmazásban **használhassa,** mielőtt a Mentés lehetőséget választaná. Ez a karakterlánc nem jelenik meg többé.
- Az alkalmazás regisztrációja során a **Tanúsítvány feltöltése** gombbal töltheti fel a tanúsítványt. Az Azure AD csak azokat a tanúsítványokat támogatja, amelyek közvetlenül regisztráltak az alkalmazásban, és nem követik a tanúsítványláncokat.

További információt a [Rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez | Adja hozzá a hitelesítő adatokat az alkalmazáshoz.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="register-client-secrets-by-using-powershell"></a>Ügyféltitok regisztrálása a PowerShell használatával

Azt is megteheti, hogy regisztrálja az alkalmazást az Azure AD parancssori eszközök használatával. Az [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) minta bemutatja, hogyan regisztrálhat egy alkalmazástitkos kulcsot vagy tanúsítványt egy Azure AD-alkalmazással:

- Az alkalmazástitkos adatok regisztrálásáról az [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)című témakörben talál részleteket.
- A tanúsítvány alkalmazással történő regisztrálásával kapcsolatos részletek az [AppCreationScripts-withCert/Configure.ps1 című témakörben vannak.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
