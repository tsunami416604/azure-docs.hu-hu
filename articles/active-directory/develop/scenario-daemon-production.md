---
title: Webes API-k (Váltás az éles környezetben) – a Microsoft identity platform démon alkalmazás hívása
description: Ismerje meg, hogyan hozhat létre egy démon alkalmazást, hogy a hívások webes API-k (Váltás az éles környezetben)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545402"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Démon alkalmazás, amely meghívja a webes API-k – helyezze át az éles környezetbe

Most, hogy tudja, hogyan szeretné beszerezni és a egy szolgáltatások közötti hívások jogkivonat használata, ismerje meg, hogyan helyezheti át az alkalmazás az éles környezetbe.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Üzembe helyezés – több-bérlős démon alkalmazások esetében

Ha Ön egy démon alkalmazást, amely képes futni számos bérlők egy független, győződjön meg arról, hogy kell a bérlői rendszergazdák:

- Egy egyszerű szolgáltatást az alkalmazás kiépíti
- Támogatás beleegyezik abba, hogy az alkalmazás

Az ügyfelek számára bemutatják, hogyan hajtsa végre ezeket a műveleteket kell. További információ: [hozzájárulás kérése egy teljes bérlő](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

Az alábbiakban néhány további hivatkozásokat:

### <a name="net"></a>.NET

- Ha még nem tette, próbálja meg a rövid útmutató [egy token beszerzéséhez és a Microsoft Graph API hívása egy konzolalkalmazás használatával az alkalmazás identitását](./quickstart-v2-netcore-daemon.md).
- Segédanyagok:
  - Hárítható el [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Hívó [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Más minták és oktatóanyagok:
  - [a Microsoft-identitás-platform-konzol-démon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) funkciókat egy egyszerű .NET Core démon konzolalkalmazást, amely jelennek meg a felhasználók a Microsoft Graph lekérdezése egy bérlő.

    ![topology](media/scenario-daemon-app/daemon-app-sample.svg)

    Egyazon mintából is szemlélteti a változat tanúsítványokkal.

    ![topology](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) funkciók, amelyek az adatok a Microsoft Graph használatával egy felhasználó nevében helyett az alkalmazás identitását az ASP.NET MVC webalkalmazás. A minta is a rendszergazdai jóváhagyás folyamatát mutatja be.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

Az MSAL Python jelenleg nyilvános előzetes verzióban érhető el. További információ: [MSAL Python ügyfél hitelesítő adatai a tárház minta](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

Az MSAL Python jelenleg nyilvános előzetes verzióban érhető el. További információ: [MSAL Java-adattárban található példák](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).