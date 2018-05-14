---
title: Az egyszeri bejelentkezés felügyeleti a vállalati alkalmazásokhoz az Azure Active Directoryban |} Microsoft Docs
description: Egyszeri bejelentkezés beállításainak kezelése vállalati alkalmazások a szervezetben az Azure Active Directory alkalmazáskatalógusában
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: baf437294dbbca7f63f9d4cdc80ac1cb33a67e42
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Egyszeri bejelentkezés a vállalati alkalmazások kezelése

Ez a cikk ismerteti, hogyan használható a [Azure-portálon](https://portal.azure.com) való egyszeri bejelentkezés beállításainak kezelése vállalati alkalmazások. Vállalati alkalmazások olyan alkalmazások, telepített és a szervezetén belül. Ez a cikk vonatkozik, különösen az alkalmazásokat, az újonnan hozzáadott a [Azure Active Directory alkalmazáskatalógusában](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>A portál az alkalmazások keresése
Minden vállalati alkalmazás, amely az egyszeri bejelentkezés beállítása tekinthetők meg és felügyelete az Azure-portálon. Az alkalmazások találhatók a **minden szolgáltatás** &gt; **vállalati alkalmazások** a portál szakaszban. 

![Vállalati alkalmazások panel](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.png)

Válassza ki **összes alkalmazás** konfigurált összes alkalmazások listájának megtekintéséhez. Látható értesítések valamelyikének kiválasztásakor az alkalmazás az adott alkalmazáshoz, ahol jelentések megtekinthetők az alkalmazáshoz, és különböző beállítások kezelheti az erőforrásokat.

Egyszeri bejelentkezés beállításainak kezeléséhez, jelölje ki a **egyszeri bejelentkezés**.

![Alkalmazás erőforráspanelen](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Egyszeri bejelentkezési módok
**Egyszeri bejelentkezés** kezdődik egy **mód** menü, amely lehetővé teszi, hogy az egyetlen bejelentkezés módban kell konfigurálni. Az elérhető lehetőségek a következők:

* **SAML-alapú bejelentkezés** – Ez a beállítás akkor használható, ha az alkalmazás teljes összevont egyszeri bejelentkezést támogatja az Azure Active Directoryval, a WS-Federation, SAML 2.0 protokoll segítségével, vagy az OpenID connect protokollokat.
* **Jelszó alapú bejelentkezés** – Ez a beállítás akkor használható, ha az Azure AD jelszó űrlap kitöltését az alkalmazás támogatja.
* **Bejelentkezés kapcsolódó** – korábbi nevén a "Meglévő egyszeri bejelentkezéshez", ez a beállítás lehetővé teszi a rendszergazdák számára, hogy a felhasználó Azure AD hozzáférési Panel vagy az Office 365 alkalmazásindító helyezze el az alkalmazás mutató hivatkozást.

Ezek módokkal kapcsolatos további információkért lásd: [hogyan egyszeri bejelentkezés az Azure Active Directory munkahelyi](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-alapú bejelentkezés
A **SAML-alapú bejelentkezés** beállítás osztják négy részből áll:

### <a name="domains-and-urls"></a>Tartományok és URL-címek
Ez azért, ahol az alkalmazás tartomány- és URL-címek összes adatait az Azure AD-címtár hozzáadódnak. Egyszeri bejelentkezés munkahelyi alkalmazás szükséges összes bemenet közvetlenül képernyőn jelennek meg a, mivel az összes opcionális bemenet is megtekintéséhez jelölje ki a **megjelenítése speciális URL-beállításainak** jelölőnégyzetet. A támogatott bemeneti adatok teljes listáját tartalmazza:

* **bejelentkezés URL-cím** – Ha a felhasználó megnyitja a jelentkezzen be az alkalmazást. Ha az alkalmazás szolgáltatás-szolgáltató által kezdeményezett egyszeri bejelentkezést, hajtsa végre, amikor a felhasználó megnyitja az URL-cím van konfigurálva, a szolgáltató az Azure AD-hitelesítéséhez, és a felhasználói bejelentkezés irányítja át. 
  * Ha ez a mező nem üres, majd az Azure AD használatával az URL-címet az alkalmazás indítása az Office 365 és az Azure AD hozzáférési Panel.
  * Ha ez a mező nincs megadva, majd az Azure AD helyette identitás-szolgáltató által kezdeményezett bejelentkezés hajt végre, amikor az alkalmazás elindul az Office 365, az Azure AD hozzáférési Panel vagy az Azure ad-egyszeri bejelentkezés URL-CÍMÉT.
* **Azonosító** -ez URI egyedileg kell azonosítania az alkalmazás, amelynek egyszeri bejelentkezésre van konfigurálva. Ez az érték, amely az Azure AD küld vissza az SAML-jogkivonatra alkalmazásának a célközönség paraméterként, és az alkalmazás várhatóan ellenőrzéséhez. Ez az érték is az entitás azonosítója a SAML metaadatai között, az alkalmazás által biztosított jelenik meg.
* **Válasz URL-cím** -válasz URL-címe, ahol az alkalmazás fogadni az SAML-jogkivonat vár. Ez más néven a helyességi feltétel fogyasztói Service (ACS) URL-CÍMÉT. Után ezek, kattintson a Tovább gombra a folytatáshoz a következő képernyő. Ez a képernyő mit kell megadni az alkalmazás oldalon engedélyezze, hogy fogadja el az Azure AD SAML-jogkivonatból információkat biztosít.
* **Továbbítási állapotot** -továbbítási állapota egy nem kötelező paraméter, amely segíthet meg, hogy az alkalmazás hol átirányítja a felhasználót a hitelesítés befejezése után. Általában a értéke egy érvényes URL-cím, az alkalmazás, azonban bizonyos alkalmazások használhatják ezt a mezőt eltérően (lásd az alkalmazás egyszeri bejelentkezés dokumentációjában). A továbbítási állapotot meg az új szolgáltatása, amely egyedi az új Azure-portálra.

### <a name="user-attributes"></a>Felhasználói attribútumok
Ez az adott rendszergazdák megtekintéséhez és szerkesztéséhez a attribútumok, amelyek az Azure AD kibocsát minden egyes alkalmazáshoz a SAML-jogkivonat a amikor a felhasználók bejelentkeznek.

A támogatott csak szerkeszthető attribútum a **felhasználói azonosító** attribútum. Ez az attribútum értéke a mező az Azure ad-ben, amely egyedileg azonosítja az egyes felhasználói az alkalmazáson belül. Például ha az alkalmazás telepítve lett, az "E-mail cím" a felhasználónév és azonosítójának használatával, majd a volna értéke a "user.mail" mezőre az Azure ad-ben.

### <a name="saml-signing-certificate"></a>SAML-aláíró tanúsítványa
Ez a szakasz az Azure AD a SAML kiadott jogkivonatokat, minden alkalommal, amikor a felhasználó hitelesíti magát az alkalmazás aláírásához használt tanúsítvány részleteit jeleníti meg. Lehetővé teszi a ellenőrizni kell, hogy az aktuális tanúsítvány tulajdonságainak többek között a lejárati dátum.

### <a name="application-configuration"></a>Alkalmazáskonfiguráció
A végső rész a dokumentáció és/vagy az alkalmazás maga használja az Azure Active Directory identitás-szolgáltatóként konfigurálásához szükséges vezérlők.

A **alkalmazás konfigurálása** úszó menü új tömör, beágyazott vonatkozó utasításokat tartalmaz, az alkalmazás konfigurálása. Ez az egyedi az új Azure-portálra egy másik új szolgáltatás.

> [!NOTE]
> Beágyazott dokumentációjának átfogó példát tekintse meg a Salesforce.com-alkalmazás. További alkalmazások dokumentációját folyamatosan bővülő.
> 
> 

![Beágyazott docs](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Jelszó alapú bejelentkezés
Ha támogatja az alkalmazáshoz, válassza a jelszó-alapú egyszeri bejelentkezési módot, majd válassza ki **mentése** azonnal konfigurálja a teendő a jelszó-alapú egyszeri Bejelentkezést. Egyszeri jelszó alapú központi telepítésével kapcsolatos további információkért lásd: [hogyan egyszeri bejelentkezés az Azure Active Directory munkahelyi](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Jelszó alapú bejelentkezés](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
Ha támogatja az alkalmazáshoz, a csatolt SSO mód kiválasztása lehetővé teszi az URL-címét, amelyet az Azure AD hozzáférési Panel vagy az Office 365 átirányítása, ha a felhasználó kattint a ezt az alkalmazást. Csatolt SSO (korábbi nevén "meglévő SSO") kapcsolatos további információkért lásd: [hogyan egyszeri bejelentkezés az Azure Active Directory munkahelyi](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Csatolt bejelentkezés](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Visszajelzés

Reméljük, például a továbbfejlesztett használata az Azure AD felhasználói élmény. Ne hamarosan visszajelzését! Visszajelzését és ötleteket javítására szolgáló utáni a **felügyeleti portál** szakasza a [visszajelzési fórumon](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Ritkán használt adatok új dolgai minden nap kiépítésével foglalkozó még érdeklődőbbek és az útmutató a shape felhasználása és határozza meg, mi készíteni.

