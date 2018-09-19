---
title: Az Azure Active Directory vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása |} A Microsoft Docs
description: Ismerje meg a vállalati alkalmazások esetén az Azure Active Directory a SAML-jogkivonatban kiadott jogcímek testreszabása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 08e817b24d4190b6d7f774dd8c812d04e89c1e5a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296753"
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Az Azure Active Directory vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása
Még ma az Azure Active Directory a legtöbb vállalati alkalmazásokat, beleértve az Azure AD-alkalmazásgyűjtemény, valamint a egyéni alkalmazásokat előre integrált alkalmazások támogatja a egyszeri bejelentkezéshez. Amikor egy felhasználó hitelesíti magát egy alkalmazást az SAML 2.0 protokoll használatával az Azure AD-n keresztül, az Azure AD egy tokent az alkalmazást (egy HTTP POST) küld. És ezt követően az alkalmazás ellenőrzi és használja a jogkivonatot a felhasználó nem kér a felhasználónévvel és jelszóval bejelentkezni. Ezek a SAML-jogkivonatok adatokra a "jogcímek" néven felhasználó tartalmaznak.

Az identitás-a beszéd, a "jogcím" információ arról, hogy az identitásszolgáltató egy felhasználót, hogy a felhasználó számára kiadják a tokenen belül. A [SAML-jogkivonat](http://en.wikipedia.org/wiki/SAML_2.0), ezek az adatok általában az SAML-attribútum utasítás szerepel. Az SAML-tulajdonos alkalmazásnév-azonosító néven a általában jelzi a felhasználó egyedi azonosítója.

Alapértelmezés szerint az Azure Active Directory a SAML-jogkivonat problémák az az alkalmazás, amely tartalmazza a NameIdentifier jogcím értéke az a felhasználó felhasználónevét (AVAGY egyszerű felhasználónév) az Azure ad-ben. Ez az érték a felhasználó egyedi azonosítására alkalmas. Az SAML-jogkivonat a felhasználó e-mail címét, Utónév és Vezetéknév tartalmazó további jogcímek is tartalmaz.

Megtekinteni vagy szerkeszteni a jogcímek, az alkalmazás számára kibocsátott SAML-jogkivonatban, nyissa meg az alkalmazás az Azure Portalon. Válassza ki a **megtekintése és egyéb felhasználói attribútumok szerkesztése** a jelölőnégyzetet a **felhasználói attribútumok** szakaszban az alkalmazás.

![Felhasználói attribútumok szakasz][1]

Előfordulhat, hogy miért a SAML-jogkivonatban kiadott jogcímek szerkesztése kell két lehetséges oka van:
* Az alkalmazás eltérő szabályzatkészletet jogcím URI-k vagy a jogcímértékek lett írva.
* Az alkalmazás telepítése úgy, hogy a NameIdentifier jogcím verziójáétól eltérő a felhasználónevet (AVAGY egyszerű felhasználónév), az Azure Active Directoryban tárolt igényel.

Az alapértelmezett jogcím értékeket szerkesztheti. Jelölje ki a jogcímet a SAML-jogkivonat attribútumai táblában. Ekkor megnyílik a **attribútum szerkesztése** szakaszban, majd szerkesztheti jogcím neve, verzióértékkel és névtérrel társított a jogcímet.

![Felhasználói attribútum szerkesztése][2]

Valamint eltávolíthatja a jogcímek (nem a NameIdentifier) használatával a helyi menü, amelyre kattintva megnyílik a **...**  ikonra. Új jogcím használatával is hozzáadhat a **attribútum hozzáadása** gombra.

![Felhasználói attribútum szerkesztése][3]

## <a name="editing-the-nameidentifier-claim"></a>A NameIdentifier jogcím szerkesztése
Megoldani a problémát, ahol az alkalmazás lett üzembe helyezve, más felhasználónevet használ, kattintson a a **felhasználói azonosító** legördülő lista a **felhasználói attribútumok** szakaszban. Ez a művelet biztosítja egy párbeszédpanel, számos különböző beállításokkal:

![Felhasználói attribútum szerkesztése][4]

Válassza ki a legördülő menüben **user.mail** a NameIdentifier jogcím a felhasználó e-mail címét a címtárban kell beállítani. Vagy válassza **user.onpremisessamaccountname** , ha szeretné a felhasználó a SAM-fiók neve, amely a helyszínről az Azure AD szinkronizálása megtörtént.

Is használhatja a speciális **ExtractMailPrefix()** a tartományi utótag eltávolítja az e-mail-cím, SAM-fiók neve vagy a felhasználó egyszerű neve. Ez a felhasználónév átadott keresztül csak az első részt kinyeri (például "joe_smith" helyett joe_smith@contoso.com).

![Felhasználói attribútum szerkesztése][5]

Ezzel is hozzáadtuk a **join()** függvény a felhasználói azonosító értékét az ellenőrzött tartományhoz való csatlakozáshoz. Ha bejelöli a join() függvény a a **felhasználóazonosító** először válassza ki a felhasználói azonosító, például e-mail-cím vagy a felhasználó egyszerű neve és a második legördülő menüből válassza ki az ellenőrzött tartomány. Ha az e-mail-címet ellenőrzött tartományának, adja meg Azure ad-ben a felhasználónév kigyűjti az első érték joe_smith a joe_smith@contoso.com és fűz hozzá a contoso.onmicrosoft.com. Tekintse meg a következő példát:

![Felhasználói attribútum szerkesztése][6]

## <a name="adding-claims"></a>Jogcím hozzáadása
Amikor egy jogcímet ad hozzá, megadhatja azon attribútum nevét (ami nem feltétlenül kell az SAML-specifikáció alapján URI mintát követi). Bármely felhasználói attribútumot a könyvtárban tárolt állítsa az értékét.

![Felhasználói attribútum hozzáadása][7]

Ha például kíván küldeni a számára, amely a felhasználó tartozik a szervezetben (mint például a Sales) jogcímként való. Adja meg a jogcím neve, az alkalmazás által elvárt módon, és válassza **user.department** értékeként.

> [!NOTE]
> Ha egy adott felhasználó nincs érték, egy kijelölt attribútumban tárolja, majd, hogy nincs folyamatban jogcímet a jogkivonat.

> [!TIP]
> A **user.onpremisesecurityidentifier** és **user.onpremisesamaccountname** csak akkor támogatott, ha a felhasználói adatok szinkronizálása a helyszíni Active Directory használatával a [Azure ad-ben Eszköz csatlakoztatása](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Korlátozott jogcímek

Nincsenek SAML néhány korlátozott jogcímeket. Ha ezeket a jogcímeket, majd az Azure AD nem küld ezeket a jogcímeket. Az alábbiakban a korlátozott SAML jogcímek készletéhez:

    | Jogcím típusa (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>További lépések
* [Alkalmazások kezelése az Azure Active Directoryban](../manage-apps/what-is-application-management.md)
* [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
