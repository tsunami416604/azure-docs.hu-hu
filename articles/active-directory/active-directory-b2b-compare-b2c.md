---
title: "Hasonlítsa össze a B2B együttműködés és az Azure Active Directory B2C |} Microsoft Docs"
description: "Mi az Azure Active Directory B2B együttműködés és az Azure AD B2C közötti különbség?"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
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
ms.openlocfilehash: beca92e9259835fffa9178f558e9762637594570
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Hasonlítsa össze a B2B együttműködés és az Azure Active Directory B2C

Azure Active Directory (Azure AD) B2B együttműködés, mind az Azure AD B2C lehetővé teszi, hogy az Azure AD külső felhasználók alkalmazásában. De annak tegye összevetése?


B2B együttműködés képességek |     Az Azure AD B2C önálló ajánlat
-------- | --------
Szánt: a szervezeteknek, amelyek szeretnék, függetlenül az identitásszolgáltató egy fiókpartner-szervezet felhasználóinak hitelesítéséhez. | Szánt: a mobil ügyfelek és a web apps, hogy meghívása személyeket, intézményi vagy a szervezeti felhasználók az Azure AD-be.
Támogatott identitások: az alkalmazottak munkahelyi vagy iskolai fiókok, a munkahelyi vagy iskolai fiókokkal partnerek vagy akármilyen e-mail címet. Hamarosan támogatásához közvetlen összevonási.  | Támogatott identitások: Identitásrendszerében a felhasználók a helyi alkalmazások fiókok (bármilyen e-mail címet vagy a felhasználó neve), vagy bármely támogatott az közvetlen összevonási közösségi identitását.
A partner felhasználók melyik címtárban: Partner felhasználók a külső szervezet felügyelete az alkalmazottak könyvtárába, de kifejezetten feliratozva. Azok az alkalmazottak azonos módon kezelhetők, ugyanahhoz a csoporthoz lehet hozzáadni és így tovább  | A könyvtár-ügyfél felhasználói entitások: az alkalmazás könyvtárában. Külön felügyelettel történik könyvtárból a szervezetben dolgozó és a partner (ha van ilyen.
Egyszeri bejelentkezés (SSO) az összes Azure AD-kompatibilis alkalmazások használata támogatott. Például biztosíthat hozzáférést az Office 365 vagy a helyszíni alkalmazások és más SaaS-alkalmazásokhoz, például Salesforce vagy Workday való.  |  Egyszeri bejelentkezés ügyfélnek tulajdonában lévő alkalmazások az Azure AD B2C-bérlőkön belül támogatott. Egyszeri bejelentkezés az Office 365 szolgáltatásra vagy más Microsoft és nem Microsoft SaaS - alkalmazásokhoz nem támogatott.
Partner életciklus: az állomás/meghívása által kezelt szervezet.  | Ügyfél életciklus: önkiszolgáló vagy az alkalmazás által felügyelt.
Biztonsági házirend és megfelelőség: az állomás/meghívása által kezelt szervezet.  | Biztonsági házirend és megfelelőség: az alkalmazás kezeli.
Márkajelzési: Állomás/meghívása szervezet márka szolgál.  |    Branding: Alkalmazás kezeli. Általában általában vállalati arculattal rendelkező a szervezet Áttűnés a háttérben történő termék.
További információ: [blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentáció](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | További információ: [termékoldalára](https://azure.microsoft.com/en-us/services/active-directory-b2c/), [dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B együttműködés felhasználó tulajdonságai](active-directory-b2b-user-properties.md)
* [Egy szerepkör B2B együttműködés felhasználók hozzáadása](active-directory-b2b-add-guest-to-role.md)
* [B2B együttműködés meghívókat delegálása](active-directory-b2b-delegate-invitations.md)
* [Dinamikus csoportok és a B2B együttműködés](active-directory-b2b-dynamic-groups.md)
* [B2B együttműködés SaaS-alkalmazások konfigurálása](active-directory-b2b-configure-saas-apps.md)
* [B2B együttműködés felhasználói jogkivonatokhoz](active-directory-b2b-user-token.md)
* [B2B együttműködés felhasználói jogcímek leképezése](active-directory-b2b-claims-mapping.md)
* [Külső Office 365-megosztás](active-directory-b2b-o365-external-user.md)
* [B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)
* [Támogatási B2B együttműködés beolvasása](active-directory-b2b-support.md)
