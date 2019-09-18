---
title: Web API-kat hívó Daemon-alkalmazás (áttérés éles környezetbe) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre a webes API-kat meghívó Daemon-alkalmazást (az éles környezetbe)
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c509e061c43c81f72682fb428529a8e72b34066a
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056325"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó Daemon-alkalmazás – áttérés éles környezetbe

Most, hogy már tudja, hogyan szerezheti be és használhatja a szolgáltatás-szolgáltatás hívására szolgáló tokent, megtudhatja, hogyan helyezheti át az alkalmazást éles környezetbe.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Üzembe helyezés – több-bérlős Daemon-alkalmazások esetén

Ha olyan Daemon-alkalmazást hoz létre, amely több bérlőn is futtatható, meg kell győződnie arról, hogy a bérlői rendszergazdák:

- Egy egyszerű szolgáltatásnév kiosztása az alkalmazáshoz
- Hozzájárulást biztosít az alkalmazáshoz

Meg kell magyaráznia ügyfeleinek, hogy miként hajtják végre ezeket a műveleteket. További információ: a teljes bérlő belefoglalásának [kérelmezése](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

Íme néhány hivatkozás, amely további információkat tartalmaz:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Ha még nem tette meg, próbálkozzon a gyors üzembe helyezési lehetőséggel, [és hívja meg Microsoft Graph API-t egy, az alkalmazás identitását használó Console-alkalmazásból](./quickstart-v2-netcore-daemon.md).
- Dokumentáció a következőhöz:
  - [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) példányának példányai
  - [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) meghívása
- Egyéb minták/oktatóanyagok:
  - [Microsoft-Identity-platform-Console-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) egy egyszerű .net Core Daemon Console-alkalmazás, amely megjeleníti a bérlő felhasználóit, és lekérdezi a Microsoft Graph.

    ![topológia](media/scenario-daemon-app/daemon-app-sample.svg)

    Ugyanez a minta is szemlélteti a tanúsítványokkal való változást.

    ![topológia](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - a [Microsoft-Identity-platform-ASPNET-WebApp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) egy ASP.net MVC-webalkalmazást tartalmaz, amely az alkalmazás identitásával szinkronizálja az Microsoft Graph adatait a felhasználó nevében. A minta a rendszergazdai engedélyezési folyamatot is szemlélteti.

    ![topológia](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

A MSAL Python jelenleg nyilvános előzetes verzióban érhető el.
További információ: [MSAL Python in-repository Samples](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample).

# <a name="javatabjava"></a>[Java](#tab/java)

msal4j (MSAL. A Java) jelenleg nyilvános előzetes verzióban érhető el. További információ: [MSAL Java in-repository Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
