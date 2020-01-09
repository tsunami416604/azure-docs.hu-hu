---
title: Egyoldalas alkalmazások regisztrálása – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (alkalmazás-regisztráció)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964d4b4c7032599cf8f74b285f819581fae907b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423659"
---
# <a name="single-page-application-app-registration"></a>Egyoldalas alkalmazás: alkalmazás regisztrálása

Ezen a lapon megtudhatja, hogy egy egyoldalas alkalmazás (SPA) mely alkalmazás-regisztrációs sajátosságai vannak.

A lépéseket követve [regisztrálhat egy új alkalmazást a Microsoft Identity platformon](quickstart-register-app.md), és kiválaszthatja az alkalmazás támogatott fiókját. A SPA-forgatókönyv támogatja a hitelesítést a szervezetében lévő fiókokkal, illetve bármely szervezet és személyes Microsoft-fiókkal.

Következő lépésként Ismerkedjen meg az alkalmazások regisztrációjának adott szempontjaival, amelyek az egyoldalas alkalmazásokra vonatkoznak.

## <a name="register-a-redirect-uri"></a>Átirányítási URI regisztrálása

Az implicit folyamat átirányítja a jogkivonatokat egy webböngészőn futó egyoldalas alkalmazásba. Ezért fontos regisztrálni egy átirányítási URI-t, amelyben az alkalmazás megkapja a jogkivonatokat. Győződjön meg arról, hogy az átirányítási URI pontosan megegyezik az alkalmazás URI azonosítóval.

A [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)lépjen a regisztrált alkalmazáshoz. Az alkalmazás **hitelesítés** lapján válassza **ki a** webplatformot. Az **átirányítási URI mezőben adja** meg az alkalmazás ÁTIRÁNYÍTÁSI URI azonosítóját.

## <a name="enable-the-implicit-flow"></a>Az implicit folyamat engedélyezése

Ugyanazon a **hitelesítési** lapon a **Speciális beállítások**területen engedélyeznie kell az **implicit engedélyezést**is. Ha az alkalmazás csak a felhasználókba jelentkezik be, és azonosító jogkivonatokat kap, akkor elég kijelölni az **azonosító tokeneket** jelölőnégyzetet.

Ha az alkalmazásnak hozzáférési tokeneket is kell kérnie az API-k meghívásához, jelölje be a **hozzáférési tokenek** jelölőnégyzetet is. További információ: [azonosító jogkivonatok](./id-tokens.md) és [hozzáférési jogkivonatok](./access-tokens.md).

## <a name="api-permissions"></a>API-engedélyek

Az egyoldalas alkalmazások a bejelentkezett felhasználó nevében hívhatnak API-kat. Delegált engedélyeket kell kérniük. Részletekért lásd: [a webes API-k eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-spa-app-configuration.md)
