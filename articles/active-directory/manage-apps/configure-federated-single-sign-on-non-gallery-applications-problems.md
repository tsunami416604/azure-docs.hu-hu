---
title: Probléma van az összevont egyszeri bejelentkezés konfigurálásával egy nem katalógusbeli alkalmazáshoz | Microsoft Docs
description: Az összevont egyszeri bejelentkezés az Azure AD Application Galleryben nem szereplő egyéni SAML-alkalmazáshoz való konfigurálása során felmerülő gyakori problémák némelyike.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711875"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Probléma az összevont egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazáshoz

Ha probléma merül fel az alkalmazás konfigurálásakor. Ellenőrizze, hogy követte-e az [egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazás-katalógusban nem szereplő alkalmazásokra](configure-federated-single-sign-on-non-gallery-applications.md) című cikkben ismertetett lépéseket.

## <a name="cant-add-another-instance-of-the-application"></a>Nem lehet felvenni az alkalmazás egy másik példányát.

Egy alkalmazás második példányának hozzáadásához a következőket kell tudnia:

-   Konfigurálja a második példány egyedi azonosítóját. Az első példányhoz használt azonosító nem konfigurálható.

-   Állítson be egy másik tanúsítványt, mint az első példánynál használt tanúsítvány.

Ha az alkalmazás nem támogatja az előzőek valamelyikét, nem konfigurálhat második példányt.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Hol állítható be a EntityID (felhasználói azonosító) formátuma

Nem választhatja ki azt a EntityID (felhasználóazonosító), amelyet az Azure AD küld az alkalmazásnak a felhasználó hitelesítése utáni válaszban.

Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML-AuthRequest által kért formátum alapján. További információkért tekintse meg az [egyszeri bejelentkezés SAML protokollját](../develop/single-sign-on-saml-protocol.md#authnrequest) a NameIDPolicy szakaszban.

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Honnan szerezhetem be az alkalmazás metaadatait vagy tanúsítványát az Azure AD-ből

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portal**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként** vagy **társ-** rendszergazdaként.

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez konfigurálta az egyszeri bejelentkezést.

7. Az alkalmazás betöltése után kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjében.

8. Nyissa meg az **SAML aláíró tanúsítvány** szakaszt, majd kattintson az oszlop értékének **letöltése** elemre. Attól függően, hogy az alkalmazásnak hogyan kell konfigurálnia az egyszeri bejelentkezést, megjelenik a metaadatok XML-fájljának vagy a tanúsítványnak a letöltésére szolgáló lehetőség.

Az Azure AD nem biztosít URL-címet a metaadatok beszerzéséhez. A metaadatokat csak XML-fájlként lehet beolvasni.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudom, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-jogcímeket

Ha meg szeretné tudni, hogyan szabhatja testre az alkalmazásnak eljuttatott SAML-attribútumok jogcímeit, tekintse meg a következő témakört: [jogcím-hozzárendelés Azure Active Directory](../develop/active-directory-claims-mapping.md)

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
