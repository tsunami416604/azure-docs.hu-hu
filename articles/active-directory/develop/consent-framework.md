---
title: Azure AD jóváhagyási keretrendszer
titleSuffix: Microsoft identity platform
description: Ismerje meg az Azure Active Directory hozzájárulási keretrendszerét, és hogyan könnyíti meg a több-bérlős webes és natív ügyfélalkalmazások fejlesztését.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.openlocfilehash: cb9441e6ce19094ff72e902cdeea151041ceb963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161134"
---
# <a name="azure-active-directory-consent-framework"></a>Az Azure Active Directory hozzájárulási keretrendszere

Az Azure Active Directory (Azure AD) hozzájárulási keretrendszer megkönnyíti a több-bérlős webes és natív ügyfélalkalmazások fejlesztését. Ezek az alkalmazások lehetővé teszik a bejelentkezést egy Azure AD-bérlő, amely eltér az alkalmazás, ahol az alkalmazás regisztrálva van. Előfordulhat, hogy a saját webes API-k mellett webes API-kat is el kell érniük, például a Microsoft Graph API-kat (az Azure AD, az Intune és az Office 365 szolgáltatásainak eléréséhez), valamint más Microsoft-szolgáltatások API-jait is.

A keretrendszer olyan felhasználón vagy rendszergazdaon alapul, aki hozzájárul egy olyan alkalmazáshoz, amely a címtárban való regisztrációt kéri, és amely magában foglalhatja a címtáradatok elérését. Ha például egy webügyfél-alkalmazásnak el kell olvasnia a felhasználó naptáradatait az Office 365-ből, akkor a felhasználónak először hozzá kell járulnia az ügyfélalkalmazáshoz. A hozzájárulás után az ügyfélalkalmazás képes lesz felhívni a Microsoft Graph API-t a felhasználó nevében, és szükség szerint felhasználhatja a naptáradatokat. A [Microsoft Graph API](https://developer.microsoft.com/graph) hozzáférést biztosít az Office 365-ben tárolt adatokhoz (például naptárakhoz és üzenetekhez az Exchange-ből, webhelyekhez és listákhoz a SharePointból, dokumentumok a OneDrive-ról, jegyzetfüzetek a OneNote-ból, feladatok a Plannerből és munkafüzetek az Excelből), valamint az Azure AD felhasználóihoz és csoportjaihoz, valamint más Microsoft felhőszolgáltatásokból származó adatobjektumokhoz.

A hozzájárulási keretrendszer az OAuth 2.0-s rendszerére és annak különböző folyamataira épül, például az engedélyezési kód megadására és az ügyfél hitelesítő adatainak megadására, nyilvános vagy bizalmas ügyfelek használatával. Az OAuth 2.0 használatával az Azure AD lehetővé teszi, hogy számos különböző típusú ügyfélalkalmazások - például telefonon, táblagépen, kiszolgálón vagy egy webalkalmazáson - és a szükséges erőforrások eléréséhez.

A jóváhagyási keretrendszer OAuth2.0 engedélyezési támogatásokkal való használatáról az [OAuth 2.0-s és az Azure AD-hez való hozzáférés engedélyezése](v2-oauth2-auth-code-flow.md) című témakörben talál további információt. [Authentication scenarios for Azure AD](authentication-scenarios.md) Az Office 365-höz a Microsoft Graphon keresztül történő nyílt hozzáférésről az [Alkalmazáshitelesítés a Microsoft Graph programmal](https://developer.microsoft.com/graph/docs/authorization/auth_overview)című témakörben talál.

## <a name="consent-experience---an-example"></a>Hozzájárulási tapasztalat – példa

A következő lépések bemutatják, hogyan működik a hozzájárulási élmény mind az alkalmazás fejlesztője, mind a felhasználó számára.

1. Tegyük fel, hogy rendelkezik egy webügyfél-alkalmazás, amely nek szüksége van egy adott engedélyek eléréséhez egy erőforrás/API eléréséhez. Megtudhatja, hogyan kell ezt a konfigurációt a következő szakaszban, de lényegében az Azure Portal on deklarálni engedélykérelmek konfigurációs időben. Más konfigurációs beállításokhoz hasonlóan az alkalmazás Azure AD-regisztrációjának részévé válnak:

    ![Engedélyek más alkalmazásokhoz](./media/consent-framework/permissions.png)

1. Vegye figyelembe, hogy az alkalmazás engedélyei frissültek, az alkalmazás fut, és a felhasználó első alkalommal használja azt. Először is az alkalmazás nak be kell szereznie `/authorize` egy engedélyezési kódot az Azure AD végpontjától. Az engedélyezési kód ezután egy új hozzáférési és frissítési jogkivonat megszerzéséhez használható.

1. Ha a felhasználó még nincs hitelesítve, `/authorize` az Azure AD végpontja kéri a felhasználót, hogy jelentkezzen be.

    ![Felhasználó vagy rendszergazda bejelentkezés az Azure AD-be](./media/consent-framework/usersignin.png)

1. Miután a felhasználó bejelentkezett, az Azure AD határozza meg, hogy a felhasználónak meg kell jelennie egy jóváhagyási oldal. Ez a meghatározás azon alapul, hogy a felhasználó (vagy a szervezet rendszergazdája) már megadta-e az alkalmazás beleegyezését. Ha a hozzájárulás még nem lett megadva, az Azure AD kéri a felhasználó beleegyezését, és megjeleníti a működéséhez szükséges engedélyeket. A jóváhagyási párbeszédpanelen megjelenő engedélyek készlete megegyezik az Azure Portalon a **Delegált engedélyekben kiválasztott engedélyekkel.**

    ![Példa a hozzájárulási párbeszédpanelen megjelenített engedélyekre](./media/consent-framework/consent.png)

1. Miután a felhasználó megadja a jóváhagyást, egy engedélyezési kódot arendszer visszaad az alkalmazásnak, amely et egy hozzáférési jogkivonat és a frissítési jogkivonat beszerzése érdekében vált be. Erről a folyamatról további információt az [OAuth 2.0 engedélyezési kódfolyamata című témakörben talál.](v2-oauth2-auth-code-flow.md)

1. Rendszergazdaként is hozzájárulhat egy alkalmazás delegált engedélyeihez a bérlő összes felhasználója nevében. A rendszergazdai hozzájárulás megakadályozza, hogy a jóváhagyási párbeszéd a bérlő minden felhasználója számára megjelenjen, és az [Azure Portalon](https://portal.azure.com) a rendszergazdai szerepkörrel rendelkező felhasználók elvégezhetők. Ha meg szeretné tudni, hogy mely rendszergazdai szerepkörök járulhatnak hozzá a delegált engedélyekhez, olvassa el [a Rendszergazdai szerepkör-engedélyek az Azure AD-ben című témakört.](../users-groups-roles/directory-assign-admin-roles.md)

    **Hozzájárulás egy alkalmazás delegált engedélyeihez**

   1. Ugrás az **alkalmazás API-engedélyek** lapjára
   1. Kattintson a **Grant admin consent** gombra.

      ![Engedélyek megadása kifejezett rendszergazdai jóváhagyáshoz](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Az Engedélyek **megadása** gomb használatával kifejezett hozzájárulás megadása jelenleg szükséges az ADAL.js-t használó egyoldalas alkalmazásokhoz (SPA). Ellenkező esetben az alkalmazás sikertelen lesz, ha a hozzáférési jogkivonatot kéri.

## <a name="next-steps"></a>További lépések

* Tekintse [meg, hogyan alakíthat át egy alkalmazást több-bérlőssé](howto-convert-app-to-be-multi-tenant.md)
* További mélységért ismerje meg, [hogyan támogatja a jóváhagyást az OAuth 2.0 protokollréteg az engedélyezési kód engedélyezési folyamat során.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
