---
title: Egyoldalas alkalmazások regisztrálása – Microsoft identity platform | Azure
description: Az egyoldalas alkalmazások létrehozásáról (alkalmazásregisztráció)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 6f690a8b3436a45d434ccad2bbaa7d2a1b0b76aa
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882148"
---
# <a name="single-page-application-app-registration"></a>Egyoldalas jelentkezés: Alkalmazásregisztráció

Ez az oldal ismerteti az egyoldalas alkalmazásregisztrációs adatokat egy egyoldalas alkalmazáshoz (SPA).

Kövesse az [új alkalmazás regisztrálásához a Microsoft identity platformmal való regisztrálásához,](quickstart-register-app.md)és válassza ki az alkalmazás támogatott fiókjait. A SPA-forgatókönyv támogatja a hitelesítést a szervezetben vagy bármely szervezetben lévő és személyes Microsoft-fiókkal.

Ezután ismerje meg az egyoldalas alkalmazásokra vonatkozó alkalmazásregisztráció konkrét szempontjait.

## <a name="register-a-redirect-uri"></a>Átirányítási URI regisztrálása

Az implicit folyamat elküldi a jogkivonatokat egy átirányításban a webböngészőben futó egyoldalas alkalmazásba. Ezért fontos, hogy regisztráljon egy átirányítási URI-t, ahol az alkalmazás fogadhatja a jogkivonatokat. Győződjön meg arról, hogy az átirányítási URI pontosan megegyezik az alkalmazás URI-jának.

Az [Azure Portalon](https://go.microsoft.com/fwlink/?linkid=2083908)nyissa meg a regisztrált alkalmazás. Az alkalmazás **Hitelesítés** lapján válassza ki a **webes** platformot. Adja meg az alkalmazás átirányítási URI-értékét az **Átirányítás URI** mezőjében.

## <a name="enable-the-implicit-flow"></a>Az implicit folyamat engedélyezése

Ugyanazon a **Hitelesítés** lapon a **Speciális beállítások**csoportban engedélyeznie kell az Implicit **támogatást**is. Ha az alkalmazás csak a felhasználók bejelentkezése és az azonosító jogkivonatok beszerzése, elég, hogy jelölje be az **azonosító jogkivonatokat jelölőnégyzetet.**

Ha az alkalmazásnak hozzáférési jogkivonatokat is be kell szereznie az API-k hívásához, győződjön meg arról, hogy jelölje be az **Access-jogkivonatok jelölőnégyzetet** is. További információt az [Azonosító-jogkivonatok](./id-tokens.md) és [az Access-jogkivonatok című témakörben talál.](./access-tokens.md)

## <a name="api-permissions"></a>API-engedélyek

Az egyoldalas alkalmazások a bejelentkezett felhasználó nevében api-kat hívhatnak. Delegált engedélyeket kell kérniük. További információt a [Webes API-k hozzáférési engedélyeinek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)című témakörben talál.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódkonfigurációja](scenario-spa-app-configuration.md)
