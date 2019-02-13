---
title: Az Azure Active Directory - alkalmazás felhasználói beleegyezés konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a felhasználók engedélyt adjanak az Alkalmazásengedélyek módon. Egyszerűsítheti a felhasználói élmény azáltal, hogy biztosítja a rendszergazdai jóváhagyás. Ezek a metódusok a végfelhasználók számára az Azure Active Directory (Azure AD) bérlő összes vonatkoznak.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: celested
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d35f8b440fe748f91c9e01003fe83a3a5343c8df
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203726"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>A végfelhasználók beleegyezik abba, hogy egy alkalmazás az Azure Active Directoryban módszer konfigurálása
Megtudhatja, hogyan konfigurálhatja a felhasználók engedélyt adjanak az Alkalmazásengedélyek módon. Egyszerűsítheti a felhasználói élmény azáltal, hogy biztosítja a rendszergazdai jóváhagyás. Ez a cikk a különböző módokon konfigurálhatja a felhasználói beleegyezés biztosít. A módszerek alkalmazni az Azure Active Directory (Azure AD) bérlő összes végfelhasználók számára. 

Az alkalmazások jóváhagyják további információkért lásd: [Azure Active Directory hozzájárulási keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

Hozzájárulás a rendszergazda megköveteli, hogy jelentkezzen be globális rendszergazdai, egy alkalmazás-rendszergazda vagy egy felhőalkalmazás-rendszergazda.

Alkalmazásokhoz való hozzáférés korlátozásához, meg kell felhasználó-hozzárendelés szükséges, és a felhasználók vagy csoportok rendelje hozzá az alkalmazáshoz.  További információkért lásd: [módszerek a felhasználók és csoportok hozzárendelése](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Rendszergazdai jóváhagyás biztosítása a vállalati alkalmazások az Azure Portalon

Rendszergazda hozzájárulását egy vállalati alkalmazást:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazda, az alkalmazás-rendszergazda vagy egy felhőalkalmazás-rendszergazda.
2. Kattintson a **minden szolgáltatás** a bal oldali navigációs menü tetején. A **Azure Active Directory-bővítmény** nyílik meg.
3. A szűrő keresőmezőbe írja be a **"Azure Active Directory"** , és válassza ki a **Azure Active Directory** elemet.
4. Kattintson a navigációs menü **vállalati alkalmazások**.
5. Kattintson a **Grant rendszergazdai jóváhagyás**. Ön kéri, jelentkezzen be az alkalmazás felügyelheti.
6. Jelentkezzen be egy olyan fiókkal, amely rendelkezik rendszergazdai hozzájárulás az alkalmazáshoz tartozó engedélyeket. 
7. Hozzájárul az Alkalmazásengedélyek.

Ez a beállítás csak akkor működik, ha az alkalmazás: 

- Regisztrál a bérlőben vagy
- Egy másik Azure AD-bérlő regisztrálva, és legalább egy végfelhasználó által jóváhagyott. Miután a felhasználó hozzájárult egy alkalmazást, az Azure AD sorolja fel az alkalmazás a **vállalati alkalmazások** az Azure Portalon.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Rendszergazdai jóváhagyás biztosít, amikor regisztrál egy alkalmazást az Azure Portalon

Rendszergazdai jóváhagyás megadni, amikor regisztrál egy alkalmazást: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Keresse meg a **Alkalmazásregisztrációk** panelen.
3. Válassza ki a jóváhagyási kérelmet.
4. Válassza ki **szükséges engedélyek**.
5. Kattintson a **engedélyek megadása** a panel tetején.


## <a name="grant-admin-consent-through-a-url-request"></a>Rendszergazdai jóváhagyás-kérés URL-cím megadása

Rendszergazdai jóváhagyás-kérés URL-cím megadását:

1. Létrehozására irányuló kérelem *login.microsoftonline.com* az alkalmazás-konfigurációval, és a hozzáfűző `&prompt=admin_consent`. 
2. Rendszergazdai hitelesítő adataival bejelentkezni, miután az alkalmazás kapott hozzájárulás az összes felhasználó számára.


## <a name="force-user-consent-through-a-url-request"></a>Felhasználói beleegyezés URL-cím-kérés kényszerítése

A végfelhasználók számára, hogy engedélyt adjanak az alkalmazás minden alkalommal, amikor a hitelesítéshez szükséges, fűzze hozzá `&prompt=consent` , a hitelesítési kérelem URL-címe.

## <a name="next-steps"></a>További lépések

[Jóváhagyás és az Azure ad-alkalmazások integrálását ismertető](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Jóváhagyás és az Azure ad v2.0 csatornákban összevont alkalmazások](../develop/active-directory-v2-scopes.md)

[Azure ad stackoverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)
