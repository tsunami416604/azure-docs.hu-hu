---
title: SaaS-alkalmazások konfigurálása B2B-együttműködésre – Azure AD
description: Kód- és a PowerShell-minták az Azure Active Directory B2B-együttműködéshez
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272955"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>SaaS-alkalmazások B2B-együttműködéshez

Az Azure Active Directory (Azure AD) B2B-együttműködés az Azure AD-vel integrálható legtöbb alkalmazással működik. Ebben a szakaszban néhány népszerű SaaS-alkalmazás azure AD B2B-vel való használatra való konfigurálásának útmutatóját ismerteti.

Mielőtt megnézi az alkalmazásspecifikus utasításokat, íme néhány ökölszabály:

* A legtöbb alkalmazás esetében a felhasználói beállításoknak manuálisan kell történniük. Ez azt illeti, a felhasználókat manuálisan kell létrehozni az alkalmazásban is.

* Az automatikus telepítést támogató alkalmazások, például a Dropbox esetében külön meghívók jönnek létre az alkalmazásokból. A felhasználóknak minden meghívást el kell fogadniuk.

* A felhasználói attribútumokban a vendégfelhasználók ban megcsonkított felhasználói profillemezzel (UPD) kapcsolatos problémák enyhítése érdekében mindig állítsa a **felhasználói azonosítót** **a user.mail**értékre.


## <a name="dropbox-business"></a>Dropbox Üzleti

Ahhoz, hogy a felhasználók a szervezeti fiókjukkal jelentkezhessenek be, manuálisan kell konfigurálnia a Dropbox Business szolgáltatást, hogy az Azure AD-t használja biztonsági helyestítési azonosító nyelvként (SAML) identitásszolgáltatóként. Ha a Dropbox Business nincs konfigurálva erre, nem kérheti, vagy más módon nem engedélyezheti a felhasználóknak, hogy az Azure AD használatával jelentkezzenek be.

1. Ha hozzá szeretné adni a Dropbox Business alkalmazást az Azure AD-hez, válassza a **Vállalati alkalmazások** lehetőséget a bal oldali ablaktáblában, majd kattintson a **Hozzáadás gombra.**

   ![A Vállalati alkalmazások lap "Hozzáadás" gombja](media/configure-saas-apps/add-dropbox.png)

2. Az **Alkalmazás hozzáadása** ablakban írja be a **beviteli mezőt** a keresőmezőbe, majd válassza a **Dropbox vállalati verzió** lehetőséget az eredménylistában.

   ![Keresés a "dropbox" kifejezésre az Alkalmazás hozzáadása lapon](media/configure-saas-apps/add-app-dialog.png)

3. Az **Egyszeri bejelentkezés** lapon válassza az **Egyszeri bejelentkezés** lehetőséget a bal oldali ablaktáblában, majd írja be a **user.mail** parancsot a **Felhasználói azonosító** mezőbe. (Alapértelmezés szerint upn-ként van beállítva.)

   ![Egyszeri bejelentkezés konfigurálása az alkalmazáshoz](media/configure-saas-apps/configure-app-sso.png)

4. A Dropbox-konfigurációhoz használandó tanúsítvány letöltéséhez válassza a **DropBox konfigurálása**lehetőséget, majd a listában válassza az **SAML Single Sign On Service URL-címét.**

   ![A Dropbox-konfiguráció tanúsítványának letöltése](media/configure-saas-apps/download-certificate.png)

5. Jelentkezzen be a Dropboxba a bejelentkezési URL-címmel az **egyszeri bejelentkezési** oldalon.

   ![A Dropbox bejelentkezési lapját bemutató képernyőkép](media/configure-saas-apps/sign-in-to-dropbox.png)

6. A menüben válassza a **Felügyeleti konzol lehetőséget.**

   ![Az "Admin Console" hivatkozás a Dropbox menüben](media/configure-saas-apps/dropbox-menu.png)

7. A **Hitelesítés** párbeszédpanelen válassza az **Egyebek**lehetőséget, töltse fel a tanúsítványt, majd a **Bejelentkezési URL-cím** mezőbe írja be az SAML egyszeri bejelentkezési URL-címét.

   ![Az összecsukott hitelesítés párbeszédpanel "Egyebek" hivatkozása](media/configure-saas-apps/dropbox-auth-01.png)

   ![A "Bejelentkezési URL" a kibontott hitelesítés párbeszédpanelen](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Az automatikus felhasználói beállítások konfigurálásához az Azure Portalon válassza a **Kiépítés** lehetőséget a bal oldali ablaktáblában, válassza az **Automatikus** lehetőséget a **Kiépítés mód** mezőben, majd az **Engedélyezés**lehetőséget.

   ![Automatikus felhasználói kiépítés konfigurálása az Azure Portalon](media/configure-saas-apps/set-up-automatic-provisioning.png)

Miután a vendég- vagy tagfelhasználók at beállították a Dropbox alkalmazásban, külön meghívót kapnak a Dropboxtól. A Dropbox egyszeri bejelentkezéshez a meghívottaknak el kell fogadniuk a meghívást egy benne lévő hivatkozásra kattintva.

## <a name="box"></a>Box
Engedélyezheti a felhasználók számára, hogy hitelesítsék a Dobozvendég-felhasználókat az Azure AD-fiókjukkal az SAML protokollon alapuló összevonás használatával. Ebben az eljárásban metaadatokat tölthet fel Box.com.

1. Adja hozzá a Box alkalmazást a vállalati alkalmazásokból.

2. Egyszeri bejelentkezés konfigurálása a következő sorrendben:

   ![Az egyszeri bejelentkezési konfigurációs beállításokat bemutató képernyőkép](media/configure-saas-apps/configure-box-sso.png)

   a. A **Bejelentkezési URL-cím** mezőben győződjön meg arról, hogy a bejelentkezési URL-cím megfelelően van beállítva az Azure Portalon a Box számára. Ez az URL-cím a Box.com bérlő URL-címe. Meg kell felelnie *https://.box.com*az elnevezési konvenció .  
   Az **azonosító** nem vonatkozik erre az alkalmazásra, de továbbra is kötelező mezőként jelenik meg.

   b. A **Felhasználói azonosító** mezőbe írja be a **user.mail** (a vendégfiókok sso-jához) értéket.

   c. Az **SAML aláíró tanúsítvány csoportban**kattintson **az Új tanúsítvány létrehozása gombra.**

   d. Ha meg szeretné kezdeni a Box.com-bérlő konfigurálását az Azure AD identitásszolgáltatóként való használatára, töltse le a metaadatfájlt, majd mentse a helyi meghajtóra.

   e. Továbbítsa a metaadatfájlt a Box támogatási csapatának, amely konfigurálja az egyszeri bejelentkezést.

3. Az Azure AD automatikus felhasználói beállításaihoz a bal oldali ablaktáblában válassza a **Kiépítés**lehetőséget, majd az **Engedélyezés lehetőséget.**

   ![Az Azure AD engedélyezése a Boxhoz való csatlakozáshoz](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

A Dropbox meghívottakhoz hasonlóan a Box meghívottaknak is be kell váltaniuk a meghívót a Box alkalmazásból.

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikkeket az Azure AD B2B együttműködés:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Dinamikus csoportok és B2B együttműködés](use-dynamic-groups.md)
- [B2B együttműködési felhasználói jogcímleképezés](claims-mapping.md)
- [Külső Office 365-megosztás](o365-external-user.md)

