---
title: Asztali alkalmazás, hogy a hívások webes API-k (áttekintés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, hogy a hívások webes API-k (áttekintés)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 204e230f7ff0897d2ba97213ebc836aff9d0324b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204650"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Forgatókönyv: Webes API-kat hívó asztali alkalmazás

Ismerje meg, hogy egy asztali alkalmazás, amely meghívja a webes API-kat kell létrehoznia az összes

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Ha még nem tette, az első alkalmazás létrehozása a .NET asztali rövid vagy a UWP rövid útmutatót követve:

> [!div class="nextstepaction"]
> [Rövid útmutató: Egy token beszerzéséhez és a Microsoft Graph API hívása Windows asztali alkalmazások](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Rövid útmutató: A token beszerzéséhez és a Microsoft Graph API hívása egy UWP-alkalmazás](./quickstart-v2-uwp.md)

## <a name="overview"></a>Áttekintés

Asztali alkalmazások írása, és a felhasználók az alkalmazáshoz, és a webes API-k, például a Microsoft Graph, más Microsoft APIs vagy saját webes API hívása. Több lehetőség közül választhat:

- Ha az asztali alkalmazás támogatja a grafikus szabályozását, például ha egy Windows.Form alkalmazást vagy aplikaci WPF, használhatja az interaktív token beszerzéséhez.
- Üzemeltetett Windows-alkalmazások a Windows-tartományhoz csatlakoztatott számítógépeken futó alkalmazások számára lehetőség arra is, vagy AAD csatlakoztatott jogkivonat-beszerzési csendes integrált Windows-hitelesítés használatával.
- Végül és noha nem ajánlott, de használhatja a nyilvános ügyfélalkalmazások felhasználónév/jelszó. Bizonyos esetekben (például DevOps) továbbra is szükséges, de ügyeljen arra, hogy használja azt fogja kivetett korlátozásokat az alkalmazás. Például, nem tud bejelentkezni a felhasználó, aki kell végrehajtania a multi-factor authentication (feltételes hozzáférés). Emellett az alkalmazás nem kihasználhatják egyszeri bejelentkezés (SSO).

  Azt is nem felel meg a modern hitelesítés elveit és a csak az örökölt okok miatt.

  ![Asztali alkalmazás](media/scenarios/desktop-app.svg)

- Ha szeretne írni egy hordozható parancssori eszköz – valószínűleg egy Linux vagy Mac rendszeren futó .NET Core-alkalmazást – nem tudja használni valamelyik az interaktív hitelesítés (mert nem biztosít a .NET Core egy [webböngésző](https://aka.ms/msal-net-uses-web-browser)), vagy integrált Windows-hitelesítés. A legjobb lehetőség ebben az esetben az eszköz kódfolyamat használja. Ez a folyamat egy böngészőbe, például az IoT-alkalmazások anélkül alkalmazásokhoz is használható

  ![Browserless alkalmazás](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Tulajdonságairól

Asztali alkalmazások jellemzőit, számú Ez főleg függ attól az alkalmazás használ-e az interaktív hitelesítéshez, vagy nem rendelkezik.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazás - alkalmazásregisztráció](scenario-desktop-app-registration.md)
