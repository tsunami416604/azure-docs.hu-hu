---
title: Webes API-kat gyűjtő démonalkalmazás áthelyezése éles környezetbe – Microsoft identity platform | Azure
description: További információ a webes API-kat éles környezetbe hívó démonalkalmazások áthelyezéséről
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262619"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>A webes API-kat meghívjad Daemon alkalmazás – ugrás éles környezetbe

Most, hogy már tudja, hogyan szerezhet be és használhat jogkivonatot egy szolgáltatás-szolgáltatás híváshoz, ismerje meg, hogyan helyezheti át az alkalmazást éles környezetbe.

## <a name="deployment---multitenant-daemon-apps"></a>Központi telepítés - több-bérlős démonalkalmazások

If you're an ISV creating a daemon application that can run in several tenants, you need to make sure that the tenant admin:

- Szolgáltatásnévállatás az alkalmazáshoz.
- Hozzájárul a kérelemhez.

El kell magyaráznia az ügyfeleknek, hogyan hajtsák végre ezeket a műveleteket. További információ: [Hozzájárulás kérése egy teljes bérlőhöz.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>További lépések

Íme néhány link, amelyek segítenek többet megtudni:

# <a name="net"></a>[.NET](#tab/dotnet)

- Gyorsútmutató: [Jogkivonat beszerzése és a Microsoft Graph API hívása egy konzolalkalmazásból az alkalmazás identitásának használatával.](./quickstart-v2-netcore-daemon.md)
- Referenciadokumentáció:
  - [A ConfidentialClientApplication példányosítása](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - [AcquiretokenForclient hívása.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Egyéb minták/oktatóanyagok:
  - [A microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) egy egyszerű .NET Core démonkonzolalkalmazást tartalmaz, amely megjeleníti a Microsoft Graph-ot lekérdező bérlő felhasználóit.

    ![Mintadémon-alkalmazás topológia](media/scenario-daemon-app/daemon-app-sample.svg)

    Ugyanez a minta a tanúsítványokkal rendelkező változatokat is szemlélteti:

    ![Mintadémon-alkalmazás topológiája – tanúsítványok](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [A microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) egy ASP.NET MVC webalkalmazással rendelkezik, amely a Microsoft Graph adatait az alkalmazás identitását használja a felhasználó nevében. Ez a minta a rendszergazdai jóváhagyási folyamatot is szemlélteti.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Próbálja ki a [gyorsútmutatót Jogkivonat beszerzése és a Microsoft Graph API hívása python-konzolalkalmazásból az alkalmazás identitásának használatával.](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

Az MSAL Java jelenleg nyilvános előzetes verzióban érhető el. További információ: [MSAL Java dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
