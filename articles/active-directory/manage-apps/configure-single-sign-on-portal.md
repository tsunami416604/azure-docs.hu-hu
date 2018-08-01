---
title: Egyszeri bejelentkezés kezelése a vállalati alkalmazásokhoz az Azure Active Directoryban |} A Microsoft Docs
description: Egyszeri bejelentkezés beállításai a szervezeten belül a vállalati alkalmazások kezelése az Azure Active Directory alkalmazáskatalógusában
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 81b959a08f55f13fd0bcadce32b65ba64f9bb270
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365491"
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Egyszeri bejelentkezés a vállalati alkalmazások kezelése

Ez a cikk ismerteti, hogyan használható a [az Azure portal](https://portal.azure.com) egyszeri bejelentkezési beállításainak vállalati alkalmazások kezeléséhez. Vállalati alkalmazások olyan alkalmazások, telepített és a szervezetén belül. A felvett alkalmazások különösen érvényes ez a cikk a [Azure Active Directory alkalmazáskatalógusában](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>A portál az alkalmazások keresése
Minden vállalati alkalmazás, amely az egyszeri bejelentkezés beállítása is tekinthetők meg és kezelhetők az Azure Portalon. Tekintheti meg az alkalmazások a **minden szolgáltatás** &gt; **vállalati alkalmazások** a portál szakaszát. 

![Vállalati alkalmazások panelen](./media/configure-single-sign-on-portal/enterprise-apps-blade.png)

Válassza ki **minden alkalmazás** konfigurált összes alkalmazások listájának megtekintéséhez. Alkalmazás kiválasztása megjeleníti az adott alkalmazáshoz, ha jelentéseket tekinthet meg az adott alkalmazáshoz, és számos beállítást kezelheti az erőforrásokat.

Egyszeri bejelentkezési beállításainak kezeléséhez, jelölje be a **egyszeri bejelentkezési**.

![Alkalmazás-erőforráspanel](./media/configure-single-sign-on-portal/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Egyszeri bejelentkezési mód
**Egyszeri bejelentkezés** kezdődik a **mód** menüben, amely lehetővé teszi az egyszeri bejelentkezési mód kell konfigurálni. Az elérhető lehetőségek a következők:

* **SAML-alapú bejelentkezés** – Ez a beállítás érhető el, ha az alkalmazás támogatja a teljes összevont egyszeri bejelentkezés az Azure Active Directoryval, a WS-Federation, SAML 2.0 protokoll használatával, vagy az OpenID connect protokollok.
* **Jelszóalapú bejelentkezés** – Ez a beállítás akkor érhető el, ha az Azure AD jelszó űrlap kitöltését az alkalmazás támogatja.
* **Csatolt bejelentkezés** – korábbi nevén "Meglévő egyszeri bejelentkezéshez", ez a beállítás lehetővé teszi a rendszergazdák számára, hogy az alkalmazás mutató hivatkozást elhelyezni az a felhasználó Azure AD hozzáférési Panel vagy az Office 365 alkalmazásindítójában.

Ezek módokkal kapcsolatos további információkért lásd: [hogyan az egyszeri bejelentkezés az Azure Active Directory munkahelyi](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-alapú bejelentkezés
A **SAML-alapú bejelentkezés** lehetőség van osztva négy részből áll:

### <a name="domains-and-urls"></a>Tartomány és URL-címek
Ez az, ahol az alkalmazás-tartomány és URL-címek minden részletét kerülnek az Azure AD-címtárhoz. Egyszeri bejelentkezés munkahelyi alkalmazás elérhetővé az összes bemeneti közvetlenül a képernyőn jelennek meg, mivel minden kötelező bemeneti kijelölésével tekinthet meg a **speciális URL-beállítások megjelenítése** jelölőnégyzetet. A támogatott bemeneti teljes listáját tartalmazza:

* **bejelentkezés URL-cím** – Ha a felhasználó megnyitja a jelentkezzen be az alkalmazás. Ha az alkalmazás, szolgáltatás-szolgáltató által kezdeményezett egyszeri bejelentkezést, hajtsa végre, amikor a felhasználó megnyitja az URL-cím van beállítva, és jelentkezzen be a felhasználó hitelesítéséhez az Azure AD átirányítja a a szolgáltató. 
  * Ha ez a mező fel van töltve, majd az Azure AD használja az URL-címet az alkalmazás elindításához az Office 365 és az Azure AD hozzáférési Panel.
  * Ha kihagyja ezt a mezőt, majd az Azure AD ehelyett identitás-szolgáltató által kezdeményezett bejelentkezést hajt végre, ha az alkalmazás az Office 365, az Azure AD hozzáférési Panel elindítása vagy az Azure ad egyszeri bejelentkezés URL-CÍMÉT.
* **Azonosító** – ennek az URI egyedileg kell azonosítania az alkalmazást, amelyhez egyszeri bejelentkezés konfigurálása történik. Ez az érték, amely az Azure AD SAML-jogkivonat célközönség paraméterként alkalmazásnak küld, és az alkalmazás várható érvényesíti. Ez az érték is az entitás azonosítója a az alkalmazás által biztosított bármely SAML-metaadatokat jelenik meg.
* **Válasz URL-cím** – a válasz URL-címe, ahol az alkalmazás megjelenik az SAML-jogkivonat vár. Ez is nevezzük a helyességi feltétel fogyasztói Service (ACS) URL-CÍMÉT. Után ezek megadott, kattintson a Tovább gombra, lépjen tovább a következő képernyőn. Ez a képernyő ismerteti, mit kell konfigurálni az alkalmazás oldalán engedélyezéséhez, hogy fogadja el az Azure AD-ből egy SAML-jogkivonat.
* **Továbbítási állapot** – a relay-állapota egy nem kötelező paraméter, amely segíthet meg, hogy az alkalmazás hol átirányítja a felhasználót a hitelesítés elvégzése után. Általában az érték-e egy érvényes URL-cím, az alkalmazás azonban az egyes alkalmazások használata ebben a mezőben eltérően (az alkalmazás egyszeri bejelentkezési dokumentációjában talál). A továbbítási állapot beállítása el egy új szolgáltatás, amely egyedi az új Azure Portalra.

### <a name="user-attributes"></a>Felhasználói attribútumok
Ez az, ahol a rendszergazdák megtekinthetik és szerkeszthetik az attribútumokat, amelyek küld az SAML-jogkivonat, amely az Azure AD az alkalmazásnak minden egyes alkalommal jelentkeznek be.

A támogatott csak a szerkeszthető attribútum a **felhasználóazonosító** attribútum. Ez az attribútum értéke a mező az Azure ad-ben, amely egyedileg azonosítja az egyes felhasználók az alkalmazáson belül. Például ha az alkalmazás telepítve lett, a felhasználónevet és az egyedi azonosítója, az "E-mail-cím" használatával, majd a lenne értéke a "user.mail" mezőre az Azure ad-ben.

### <a name="saml-signing-certificate"></a>SAML-aláíró tanúsítvány
Ez a szakasz az Azure ad-ben az minden alkalommal, amikor a felhasználó hitelesíti magát az alkalmazáshoz kibocsátott SAML-jogkivonatok aláírásához használt tanúsítvány részleteit jeleníti meg. Ellenőrizni kell, hogy az aktuális tanúsítvány tulajdonságainak vele, többek között a lejárati dátumot.

### <a name="application-configuration"></a>Alkalmazás konfigurációja
Az utolsó rész a dokumentáció és/vagy maga az alkalmazás Azure Active Directory használata Identitásszolgáltatóként való konfigurálásához szükséges vezérlők.

A **alkalmazás konfigurálása** úszó menü új tömör, beágyazott utasításokat biztosít az alkalmazás konfigurálása. Ez az egyedi az új Azure Portalra egy másik új szolgáltatás.

> [!NOTE]
> Beágyazott dokumentáció egy teljes példa: a Salesforce.com-alkalmazás. További alkalmazások dokumentációját folyamatosan hozzáadása folyamatban van.
> 
> 

![Beágyazott docs](./media/configure-single-sign-on-portal/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Jelszóalapú bejelentkezés
Az alkalmazás támogatott, ha a jelszóalapú egyszeri bejelentkezési módot válassza, majd válassza ki **mentése** azonnal konfigurálja, hogy hajtsa végre a jelszóalapú egyszeri bejelentkezés. Jelszóalapú egyszeri bejelentkezés központi telepítésével kapcsolatos további információkért lásd: [hogyan az egyszeri bejelentkezés az Azure Active Directory munkahelyi](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Jelszóalapú bejelentkezés](./media/configure-single-sign-on-portal/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Csatolt bejelentkezés
Ha az alkalmazás támogatja, a társított egyszeri bejelentkezési mód kiválasztása lehetővé teszi az URL-címét, amelyeket szeretne az Azure AD hozzáférési Panel vagy az Office 365, amikor a felhasználók kattintanak, az alkalmazás átirányítása. (Korábbi nevén "meglévő egyszeri bejelentkezés") a csatolt egyszeri Bejelentkezéssel kapcsolatos további információkért lásd: [hogyan az egyszeri bejelentkezés az Azure Active Directory munkahelyi](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Csatolt bejelentkezés](./media/configure-single-sign-on-portal/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Visszajelzés

Reméljük, például a továbbfejlesztett használatával az Azure AD felhasználói élményt. Ne zárja be a visszajelzés hamarosan elérhető! A visszajelzések és javítására szolgáló ötleteket közzététele a **felügyeleti portálon** szakaszában az [Visszajelzési fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Azt már nagyon Izgatottan várjuk, új cuccok minden nap létrehozásával kapcsolatos és az alakzat útmutatóval használja, és határozza meg, milyen készíteni.

