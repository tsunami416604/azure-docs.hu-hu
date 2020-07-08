---
title: 'Rövid útmutató: egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben'
description: Ez a rövid útmutató végigvezeti az egyszeri bejelentkezés (SSO) beállításának folyamatán a Azure Active Directory (Azure AD) bérlőben lévő alkalmazásokhoz.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: f90edf525a8deb35376d6637090e7b424b2fd658
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86038990"
---
# <a name="quickstart-set-up-single-sign-on-sso-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>Rövid útmutató: egyszeri bejelentkezés (SSO) beállítása egy alkalmazáshoz a Azure Active Directory (Azure AD) bérlőben

Az Azure AD-bérlőhöz hozzáadott alkalmazásokhoz tartozó egyszeri bejelentkezés beállításával megkezdheti az egyszerűsített felhasználói bejelentkezések megkezdését. Az egyszeri bejelentkezés beállítása után a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be egy alkalmazásba. Az egyszeri bejelentkezést az Azure AD ingyenes kiadása tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-bérlőhöz hozzáadott alkalmazások egyszeri bejelentkezésének beállításához a következők szükségesek:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A következő szerepkörök egyike: globális rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás rendszergazdája vagy az egyszerű szolgáltatásnév tulajdonosa.
- Egy olyan alkalmazás, amely támogatja az egyszeri bejelentkezést, amely már előre konfigurálva van, és hozzá lett adva az Azure AD-gyűjteményhez. A legtöbb alkalmazás használhatja az Azure AD-t az egyszeri bejelentkezéshez. Az Azure AD Gallery alkalmazásai előre konfigurálva vannak. Ha az alkalmazás nem szerepel a listáján, vagy egy egyéni fejlesztésű alkalmazás, akkor továbbra is használhatja az Azure AD-vel. Tekintse meg az oktatóanyagokat és az egyéb dokumentációt a tartalomjegyzékben. Ez a rövid útmutató olyan alkalmazásokra összpontosít, amelyek előre konfigurálva lettek az egyszeri bejelentkezéshez, és az alkalmazás fejlesztői számára az Azure AD-katalógusba kerültek.
- (Nem kötelező: [az alkalmazások megtekintésének](view-applications-portal.md)befejezése).
- (Nem kötelező: [alkalmazás hozzáadása](add-application-portal.md)).
- (Nem kötelező: [alkalmazás konfigurálásának](add-application-portal-configure.md)befejezése).


>[!IMPORTANT]
>Az ebben a rövid útmutatóban ismertetett lépések teszteléséhez nem éles környezet használatát javasoljuk.


## <a name="enable-single-sign-on-for-an-app"></a>Egyszeri bejelentkezés engedélyezése egy alkalmazáshoz

Amikor befejezi az alkalmazás hozzáadását az Azure AD-bérlőhöz, azonnal megjelenik az áttekintő oldal. Ha olyan alkalmazást konfigurál, amely már hozzá lett adva, tekintse meg az első rövid útmutatót, amely végigvezeti a bérlőhöz hozzáadott alkalmazások megtekintésén. 

Egyszeri bejelentkezés beállítása egy alkalmazáshoz:

1. Az Azure AD-portálon válassza a **vállalati alkalmazások** lehetőséget, majd keresse meg és válassza ki azt az alkalmazást, amelyet be szeretne állítani az egyszeri bejelentkezéshez.
2. A kezelés szakaszban válassza az **egyszeri bejelentkezés** lehetőséget a Tulajdonságok ablaktábla szerkesztéshez való megnyitásához.
    :::image type="content" source="media/add-application-portal-setup-sso/configure-sso.png" alt-text="Megjeleníti az Azure AD portál egyszeri bejelentkezési konfigurációjának oldalát.":::
3. Válassza az SAML lehetőséget az SSO-konfiguráció lap megnyitásához. Ebben a példában az SSO-ra konfigurált alkalmazás a GitHub. Miután beállította a GitHubot, a felhasználók bejelentkezhetnek a GitHubra az Azure AD-bérlője hitelesítő adataival.
    :::image type="content" source="media/add-application-portal-setup-sso/github-sso.png" alt-text="Megjeleníti az egyszeri bejelentkezés konfigurációjának oldalát a GitHubon.":::
4. Az Azure AD az SAML-alapú SSO használatára való konfigurálásának folyamata az alkalmazástól függően változhat. Figyelje meg, hogy van hivatkozás a GitHub útmutatására. A további alkalmazásokra vonatkozó útmutatók a következő címen találhatók:https://docs.microsoft.com/azure/active-directory/saas-apps/
5. Az alkalmazáshoz tartozó egyszeri bejelentkezés beállításához kövesse az útmutatót. Számos alkalmazás rendelkezik konkrét előfizetési követelményekkel az egyszeri bejelentkezés működéséhez. A GitHub például nagyvállalati előfizetést igényel.
    :::image type="content" source="media/add-application-portal-setup-sso/github-pricing.png" alt-text="Megjeleníti az egyszeri bejelentkezés lehetőséget a GitHub díjszabási oldalának vállalati előfizetésében.":::


## <a name="next-steps"></a>További lépések

- [Alkalmazás törlése](delete-application-portal.md)
