---
title: "Az Azure Active Directory B2B együttműködés SaaS-alkalmazások konfigurálása |} Microsoft Docs"
description: "Azure Active Directory B2B együttműködés kód és a PowerShell-példák"
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: e2206ca8ddee867b9ea48911f70793010989ad69
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B együttműködés SaaS-alkalmazások konfigurálása

Az Azure Active Directory (Azure AD) B2B együttműködés működik együtt a legtöbb alkalmazást, amelyekbe beépül az Azure AD. Ez a szakasz azt végezze el az Azure AD B2B néhány népszerű SaaS-alkalmazások a konfigurálására vonatkozó útmutatásokat.

Mielőtt az alkalmazás-specifikus utasításokkal tekinti meg, az alábbiakban néhány szabályok megoldás:

* Az alkalmazások a legtöbb, a felhasználó beállítása fordulhat elő, manuálisan kell. Ez azt jelenti, hogy felhasználók manuálisan kell létrehozni az alkalmazásban.

* Automatikus telepítés, például a Dropbox, támogató alkalmazások esetében külön meghívókat készített alkalmazások. Lehet, hogy a felhasználók meg arról, hogy minden egyes meghívó elfogadásához.

* A felhasználói attribútumok a problémák merülnek fel a összekeveredett felhasználói profil lemezre (UPD) a vendégfelhasználók mérséklése érdekében mindig beállította **felhasználói azonosító** való **user.mail**.


## <a name="dropbox-business"></a>Dropbox üzleti

Ahhoz, hogy a felhasználók jelentkezhetnek be a szervezeti fiókjával, manuálisan kell konfigurálni a Dropbox üzleti az Azure AD használatára a Security Assertion Markup Language (SAML) identitás-szolgáltatóként. Ha Dropbox üzleti nem erre van konfigurálva, nem kérése és egyéb engedélyezése a felhasználók számára, hogy jelentkezzen be az Azure AD.

1. Az Azure AD-be a Dropbox üzleti alkalmazás hozzáadásához válassza **vállalati alkalmazások** a bal oldali ablaktáblán, majd **Hozzáadás**.

  ![A "Hozzáadás" gombra a vállalati alkalmazások lap](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

2. Az a **alkalmazás hozzáadása** ablak, írja be **dropbox** a keresési mezőbe, és válassza a **vállalati Dropbox** az eredménylistában.

  ![Egy alkalmazás-lap hozzáadása "dropbox" keresése](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

3. A a **egyszeri bejelentkezés** lapon jelölje be **egyszeri bejelentkezés** a bal oldali ablaktáblán, majd adja meg **user.mail** a a **felhasználói azonosító** mezőbe. (Érték szerint UPN alapértelmezés szerint.)

  ![Egyszeri bejelentkezés az alkalmazás konfigurálása](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

4. A Dropbox-konfigurációhoz használni kívánt tanúsítványt letöltéséhez, jelölje be az **konfigurálása DropBox**, majd válassza ki **SAML-alapú egyszeri bejelentkezési szolgáltatás URL-cím** a listában.

  ![A Dropbox-konfiguráció tanúsítvány letöltése](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. Jelentkezzen be a bejelentkezési URL-címet a Dropbox a **egyszeri bejelentkezés** lap.

  ![A Dropbox bejelentkezési oldal](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

6. Válassza a menü **felügyeleti konzol**.

  ![A "Felügyeleti konzol" hivatkozásra kattintva a Dropbox menü](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

7. A a **hitelesítési** párbeszédpanelen jelölje ki **további**, a tanúsítvány feltöltése, a a **jelentkezzen be az URL-cím** mezőbe írja be a SAML egyetlen bejelentkezési URL-címet.

  ![A "Több" hivatkozásra a összecsukott párbeszédpanel](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![A "bejelentkezés URL-címe" a hitelesítés kibontott párbeszédpanelen](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

8. Automatikus felhasználó beállítása az Azure portálon konfigurálásához jelölje ki **kiépítési** a bal oldali panelen válassza ki a **automatikus** a a **kiépítési üzemmódban** mezőbe, majd válassza ki a  **Engedélyezi**.

  ![Automatikus felhasználók átadására az Azure-portálon](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

Vendég vagy tag felhasználók a Dropbox alkalmazás van beállítva, akkor kapnak egy külön meghívó az dropbox-bA. Dropbox egyszeri bejelentkezést használ, a meghívott személyeknek el kell fogadnia a meghívó a hivatkozásra kattintva.

## <a name="box"></a>Box
Engedélyezheti a felhasználók számára a vendégfelhasználók mezőben az Azure AD-fiókkal hitelesítést összevonási alapuló az SAML protokoll használatával. Ezzel az eljárással feltöltött Box.com metaadatok.

1. A Box alkalmazásához adja hozzá a vállalati alkalmazásokat.

2. Egyszeri bejelentkezés konfigurálása a következő sorrendben:

  ![Mezőbe az egyszeri bejelentkezés konfigurálása](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

 a. Az a **bejelentkezési URL-cím** győződjön meg arról, hogy a bejelentkezési URL-cím beállításai megfelelően be az Azure portálon. Az URL-cím a Box.com bérlői URL-CÍMÉT. Azt az elnevezési konvenciót kell követnie *https://.box.com*.  
 A **azonosító** nem felel meg az alkalmazáshoz, de is megjelenik a kötelező mező.

 b. Az a **felhasználói azonosító** adja meg a **user.mail** (az egyszeri bejelentkezés a Vendég-fiókok).

 c. A **SAML-aláíró tanúsítványa**, kattintson a **hozzon létre új tanúsítvány**.

 d. A Box.com bérlő identitás-szolgáltatóként használhatja az Azure Active Directory konfigurálása a kezdéshez töltse le a metaadatokat, és mentse a helyi meghajtóról.

 e. A mezőben a metaadatfájl előre támogatja a csoport, amely egyszeri bejelentkezést az Ön konfigurálja.

3. Az Azure AD automatikus felhasználó beállítása, a bal oldali panelen válassza a **kiépítési**, majd válassza ki **engedélyezés**.

  ![Engedélyezi az Azure AD-be való kapcsolódáshoz](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Például a Dropbox a meghívott személyeknek a mezőben a meghívott személyeknek a meghívó a Box alkalmazásához a kell beváltani.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés, tekintse meg a következő cikkeket:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2B együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
