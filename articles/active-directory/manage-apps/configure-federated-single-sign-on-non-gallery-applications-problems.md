---
title: Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása során |} A Microsoft Docs
description: Összevont egyszeri bejelentkezés az egyéni SAML-alkalmazás, amely nem szerepel az Azure AD Alkalmazáskatalógusában konfigurálása során felmerülő gyakori problémák megoldása
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: ec9386b2c54e05f9308fb00ed627f74503b7dfc1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54460941"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Összevont egyszeri bejelentkezés nem katalógusból származó alkalmazásra konfigurálása során

Ha problémát tapasztal az alkalmazás konfigurálásakor. Ellenőrizze, hogy követte a cikk összes lépését [konfigurálása egyszeri bejelentkezéshez, amely az Azure Active Directory alkalmazáskatalógusában nem találhatók alkalmazások.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Az alkalmazás egy másik példánya nem vehető fel.

Adjon hozzá egy második példányt egy alkalmazást, meg kell tudni:

-   Állítsa be a második példány egyedi azonosítója. Az első példánynál használt azonos azonosító nem lehet konfigurálni.

-   Az első példánynál használttól eltérő tanúsítvány konfigurálása.

Ha az alkalmazás nem támogatja az előző bármely, egy második példányt nem konfigurálható.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Ahol állítsa be a EntityID (felhasználóazonosító) formátum

Nem választhat ki, amely az Azure AD felhasználói hitelesítés után az alkalmazás a válaszban küld EntityID (felhasználóazonosító) formátumban.

Az Azure AD a kiválasztott érték alapján a NameID-attribútum (felhasználóazonosító) formátumát vagy formátumát az SAML AuthRequest az alkalmazás által kért jelöl ki. További részletekért látogasson el a cikk [egyszeri bejelentkezéses SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) NameIDPolicy, területen

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Ha az alkalmazás metaadatait, vagy a tanúsítvány lekérése az Azure ad-ből

Az Azure ad-ből az alkalmazás metaadatait, vagy a tanúsítvány letöltéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg a [ **az Azure portal** ](https://portal.azure.com/) , és jelentkezzen be egy **globális rendszergazdai** vagy **Társadminisztrátorként.**

2.  Nyissa meg a **Azure Active Directory-bővítmény** kattintva **minden szolgáltatás** a fő bal oldali navigációs menü tetején.

3.  Írja be a **"Azure Active Directory**" szöveget a szűrő keresőmezőbe, és válassza a **Azure Active Directory** elemet.

4.  Kattintson a **vállalati alkalmazások** az Azure Active Directory bal oldali navigációs menüjében.

5.  Kattintson a **minden alkalmazás** az alkalmazások listájának megtekintéséhez.

   * Ha azt szeretné, hogy itt jelennek meg az alkalmazás nem látja, használja a **szűrő** vezérlőelem felső részén a **minden alkalmazás lista** és állítsa be a **megjelenítése** beállítást **összes Az alkalmazások.**

6.  Válassza ki az alkalmazás egyszeri bejelentkezésre konfigurálta.

7.  Ha az alkalmazás betöltött, kattintson a **egyszeri bejelentkezési** az alkalmazás bal oldali navigációs menüjében.

8.  Lépjen a **SAML-aláíró tanúsítvány** területen, majd kattintson a **letöltése** oszlop értékét. Attól függően, melyik az alkalmazás van szüksége, az egyszeri bejelentkezés konfigurálása láthatja az metaadatainak XML-fájl letöltése lehetőséget, vagy a tanúsítványt.

Az Azure AD nem biztosít a metaadatok beolvasása egy URL-címe. A metaadatok is csak olvasható be XML-fájlba.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudom, hogyan kérelmet küldött SAML-jogcímek testreszabása

A SAML-attribútum jogcímek, az alkalmazás testreszabásával kapcsolatban lásd: [Jogcímtársítások az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) további információt.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
