---
title: "B2B együttműködés a felhasználói jogcímek hozzárendelése az Azure Active Directoryban |} Microsoft Docs"
description: "a jogcímek referencia az Azure Active Directory B2B együttműködés leképezése"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>B2B együttműködés a felhasználói jogcímek hozzárendelése az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a SAML-jogkivonat B2B együttműködés felhasználók számára kiállított jogcímek testreszabása. Amikor egy felhasználó hitelesíti magát az alkalmazást, az Azure AD bocsát ki egy SAML-jogkivonat a az alkalmazás, amely tartalmazza az adatokat (vagy jogcímeket), amely egyedileg azonosítja a felhasználóról. Alapértelmezés szerint ez tartalmazza a felhasználó felhasználónevét, e-mail címét, Utónév és Vezetéknév. Megtekintheti vagy szerkesztheti a jogcímek küldése az alkalmazásnak az attribútumok lapon a SAML-jogkivonat.

Előfordulhat, hogy miért szerkesztése a SAML-jogkivonat kiadott jogcímeket kell két lehetséges oka van.

1. Az alkalmazás írt eltérő szabályzatkészletet jogcím URI-azonosítók, vagy a jogcímértékek

2. Az alkalmazás által a NameIdentifier jogcímet, mint a felhasználó egyszerű felhasználóneve, az Azure Active Directoryban tárolja.

  ![nézet jogcímek SAML-jogkivonat](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Tájékoztatást adhat hozzá és szerkeszthet a jogcímeket, tekintse meg a jogcímek testreszabása, ez a cikk [előre integrált alkalmazások az Azure Active Directoryban a SAML-jogkivonat kiállított jogcímek testreszabása](develop/active-directory-saml-claims-customization.md). A B2B együttműködés a felhasználók, NameID és UPN kereszt-bérlő leképezési biztonsági okokból nem.


## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2bB együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés kód és a PowerShell-példák](active-directory-b2b-code-samples.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
