---
title: B2B-együttműködés a SaaS-alkalmazások konfigurálása az Azure Active Directoryban |} A Microsoft Docs
description: Kód- és a PowerShell-minták az Azure Active Directory B2B-együttműködéshez
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2f4ecbe8a183636bfc4e463e4c074ab94e2d01
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182816"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B-együttműködés a SaaS-alkalmazások konfigurálása

Az Azure Active Directory (Azure AD) B2B-együttműködés működik a legtöbb alkalmazás, amelyekbe beépül az Azure ad-ben. Ebben a szakaszban azt végig az Azure AD B2B néhány népszerű SaaS-alkalmazásokhoz használható konfigurálásával kapcsolatos utasításokat is.

Mielőtt az alkalmazás-specifikus utasításokkal tekinti meg, az alábbiakban néhány szabályok költségcsökkenést eredményezzen:

* Az alkalmazások többségét, a felhasználó beállítása fordulhat elő, manuálisan kell. Vagyis a felhasználóknak manuálisan kell létrehozni az alkalmazásban.

* Automatikus telepítés, például a Dropbox vagy a támogató alkalmazások esetében külön meghívók jönnek létre az alkalmazásokból. Felhasználók kell lennie arra, hogy minden a meghívás elfogadásához.

* A felhasználói attribútumok összekeveredett felhasználói profil lemezre (UPD) vendégfelhasználók a problémák elhárításához, mindig állítsa **felhasználóazonosító** való **user.mail**.


## <a name="dropbox-business"></a>Dropbox Business

Ahhoz, hogy a felhasználók számára, hogy jelentkezzen be a szervezeti fiókjával, manuálisan kell konfigurálni a Dropbox Business az Azure AD használatára a Security Assertion Markup Language (SAML) identitás-szolgáltatóként. Dropbox Business nincs konfigurálva ehhez, ha nem kér és egyéb engedélyezése a felhasználók számára, hogy jelentkezzen be az Azure AD.

1. Azure ad-ben a Dropbox alkalmazás hozzáadásához válassza **vállalati alkalmazások** a bal oldali panelen, és kattintson a **Hozzáadás**.

  ![A vállalati alkalmazások oldalon a "Hozzáadás" gombra](media/configure-saas-apps/add-dropbox.png)

2. Az a **alkalmazás hozzáadása** ablakban adja meg **dropbox** a keresési mezőbe, és válassza ki a **Dropbox Business** az eredménylistában.

  ![Keressen a "dropbox" hozzáadása egy alkalmazás lap](media/configure-saas-apps/add-app-dialog.png)

3. A a **egyszeri bejelentkezési** lapon jelölje be **egyszeri bejelentkezési** a bal oldali panelen, és írja be **user.mail** a a **felhasználói azonosító** mezőbe. (Ez UPN, alapértelmezés szerint van beállítva.)

  ![Egyszeri bejelentkezés az alkalmazás konfigurálása](media/configure-saas-apps/configure-app-sso.png)

4. Töltse le a Dropbox-konfigurációhoz használni kívánt tanúsítványt, jelölje be **konfigurálása DropBox**, majd válassza ki **SAML egyszeri bejelentkezési szolgáltatás URL-Címként** a listában.

  ![A Dropbox-konfiguráció tanúsítvány letöltése](media/configure-saas-apps/download-certificate.png)

5. Jelentkezzen be a bejelentkezési URL-címet a Dropbox a **egyszeri bejelentkezési** lapot.

  ![A Dropbox bejelentkezési oldala](media/configure-saas-apps/sign-in-to-dropbox.png)

6. Válassza a menü **felügyeleti konzol**.

  ![A "Felügyeleti konzol" hivatkozást, a Dropbox menü](media/configure-saas-apps/dropbox-menu.png)

7. Az a **hitelesítési** párbeszédpanelen jelölje ki **további**, töltse fel a tanúsítványt, majd a **jelentkezzen be az URL-cím** adja meg az SAML egyszeri bejelentkezési URL-cím.

  ![A "Több" hivatkozásra a hitelesítési összecsukott párbeszédpanel](media/configure-saas-apps/dropbox-auth-01.png)

  ![A "bejelentkezés URL-címe" a hitelesítés kibontott párbeszédpanelen](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Automatikus beállításainak konfigurálása az Azure Portalon, válassza ki a **kiépítési** a bal oldali panelen válassza ki a **automatikus** a a **Kiépítési mód** mezőbe, és válassza ki a  **Engedélyezés**.

  ![Az Azure Portalon automatikus felhasználókiépítés konfigurálása](media/configure-saas-apps/set-up-automatic-provisioning.png)

Vendég vagy tag felhasználók a Dropbox alkalmazásban van beállítva, miután egy külön meghívást kapnak a Dropboxból. Dropbox egyszeri bejelentkezés használatára meghívottak el kell fogadnia a meghívást, egy hivatkozásra kattintva.

## <a name="box"></a>Box
Engedélyezheti a felhasználók be vendég felhasználók hitelesítése az Azure AD-fiókja összevonási, amely a SAML protokoll használatával. Ebben az eljárásban a metaadatokat a Box.com feltöltheti.

1. Adja hozzá a Box alkalmazás a vállalati alkalmazások.

2. Egyszeri bejelentkezés konfigurálásához a következő sorrendben:

  ![Box egyszeri bejelentkezés konfigurálása](media/configure-saas-apps/configure-box-sso.png)

 a. Az a **bejelentkezési URL-cím** ellenőrizze, hogy, hogy a bejelentkezési URL-cím van beállítva megfelelően be az Azure Portalon. Az URL-címet a Box.com-bérlői URL-címe. Azt az elnevezési konvenciót kell követniük *https://.box.com*.  
 A **azonosító** nem vonatkozik az alkalmazáshoz, de is megjelenik a kötelező mező.

 b. Az a **felhasználóazonosító** mezőbe írja be **user.mail** (az egyszeri bejelentkezés az vendégfiókok).

 c. A **SAML-aláíró tanúsítvány**, kattintson a **új tanúsítvány létrehozása**.

 d. Az Azure AD használata Identitásszolgáltatóként, Box.com-bérlő konfigurálásának megkezdéséhez, töltse le a metaadatokat, és mentse a helyi meghajtóról.

 e. A metaadatfájl mezőben előre támogatja a csapattal, amely egyszeri bejelentkezést, konfigurálja.

3. Az Azure AD automatikus felhasználó beállítása, a bal oldali panelen válassza ki a **kiépítési**, majd válassza ki **engedélyezés**.

  ![Az Azure AD-csatlakozhat a Boxhoz engedélyezése](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Például a Dropbox vannak be vannak be kell a Box alkalmazás a meghívót.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Dinamikus csoportok és B2B-együttműködés](use-dynamic-groups.md)
- [B2B-együttműködés felhasználói jogcímek társítása](claims-mapping.md)
- [Külső Office 365-megosztás](o365-external-user.md)

