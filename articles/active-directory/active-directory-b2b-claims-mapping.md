---
title: B2B együttműködés a felhasználói jogcímek hozzárendelése az Azure Active Directoryban |} Microsoft Docs
description: A felhasználói jogcímeket, amelyeket a SAML-jogkivonat Azure Active Directory (Azure AD) B2B felhasználók testreszabása.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/06/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: bab339a07503a042f459c0933e92e0ed4a4fb27e
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B együttműködés a felhasználói jogcímek hozzárendelése az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a SAML-jogkivonat B2B együttműködés felhasználók számára kiállított jogcímek testreszabása. Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD bocsát ki egy SAML-jogkivonat a az alkalmazás, amely tartalmazza az adatokat (vagy jogcímeket), amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználónevét, e-mail címét, Utónév és Vezetéknév.

Az a [Azure-portálon](https://portal.azure.com), akkor megtekintheti vagy szerkesztheti a küldése az alkalmazásnak a SAML-jogkivonat a jogcímeket. Válassza a beállítások eléréséhez **Azure Active Directory** > **vállalati alkalmazások** > az alkalmazás konfigurált az egyszeri bejelentkezés > **egyszeri bejelentkezés** . Tekintse meg a SAML token beállításait a **felhasználói attribútumok** szakasz.

![A SAML-jogkivonat attribútumok mutatja a felhasználói felületen](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Ennek oka két miért szükség lehet a SAML-jogkivonat kiállított jogcímek szerkesztése:

1. Az alkalmazás futtatásához szükséges különböző jogcím-URI-azonosítók, vagy a jogcímértékek.

2. A NameIdentifier jogcímet, mint az egyszerű felhasználónév (UPN) az Azure ad-ben tárolt szükséges az alkalmazás számára.

Információ arról, hogyan adhat hozzá és szerkeszthet jogcímek: [a SAML-jogkivonat a vállalati alkalmazások az Azure Active Directoryban kiállított jogcímek testreszabása](develop/active-directory-saml-claims-customization.md).

A B2B együttműködés a felhasználók, NameID és UPN kereszt-bérlő leképezési biztonsági okokból nem.

## <a name="next-steps"></a>További lépések

- További információ a B2B együttműködés felhasználói tulajdonságok: [egy Azure Active Directory B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md).
- Felhasználói jogkivonatokat a B2B együttműködés felhasználók kapcsolatos információkért lásd: [megismerése az Azure AD B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md).

