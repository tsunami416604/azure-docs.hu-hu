---
title: Webalkalmazás hozzáadása – Azure Active Directory B2C | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá webalkalmazásokat a Active Directory B2C bérlőhöz.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50a4ead58cc70524ec464e52ce546b36f9685df5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064536"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Webes API-alkalmazás hozzáadása a Azure Active Directory B2C-bérlőhöz

 Regisztrálja webes API-erőforrásait a bérlőben, hogy elfogadják és válaszoljanak a hozzáférési jogkivonatot futtató ügyfélalkalmazások által benyújtott kérelmekre. Ez a cikk bemutatja, hogyan regisztrálhatja az alkalmazást Azure Active Directory B2Cban (Azure AD B2C).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
5. Adja meg az alkalmazás nevét. Például: *webapi1*.
6. A **Web App/web API** belefoglalása és az **implicit folyamat engedélyezése**lehetőségnél válassza az **Igen**lehetőséget.
7. A **Válasz URL-cím**mezőben adjon meg egy végpontot, amelyben Azure ad B2C vissza kell adnia az alkalmazás által kért jogkivonatokat. Az éles alkalmazásban beállíthatja a válasz URL-címét egy értékre (például `https://localhost:44332`). Tesztelési célból állítsa be a válasz URL- `https://jwt.ms`címét a következőre:.
8. Az **alkalmazás-azonosító URI-ja**mezőben adja meg a webes API-hoz használt azonosítót. A teljes azonosító URI-t, a tartománnyal együtt, a rendszer hozza létre. Például: `https://contosotenant.onmicrosoft.com/api`.
9. Kattintson a **Create** (Létrehozás) gombra.
10. A Tulajdonságok lapon jegyezze fel az alkalmazás AZONOSÍTÓját, amelyet a webalkalmazás konfigurálásakor használni fog.

## <a name="configure-scopes"></a>Hatókörök konfigurálása

A hatókörök lehetővé teszik a védett erőforrásokhoz való hozzáférés szabályozását. A hatóköröket a webes API a hatóköralapú hozzáférés-vezérlés megvalósításához használja. A webes API-k bizonyos felhasználói például rendelkezhetnek olvasási és írási hozzáféréssel is, míg mások csak olvasási hozzáféréssel. Ebben az oktatóanyagban hatókörök segítségével határozzuk meg az olvasási és írási engedélyeket a webes API számára.

1. Válassza az **alkalmazások**, majd a *webapi1*lehetőséget.
2. Válassza a **közzétett hatókörök**elemet.
3. A **hatókör**, `Read`a és a Leírás mezőbe írja be `Read access to the application`a következőt:.
4. A **hatókör**, `Write`a és a Leírás mezőbe írja be `Write access to the application`a következőt:.
5. Kattintson a **Save** (Mentés) gombra.

A közzétett hatókörök segítségével ügyfélalkalmazások számára engedélyezhető a webes API-hoz való hozzáférés.

## <a name="grant-permissions"></a>Engedélyek megadása

Ha egy védett webes API-t szeretne meghívni egy alkalmazásból, meg kell adnia az alkalmazás engedélyeit az API-nak. Például az [oktatóanyagban: Alkalmazás regisztrálása a Azure Active Directory B2C](tutorial-register-applications.md)-ben egy webalkalmazás jön létre a *webapp1*nevű Azure ad B2C. Ezt az alkalmazást használhatja a webes API meghívásához.

1. Válassza az **alkalmazások**lehetőséget, majd válassza ki a webalkalmazást.
2. Válassza az **API-hozzáférés**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Az **API kiválasztása** legördülő menüben válassza a *webapi1*lehetőséget.
4. A **hatókörök kiválasztása** legördülő menüben válassza ki a korábban definiált **olvasási** és **írási** hatóköröket.
5. Kattintson az **OK** gombra.

Az alkalmazás regisztrálva van a védett webes API meghívásához. A felhasználók a Azure AD B2C használatával hitelesítik az alkalmazást. Az alkalmazás a védett webes API-hoz való hozzáféréshez Azure AD B2C engedélyezési engedélyt kap.
