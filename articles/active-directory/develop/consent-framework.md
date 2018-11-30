---
title: Az Azure Active Directory hozzájárulási keretrendszer
description: További információ az Azure Active Directory és az hogyan, megkönnyíti a több-bérlős webes és natív ügyfélalkalmazások fejlesztéséhez a hozzájárulási keretrendszer.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: celested
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: 2f9cefa31b007cae715ff2ea98bccb3112babbef
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619792"
---
# <a name="azure-active-directory-consent-framework"></a>Az Azure Active Directory hozzájárulási keretrendszer

Az Azure Active Directory (Azure AD) hozzájárulási keretrendszer megkönnyíti a több-bérlős webes és natív ügyfélalkalmazások fejlesztéséhez. Ezek az alkalmazások engedélyezheti a bejelentkezési felhasználói fiókokat az Azure AD-bérlővel, ahol az alkalmazás regisztrálva lesz és a egy eltérő. Webes API-k, például a Microsoft Graph API (az Azure ad-ben, Intune-ban, és az Office 365-ben) és egyéb Microsoft-szolgáltatás API-k, a saját webes API-k mellett eléréséhez is szükséges lehet.

A keretrendszer egy felhasználó vagy rendszergazda engedélyezi hogy egy alkalmazás, amely rákérdez, regisztrálni kell a könyvtárban, amelyek magukban foglalhatják a directory-adatok elérése alapul. Például ha webes ügyfélalkalmazásnak szüksége van a felhasználó naptár adatainak olvasásához az Office 365-ből, a felhasználó szükséges, hogy engedélyt adjanak az ügyfélalkalmazásnak. Miután jóváhagyás van megadva, az ügyfélalkalmazás lesz a Microsoft Graph API meghívása a felhasználó nevében, és használhatják a naptári adatok, igény szerint. A [Microsoft Graph API](https://developer.microsoft.com/graph) hozzáférést biztosít az adatok az Office 365-ben (például a naptárak és üzeneteket az Exchange, a helyek és a SharePoint, a onedrive-ról, notebookok a OneNote-ban, a tevékenységek a Planner, valamint a munkafüzetek dokumentumok listája Excel-), valamint a felhasználók és csoportok Azure AD-ből és más objektumok további Microsoft cloud servicesből.

A hozzájárulási keretrendszer az OAuth 2.0 és a különböző folyamatok épül, mint például engedélyezési kód engedélyezési és az ügyfél hitelesítő adatainak megadása, nyilvános vagy bizalmas ügyfelek használatával. OAuth 2.0 használatával az Azure AD lehetővé teszi számos különböző típusú ügyfélalkalmazások – például telefonon, táblagépen, kiszolgáló vagy egy webalkalmazás –, illetve a szükséges erőforrásokhoz való hozzáférés elnyeréséhez.

A hozzájárulási keretrendszer OAuth2.0-engedélyezések való használatával kapcsolatos további információkért lásd: [engedélyezése webalkalmazásoknak OAuth 2.0 és az Azure AD használatával hozzáférést](v1-protocols-oauth-code.md) és [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md). További információ a kezdeti hitelesített hozzáférést az Office 365 a Microsoft Graphon keresztül: [alkalmazáshitelesítést a Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Jóváhagyási felületen – példa

A következő lépések bemutatják, hogyan beleegyezése élmény az alkalmazás fejlesztője, mind a felhasználó a működését.

1. Fel, hogy egy ügyfél webalkalmazást, amely egy erőforrás és az API eléréséhez adott engedélyek kéréséhez szükséges. Megtudhatja, hogyan ehhez a következő szakaszban Ez a konfiguráció, de alapvetően az Azure portal segítségével alkalmazásengedély-kérelmeket deklarálja a konfiguráláskor. Egyéb olyan konfigurációs beállításoknak, például az alkalmazás Azure AD-regisztrációs részét képezik azok:

    ![Egyéb alkalmazások engedélyei](./media/quickstart-v1-integrate-apps-with-azure-ad/requiredpermissions.png)

1. Vegye figyelembe, hogy az Alkalmazásengedélyek frissítve lett-e, az alkalmazás fut, és egy felhasználó arra készül, hogy első alkalommal használja. Az alkalmazás először szüksége van egy engedélyezési kód beszerzése az Azure AD-ből `/authorize` végpont. Az engedélyezési kód majd egy új hozzáférési beszerezni, és a jogkivonat frissítésére használható.

1. Ha a felhasználó még nem hitelesített, Azure AD `/authorize` végpont kéri a felhasználót, jelentkezzen be.

    [Felhasználó vagy rendszergazda bejelentkezhet az Azure ad-ben](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Miután a felhasználó jelentkezett be, az Azure AD határozza meg, ha a felhasználónak megjelenítendő egy hozzájárulást kérő lap. Ez a döntés e a felhasználó (vagy a szervezet rendszergazdája) már megadta az alkalmazás jóváhagyásának alapul. Jóváhagyás nem már rendelkezik, ha az Azure AD felkéri a felhasználót a hozzájárulásra, és megjeleníti a működéséhez szükséges engedélyekkel. A beleegyezés párbeszédpanelen megjelenő engedélykészletet egyeznek a kiválasztott azokkal a **delegált engedélyek** az Azure Portalon.

    ![Felhasználói jóváhagyás élmény](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Miután a felhasználó engedélyezi a jóváhagyás, az engedélyezési kódot az alkalmazás, amely váltják a hozzáférési jogkivonat beszerzése és a frissítési token vissza küld vissza. Ezzel a folyamattal kapcsolatos további információkért lásd: [webes API-alkalmazás típusa](web-api.md).

1. A rendszergazdák is is beleegyezik az összes felhasználó nevében egy alkalmazás delegált engedélyeit a bérlőben. Rendszergazdai jóváhagyás megakadályozza, hogy a beleegyezés párbeszédpanelen jelenik meg a bérlő összes felhasználója számára, és elvégezhető a [az Azure portal](https://portal.azure.com) a rendszergazdai szerepkörrel rendelkező felhasználók által. Melyik rendszergazda szerepkörök jóváhagyhat delegált engedélyeket kapcsolatban lásd: [rendszergazdája szerepkör engedélyei az Azure ad-ben](../users-groups-roles/directory-assign-admin-roles.md).

    **Hogy engedélyt adjanak az alkalmazás a delegált engedélyek**

    1. Nyissa meg a **beállítások** oldalon az alkalmazás
    1. Válassza ki **szükséges engedélyek**.
    1. Kattintson a **engedélyeket** gombra.

    ![Engedélyek megadása az explicit rendszergazdai jóváhagyás](./media/quickstart-v1-integrate-apps-with-azure-ad/grantpermissions.png)

  > [!IMPORTANT]
  > Hozzájárulás megadása az explicit használatával a **engedélyeket** gomb ADAL.js használó egyoldalas alkalmazások (SPA) jelenleg szükség. Ellenkező esetben a kérelem sikertelen lesz, amikor a hozzáférési jogkivonatot kér.

## <a name="next-steps"></a>További lépések

* Lásd: [kell a több-bérlős alkalmazás átalakítása](howto-convert-app-to-be-multi-tenant.md)
* Ismerje meg részletesebben olvashat róluk, a [hogyan jóváhagyás támogatott az OAuth 2.0 protokoll rétegben az engedélyezési kód engedélyezési folyamata során.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
