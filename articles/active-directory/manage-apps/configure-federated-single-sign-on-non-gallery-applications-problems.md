---
title: Probléma az összevont egyszeri bejelentkezés konfigurálása nem galériaalkalmazásokhoz | Microsoft dokumentumok
description: Az Azure AD alkalmazásgalériában nem szereplő egyéni SAML-alkalmazásba való egyszeri bejelentkezés konfigurálásakor felmerülő gyakori problémák kezelése
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76711875"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Probléma az összevont egyszeri bejelentkezés konfigurálása nem katalógusalkalmazáshoz

Ha egy alkalmazás konfigurálása kor problémát tapasztal. Ellenőrizze, hogy követte-e a cikkben az [egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusban nem szereplő alkalmazásokra](configure-federated-single-sign-on-non-gallery-applications.md) vonatkozó összes lépést.

## <a name="cant-add-another-instance-of-the-application"></a>Nem lehet hozzáadni az alkalmazás másik példányát

Egy alkalmazás második példányának hozzáadásához a következőket kell tudnia:

-   A második példány egyedi azonosítójának konfigurálása. Az első példányhoz használt azonosító nem konfigurálható.

-   Az első példánytól eltérő tanúsítvány konfigurálása.

Ha az alkalmazás nem támogatja az előző, nem konfigurálhatja a második példány.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Hol állíthatom be az EntityID (felhasználói azonosító) formátumot?

Nem választhatja ki azt az Entitásazonosító (felhasználói azonosító) formátumot, amelyet az Azure AD a felhasználó hitelesítése után a válaszban küld az alkalmazásnak.

Az Azure AD kiválasztja a NameID attribútum (felhasználói azonosító) formátumát a kiválasztott érték vagy az alkalmazás által az SAML AuthRequest-ben kért formátum alapján. További információkért látogasson el az [Egyszeri bejelentkezés SAML protokoll](../develop/single-sign-on-saml-protocol.md#authnrequest) című cikkbe a NameIDPolicy

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Hol kaphatom meg az alkalmazás metaadatait vagy tanúsítványát az Azure AD-től?

Az alkalmazás metaadatainak vagy tanúsítványának az Azure AD-ből való letöltéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként** vagy **társrendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki az egyszeri bejelentkezést konfigurált alkalmazást.

7. Miután az alkalmazás betöltődik, kattintson az **egyszeri bejelentkezésre** az alkalmazás bal oldali navigációs menüjéből.

8. Nyissa meg az **SAML aláíró tanúsítvány szakaszt,** majd kattintson **az Oszlopérték letöltése** parancsra. Attól függően, hogy az alkalmazás nak mi szükséges egyszeri bejelentkezés konfigurálásához, megjelenik a metaadat-XML vagy a tanúsítvány letöltésének lehetősége.

Az Azure AD nem ad meg URL-címet a metaadatok leéséhez. A metaadatok csak XML-fájlként olvashatók be.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nem tudja, hogyan szabható testre az alkalmazásnak küldött SAML-jogcímek

Az alkalmazásnak küldött SAML-attribútumjogcímek testreszabásáról további információt az Azure Active Directory ban található [Jogcímleképezés](../develop/active-directory-claims-mapping.md) című témakörben talál.

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
