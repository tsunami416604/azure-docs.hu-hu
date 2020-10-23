---
title: SAML-hitelesítés Azure Active Directory
description: Építészeti útmutató az SAML-hitelesítés megvalósításához Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ab14413de1f999747e5b3fb58b505e0a9258a55
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441215"
---
# <a name="saml-authentication-with-azure-active-directory"></a>SAML-hitelesítés Azure Active Directory

A Security Assertion Markup Language (SAML) egy nyílt szabvány, amely a hitelesítési és engedélyezési adatoknak az identitás-szolgáltató és a szolgáltató közötti cseréjére szolgál. Az SAML egy XML-alapú Markup Language a biztonsági kijelentésekhez, amelyek olyan utasítások, amelyeket a szolgáltatók a hozzáférés-vezérlési döntések elvégzéséhez használnak. 

Az SAML-specifikáció három szerepkört határoz meg:

* A rendszerbiztonsági tag, általában egy felhasználó
* Az identitás-szolgáltató (identitásszolgáltató)
* A szolgáltató (SP)


## <a name="use-when"></a>A következő esetekben használja

Egy vállalati SAML-alkalmazáshoz meg kell adni egy egyszeri bejelentkezési (SSO) élményt.

Noha az SAML-címek egyik legfontosabb felhasználási esete az SSO, különösen az egyszeri bejelentkezésnek a biztonsági tartományokban való kibővítésével, más használati esetek is léteznek (profilok). 

![az SAML architektúrájának ábrája](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: szolgáltatás kérése az alkalmazástól.

* **Webböngésző**: az az összetevő, amellyel a felhasználó kommunikál.

* **Web App**: vállalati alkalmazás, amely támogatja az SAML-t, és az Azure ad-t használja identitásszolgáltató.

* **Jogkivonat**: SAML-jogcímek (más néven SAML-jogkivonatok), amelyek a identitásszolgáltató által az alapelvre (felhasználó) vonatkozó jogcímek készleteit végzik. A hitelesítési adatokat, attribútumokat és engedélyezési döntési utasításokat tartalmaz.

* **Azure ad**: vállalati felhőalapú identitásszolgáltató, amely egyszeri bejelentkezést és többtényezős hitelesítést biztosít az SAML-alkalmazásokhoz. Szinkronizálja, karbantartja és kezeli a felhasználók identitási adatait, miközben hitelesítési szolgáltatásokat biztosít az alkalmazások függőben tartásához. 

## <a name="implement-saml-authentication-with-azure-ad"></a>SAML-hitelesítés implementálása az Azure AD-vel

* [Oktatóanyagok az SaaS-alkalmazások integrálásához a Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [SAML-alapú egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Hogyan használja az Azure AD az SAML-protokollt](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
