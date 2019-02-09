---
title: Összevonási tanúsítványok kezelése az Azure ad-ben |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre az összevonási tanúsítványok lejáratának dátumát, és a közeljövőben lejáró tanúsítványok megújítása.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: jeedes
ms.openlocfilehash: 73aa9d232567f7c0994c29c29076ef4c8d9319fc
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963947"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Összevont egyszeri bejelentkezés az Azure Active Directory-tanúsítványok kezelése
Ez a cikk ismerteti a gyakori kérdéseket és a tanúsítványok, az Azure Active Directory (Azure AD) hoz létre, az SaaS-alkalmazások létrehozásához összevont egyszeri bejelentkezés (SSO) kapcsolatos adatokat. Alkalmazások hozzáadása az Azure AD-alkalmazásgyűjtemény vagy egy katalógusban nem szereplő alkalmazást sablon használatával. Az alkalmazás konfigurálása az összevont egyszeri bejelentkezési beállítás használatával.

Ez a cikk jelentősége, csak az alkalmazásoknak a SAML-Összevonás, az Azure AD egyszeri bejelentkezés használatára vannak konfigurálva, az alábbi példában látható módon:

![Az Azure AD egyszeri bejelentkezés](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatikusan létrehozott tanúsítvány katalógus és a katalógusban nem szereplő alkalmazásokhoz
Amikor egy új alkalmazás hozzáadása a katalógusból, és a egy SAML-alapú bejelentkezés konfigurálása, az Azure AD állít elő, egy tanúsítványt az alkalmazáshoz, három évig érvényes. Letöltheti a tanúsítványt a **SAML-aláíró tanúsítvány** szakaszban. Katalógus alkalmazások ebben a szakaszban melyre kattintva letölthető a tanúsítvány vagy a metaadatok, az alkalmazás a követelmény függően előfordulhat, hogy megjelenítése.

![Az Azure AD egyszeri bejelentkezés](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Testre szabhatja az összevonási tanúsítványok lejáratának dátumát és vihetők át az új tanúsítvány
Alapértelmezés szerint a tanúsítványok állíthatók három év után lejár. Választhat egy másik lejárati dátumot a tanúsítvány a következő lépések végrehajtásával.
A képernyőképek példa az használja a Salesforce, de ezeket a lépéseket alkalmazhatja bármely összevont SaaS-alkalmazásba.

1. Az a [az Azure portal](https://aad.portal.azure.com), kattintson a **vállalati alkalmazás** a bal oldali panelen, és kattintson a **új alkalmazás** a a **áttekintése** oldalon:

   ![Nyissa meg az egyszeri bejelentkezés konfigurálása varázsló](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Keresse meg a katalógusból származó alkalmazásra, és válassza ki a hozzáadni kívánt alkalmazást. Ha nem találja a szükséges alkalmazást, adja hozzá az alkalmazás használatával az **katalógusban nem szereplő alkalmazás** lehetőséget. Ez a funkció csak a prémium szintű Azure AD (a P1 és P2) Termékváltozat érhető el.

    ![Az Azure AD egyszeri bejelentkezés](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Kattintson a **egyszeri bejelentkezési** hivatkozásra a bal oldali panelen, és módosítsa **egyszeri bejelentkezési mód** való **SAML-alapú bejelentkezés**. Ebben a lépésben létrehoz egy három éves tanúsítványt, az alkalmazás.

4. Hozzon létre egy új tanúsítványt, kattintson a **új tanúsítvány létrehozása** hivatkozásra a **SAML-aláíró tanúsítvány** szakaszban.

    ![Új tanúsítvány létrehozása](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. A **hozzon létre egy új tanúsítványt** hivatkozás megnyitja a naptárban. Minden dátum megadása, és legfeljebb három év a mai dátumtól idő. A kiválasztott dátum és idő az új lejárati dátum és idő, az új tanúsítvány. Kattintson a **Save** (Mentés) gombra.

    ![Töltse le, majd a tanúsítvány feltöltése](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Most már az új tanúsítvány letöltése érhető el. Kattintson a **tanúsítvány** hivatkozás, ahonnan letöltheti azt. Ezen a ponton a tanúsítvány nem lesz aktív. Ha meg szeretné vihető át ezt a tanúsítványt, válassza ki a **új tanúsítvány aktívvá** jelölőnégyzetet, majd kattintson **mentése**. Ettől a ponttól az Azure ad-ben elindítja az új tanúsítványt használja a válasz az aláíráshoz.

7.  Megtudhatja, hogyan tölthet fel a tanúsítványt az adott SaaS-alkalmazáshoz, kattintson a **nézet konfigurációs oktatóanyag** hivatkozásra.

## <a name="certificate-expiration-notification-email"></a>Tanúsítvány lejárati értesítést e-mailben

Az Azure AD elküldi egy e-mail-értesítés 60, 30 és 7 nap SAML-tanúsítvány lejárta előtt. Az e-mail címet, amelyre az értesítés megadása:

- Az Azure Active Directory alkalmazás egyszeri bejelentkezési oldalán lépjen az értesítési E-mail mező.
- Adja meg az e-mail-cím, amely a tanúsítvány lejárati értesítő e-mailt kell kapnia. Alapértelmezés szerint ez a mező a rendszergazda, aki felvette az alkalmazás e-mail címét használja.

Az értesítési e-mailt a aadnotification@microsoft.com. Az e-mailt a levélszemét helyet fogja elkerülése érdekében ügyeljen arra, adja hozzá az e-mailt az ügyfelekhez. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Újítsa meg egy tanúsítvány hamarosan lejár
A következő megújítási lépéseket kell eredményeznie, jelentős állásidő nélkül a felhasználók számára. A képernyőképek csak példaként, de ezeket a lépéseket, a szakasz funkció Salesforce az összevont SaaS-alkalmazásaiban is alkalmazhat.

1. Az a **Azure Active Directory** alkalmazás **egyszeri bejelentkezési** lapon, az alkalmazás az új tanúsítvány előállításához. Ezt megteheti kattintva is a **új tanúsítvány létrehozása** hivatkozásra a **SAML-aláíró tanúsítvány** szakaszban.

    ![Új tanúsítvány létrehozása](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Válassza ki a kívánt lejárati dátuma és időpontja az új tanúsítvány számára, és kattintson a **mentése**. Egy dátumot, amely átfedésben van a meglévő tanúsítvány kiválasztása biztosítja, hogy leállás miatt tanúsítvány lejárati korlátozva. 

3. Ha az alkalmazás is automatikusan lecserélhetők egy tanúsítványt, állítsa az új tanúsítvány aktív.  Jelentkezzen be az alkalmazást, ellenőrizze, hogy működik-e.

4. Ha az alkalmazás nem automatikus felvétel az új tanúsítvány, de leíró több mint egy aláíró tanúsítványt, mielőtt lejár, a régit, töltse fel újra az alkalmazás ezután lépjen vissza a portálra és győződjön meg arról, hogy az aktív tanúsítvány. 

5. Az alkalmazás csak egy időben képes kezelni egy tanúsítványt, ha egy üzemszüneti időszakot válasszon, töltse le az új tanúsítványt, töltse fel az alkalmazás az, térjen vissza az Azure Portal és az új tanúsítvány állítja be aktív. 
   
6. Az Azure ad-ben az új tanúsítvány aktiválásához jelölje be a **új tanúsítvány aktívvá** jelölőnégyzetet, majd kattintson a **mentése** gombra a lap tetején. Ez összesíti az új tanúsítvány keresztül, az Azure AD-oldalán. A tanúsítvány állapotának a változik **új** való **aktív**. Ettől a ponttól az Azure ad-ben elindítja az új tanúsítványt használja a válasz az aláíráshoz. 
   
    ![Új tanúsítvány létrehozása](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](../saas-apps/tutorial-list.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](what-is-application-management.md)
* [Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](what-is-single-sign-on.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](../develop/howto-v1-debug-saml-sso-issues.md)
