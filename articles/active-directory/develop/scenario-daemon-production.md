---
title: Webes API-kat meghívó Daemon-alkalmazás áthelyezése az éles környezetbe – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan helyezhet át egy olyan Daemon-alkalmazást, amely webes API-kat hív meg éles környezetben
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: db5f52c95daf4e93c140b4c93f39dad19971319d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262619"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó Daemon-alkalmazás – áttérés éles környezetbe

Most, hogy már tudja, hogyan szerezheti be és használhatja a szolgáltatás-szolgáltatás hívására szolgáló tokent, megtudhatja, hogyan helyezheti át az alkalmazást éles környezetbe.

## <a name="deployment---multitenant-daemon-apps"></a>Üzembe helyezés – több-bérlős Daemon-alkalmazások

Ha olyan Daemon-alkalmazást hoz létre, amely több bérlőn is futtatható, meg kell győződnie arról, hogy a bérlői rendszergazda:

- Kiépít egy egyszerű szolgáltatást az alkalmazáshoz.
- Hozzájárulást biztosít az alkalmazáshoz.

Meg kell magyaráznia ügyfeleinek, hogy miként hajtják végre ezeket a műveleteket. További információ: a teljes bérlő belefoglalásának [kérelmezése](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Következő lépések

Íme néhány hivatkozás, amely segítséget nyújt a további információk megismeréséhez:

# <a name="net"></a>[.NET](#tab/dotnet)

- Gyors útmutató: [token beszerzése és Microsoft Graph API meghívása egy konzol alkalmazásból az alkalmazás identitásával](./quickstart-v2-netcore-daemon.md).
- Dokumentáció a következőhöz:
  - [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)-példányok.
  - [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)meghívása.
- Egyéb minták/oktatóanyagok:
  - [Microsoft-Identity-platform-Console-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) egy egyszerű .net Core Daemon Console-alkalmazás, amely megjeleníti a bérlők lekérdezési Microsoft Graph felhasználóit.

    ![Minta Daemon-alkalmazás topológiája](media/scenario-daemon-app/daemon-app-sample.svg)

    Ugyanez a minta a tanúsítványokkal való változást is szemlélteti:

    ![Minta Daemon-alkalmazás topológiája – tanúsítványok](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - a [Microsoft-Identity-platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) egy ASP.net MVC-webalkalmazást tartalmaz, amely a felhasználó nevében való használat helyett az alkalmazás identitásával szinkronizálja Microsoft Graph adatait. Ez a minta a rendszergazdai engedélyezési folyamatot is szemlélteti.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Próbálja ki [, hogyan szerezzen be tokent, és hívja meg Microsoft Graph API-t egy Python-konzol alkalmazásból az alkalmazás identitásával](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

A MSAL Java jelenleg nyilvános előzetes verzióban érhető el. További információ: [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
