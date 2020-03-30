---
title: B2B együttműködési felhasználói jogcím-hozzárendelés – Azure Active Directory
description: Az Azure Active Directory (Azure AD) B2B-felhasználók SAML-jogkivonatában kiadott felhasználói jogcímek testreszabása.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508865d9f89501cf70e09087c6a609beb6662b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273187"
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B együttműködési felhasználói jogcím-hozzárendelés az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a B2B együttműködési felhasználók SAML-jogkivonatában kiadott jogcímek testreszabását. Amikor egy felhasználó hitelesíti magát az alkalmazás, az Azure AD kiad egy SAML-jogkivonatot az alkalmazásnak, amely információkat (vagy jogcímeket) tartalmaz az egyedileg azonosító felhasználóról. Alapértelmezés szerint ez magában foglalja a felhasználó felhasználónevét, e-mail címét, utónevét és vezetéknevét.

Az [Azure Portalon](https://portal.azure.com)megtekintheti vagy szerkesztheti az alkalmazássaml-tokenben küldött jogcímeket. A beállítások eléréséhez válassza az **Azure Active Directory** > **Enterprise alkalmazások** > az egyszeri bejelentkezéshez konfigurált alkalmazást > **egyszeri bejelentkezést.** Tekintse meg az SAML token beállításait a **Felhasználói attribútumok** szakaszban.

![Az SAML token attribútumok megjelenítése a felhasználói felületen](media/claims-mapping/view-claims-in-saml-token.png)

Két oka lehet annak, hogy miért kell az SAML-jogkivonatban kiadott jogcímeket szerkesztenie:

1. Az alkalmazás igényel egy másik jogcím URI-k vagy jogcímértékek.

2. Az alkalmazás megköveteli, hogy a NameIdentifier jogcím, hogy valami más, mint a felhasználó egyszerű neve (UPN), amely az Azure AD-ben tárolt.

A jogcímek hozzáadásáról és szerkesztéséről az [Azure Active Directoryban nagyvállalati alkalmazások SAML-jogkivonatában kiadott jogcímek testreszabása](../develop/active-directory-saml-claims-customization.md)című témakörben talál további információt.

A B2B együttműködési felhasználók számára a NameID és az UPN cross-tenant leképezése biztonsági okokból megakadályozható.

## <a name="next-steps"></a>További lépések

- A B2B együttműködési felhasználói tulajdonságairól az [Azure Active Directory B2B együttműködési felhasználóinak tulajdonságai című](user-properties.md)témakörben talál további információt.
- A B2B-együttműködési felhasználók felhasználói jogkivonatairól az [Azure AD B2B együttműködés felhasználói jogkivonata című témakörben](user-token.md)talál további információt.

