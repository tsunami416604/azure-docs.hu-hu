---
title: Mi az az Azure Static Web Apps?
description: Az Azure statikus Web Apps legfontosabb funkciói és funkciói.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 476b81fc9aceae3f05ed54e7b2b11be381121bd8
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488919"
---
# <a name="what-is-azure-static-web-apps-preview"></a>Mi az Azure statikus Web Apps előzetes verzió?

Az Azure statikus Web Apps egy olyan szolgáltatás, amely automatikusan létrehozza és üzembe helyezi a teljes értékű webalkalmazásokat az Azure-ban egy GitHub-adattárból.

:::image type="content" source="media/overview/static-apps-overview.png" alt-text="Statikus Web Apps áttekintése":::

Az Azure statikus Web Apps munkafolyamata egy fejlesztői napi munkafolyamatra van szabva. Az alkalmazásokat a GitHub-interakciók alapján építették és helyezik üzembe.

Amikor létrehoz egy Azure statikus Web Apps-erőforrást, az Azure beállítja a GitHub-műveletek munkafolyamatot az alkalmazás forráskód-tárházában, amely figyeli a választott ágat. Minden alkalommal, amikor leküldi vagy fogadja a lekéréses kérelmeket a figyelt ágra, a GitHub-művelet automatikusan létrehozza és telepíti az alkalmazást és az API-ját az Azure-ba.

A statikus webalkalmazások általában könyvtárak és keretrendszerek használatával készültek, mint például a szögletes, a reakciós, a karcsú vagy a Vue. Ezek az alkalmazások az alkalmazást alkotó HTML-, CSS-, JavaScript-és képobjektumokat tartalmazzák. A hagyományos webkiszolgálókkal ezeket az eszközöket a szükséges API-végpontok mellett egyetlen kiszolgáló szolgálja ki.

A statikus Web Apps a statikus eszközöket egy hagyományos webkiszolgálótól választja el, és helyette a világ földrajzilag elosztott pontjairól szolgálnak. Ez a terjesztés sokkal gyorsabban teszi lehetővé a fájlok kiszolgálását, mivel a fájlok fizikailag közelebb vannak a végfelhasználók számára. Emellett az API-végpontok [kiszolgáló nélküli architektúrával](../azure-functions/functions-overview.md)vannak tárolva, így elkerülhető, hogy a teljes háttér-kiszolgálót egyszerre kelljen használni.

## <a name="key-features"></a>A legfontosabb jellemzők

- **Webes üzemeltetés** statikus tartalmakhoz, például HTML-, CSS-, JavaScript-és rendszerképekhez.
- A Azure Functions által biztosított **integrált API** -támogatás.
- Az **első fél GitHub-integrációja** , ahol a tárház megváltoztatja a triggereket és a központi telepítéseket.
- **Globálisan elosztott** statikus tartalom, amely közelebb helyezi a tartalmat a felhasználókhoz.
- **Ingyenes SSL-tanúsítványok**, amelyek automatikusan megújulnak.
- **Egyéni tartományok** \* Ha márkás testreszabásokat kíván biztosítani az alkalmazás számára.
- **Zökkenőmentes biztonsági modell** fordított proxyval, ha olyan API-kat hív meg, amelyek nem igényelnek CORS-konfigurációt.
- A Azure Active Directory, a Facebook, a Google, a GitHub és a Twitter **szolgáltatást használó hitelesítésszolgáltató integrációja** .
- **Testreszabható engedélyezési szerepkör-definíció** és hozzárendelések.
- A **háttér-útválasztási szabályok** lehetővé teszik a teljes körű irányítást a kiszolgált tartalom és útvonalak felett.
- A lekéréses kérelmek által **generált átmeneti verziók** a közzététel előtt lehetővé teszik a hely előzetes verziójának kiadását.

## <a name="what-you-can-do-with-static-web-apps"></a>Mit tehet a statikus Web Apps

- **Modern JavaScript-alkalmazásokat készíthet** olyan keretrendszerekkel és tárakkal, mint például a [szögletes](https://angular.io/), a [reakciós](https://reactjs.org/), a [karcsú](https://svelte.dev/), a [Vue](https://vuejs.org/) [Azure functions](https://azure.microsoft.com/services/functions/) háttérrel.
- **Statikus helyeket tehet közzé** olyan keretrendszerekkel, mint például a [Gatsby](publish-gatsby.md), a [Hugo](publish-hugo.md), a [VuePress](publish-vuepress.md).
- **Webalkalmazások üzembe helyezése** olyan keretrendszerekkel, mint a [Next. js](deploy-nextjs.md) és a [Nuxt. js](deploy-nuxtjs.md).

\*Az előzetes verzióban nem támogatott a APEX-tartomány regisztrációja.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első statikus alkalmazás létrehozása](getting-started.md)
