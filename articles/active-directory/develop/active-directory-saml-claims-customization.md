---
title: Az Azure ad-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása |} A Microsoft Docs
description: Ismerje meg az Azure AD-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása.
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
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.openlocfilehash: 11132426bb8adb6ede564e706e18f3eddd649bef
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401877"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Útmutató: Vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása

Még ma az Azure Active Directory (Azure AD) a legtöbb vállalati alkalmazásokat, beleértve az Azure AD-alkalmazásgyűjtemény, valamint a egyéni alkalmazásokat előre integrált alkalmazások támogatja a egyszeri bejelentkezéshez. Amikor egy felhasználó hitelesíti magát egy alkalmazást az SAML 2.0 protokoll használatával az Azure AD-n keresztül, az Azure AD egy tokent az alkalmazást (egy HTTP POST) küld. És ezt követően az alkalmazás ellenőrzi és használja a jogkivonatot a felhasználó nem kér a felhasználónévvel és jelszóval bejelentkezni. Ezek a SAML-jogkivonatok adatokra a "jogcímek" néven felhasználó tartalmaznak.

A *jogcím* arról, hogy identitásszolgáltatót egy felhasználó adatainak bocsátanak ki, hogy a felhasználó számára a tokenen belül van. A [SAML-jogkivonat](https://en.wikipedia.org/wiki/SAML_2.0), ezek az adatok általában az SAML-attribútum utasítás szerepel. Az SAML-tulajdonos alkalmazásnév-azonosító néven a általában jelzi a felhasználó egyedi azonosítója.

Alapértelmezés szerint az Azure AD kibocsát egy SAML-jogkivonat az alkalmazáshoz, amely tartalmazza a NameIdentifier jogcím értéke az a felhasználó felhasználónevét (AVAGY egyszerű felhasználónév) az Azure ad-ben. Ez az érték a felhasználó egyedi azonosítására alkalmas. Az SAML-jogkivonat a felhasználó e-mail címét, Utónév és Vezetéknév tartalmazó további jogcímek is tartalmaz.

Megtekinteni vagy szerkeszteni a jogcímek, az alkalmazás számára kibocsátott SAML-jogkivonatban, nyissa meg az alkalmazás az Azure Portalon. Válassza ki a **megtekintése és egyéb felhasználói attribútumok szerkesztése** a jelölőnégyzetet a **felhasználói attribútumok** szakaszban az alkalmazás.

![Felhasználói attribútumok szakasz][1]

Előfordulhat, hogy miért a SAML-jogkivonatban kiadott jogcímek szerkesztése kell két lehetséges oka van:
* Az alkalmazás eltérő szabályzatkészletet jogcím URI-k vagy a jogcímértékek lett írva.
* Az alkalmazás telepítése úgy, hogy a NameIdentifier jogcím verziójáétól eltérő a felhasználónevet (AVAGY egyszerű felhasználónév), az Azure AD-ben tárolt igényel.

Az alapértelmezett jogcím értékeket szerkesztheti. Jelölje ki a jogcímet a SAML-jogkivonat attribútumai táblában. Ekkor megnyílik a **attribútum szerkesztése** szakaszban, majd szerkesztheti jogcím neve, verzióértékkel és névtérrel társított a jogcímet.

![Felhasználói attribútum szerkesztése][2]

Valamint eltávolíthatja a jogcímek (nem a NameIdentifier) használatával a helyi menü, amelyre kattintva megnyílik a **...**  ikonra. Új jogcím használatával is hozzáadhat a **attribútum hozzáadása** gombra.

![Felhasználói attribútum szerkesztése][3]

## <a name="editing-the-nameidentifier-claim"></a>A NameIdentifier jogcím szerkesztése

Megoldani a problémát, ahol az alkalmazás lett üzembe helyezve, más felhasználónevet használ, válassza ki a a **felhasználói azonosító** legördülő lista a **felhasználói attribútumok** szakaszban. Ez a művelet biztosítja egy párbeszédpanel, számos különböző beállításokkal:

![Felhasználói attribútum szerkesztése][4]

### <a name="attributes"></a>Attribútumok

Válassza ki a kívánt forrása a `NameIdentifier` (vagy NameID) jogcím. Az alábbi lehetőségek közül választhat.

| Name (Név) | Leírás |
|------|-------------|
| E-mail | A felhasználó e-mail-címe |
| userprincipalName | Az egyszerű felhasználónév (UPN) a felhasználó |
| onpremisessamaccount | SAM-fiók neve, amely a helyszínről az Azure AD szinkronizálása megtörtént |
| objectID | Az ObjectId azonosítóját, a felhasználó Azure AD-ben |
| EmployeeID | A felhasználó az EmployeeID |
| Címtárbővítmények | Címtárbővítmények [az Azure AD Connect szinkronizálási szolgáltatás használata a helyszíni Active Directoryból szinkronizált](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| 1 – 15. Bővítményattribútumok | A helyszíni kiterjesztési attribútumot használja az Azure AD-séma kiterjesztése |

### <a name="transformations"></a>Átalakítások

A speciális jogcímek átalakítások funkciók is használható.

| Függvény | Leírás |
|----------|-------------|
| **ExtractMailPrefix()** | A tartományi utótag eltávolítja az e-mail-cím, SAM-fiók neve vagy a felhasználó egyszerű neve. Ez a felhasználónév átadott keresztül csak az első részt kinyeri (például "joe_smith" helyett joe_smith@contoso.com). |
| **join()** | Az attribútum egy ellenőrzött tartomány csatlakozik. Ha a kiválasztott felhasználói azonosító értékét egy tartománnyal rendelkezik, azt fogja bontsa ki a hozzáfűzni a kiválasztott ellenőrzött tartomány felhasználónév. Ha például az e-mailt választja (joe_smith@contoso.com), a felhasználói azonosító értékét, és válassza contoso.onmicrosoft.com ellenőrzött tartományának részeként, emiatt joe_smith@contoso.onmicrosoft.com. |
| **ToLower()** | Konvertálja a karaktereket a kijelölt attribútum kisbetűs karaktert. |
| **ToUpper()** | Konvertálja a karaktereket a kijelölt attribútum nagybetűket. |

## <a name="adding-claims"></a>Jogcím hozzáadása

Amikor egy jogcímet ad hozzá, megadhatja azon attribútum nevét (ami nem feltétlenül kell az SAML-specifikáció alapján URI mintát követi). Bármely felhasználói attribútumot a könyvtárban tárolt állítsa az értékét, vagy statikus bejegyzésként stabilakká értéket használja a szervezet összes felhasználója.

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

* [Alkalmazások kezelése az Azure ad-ben](../manage-apps/what-is-application-management.md)
* [Az alkalmazásokat, amelyek nem szerepelnek az Azure AD alkalmazáskatalógusában egyszeri bejelentkezés konfigurálása](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
