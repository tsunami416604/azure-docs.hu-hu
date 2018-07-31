---
title: B2B-együttműködés és az Azure Active Directory B2C összehasonlítása |} A Microsoft Docs
description: Mi a különbség az Azure Active Directory B2B együttműködés és az Azure AD B2C-vel?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b59dba541394c105370cfd3af0768a8477ddb4e6
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348387"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>B2B-együttműködés és az Azure Active Directory B2C összehasonlítása

Az Azure Active Directory (Azure AD) B2B-együttműködés és az Azure AD B2C lehetővé teszi, hogy a külső felhasználók Azure AD-ben. De hogyan viszonyulnak?


B2B együttműködési lehetőségekhez |     Az Azure AD B2C-t önálló ajánlat
-------- | --------
Szánt: szervezeteknek, amelyek szeretnék a, függetlenül az identitásszolgáltató egy fiókpartner-szervezet felhasználóinak hitelesítéséhez. | Szánt: e figyelmét a mobil felhasználók és a web apps személyeket, intézményi vagy a szervezeti felhasználók az Azure AD-be.
Támogatott identitások: munkahelyi vagy iskolai fiókokhoz, a munkahelyi vagy iskolai fiókkal rendelkező partnerek vagy bármilyen e-mail-címmel rendelkező alkalmazottak. Hamarosan a közvetlen összevonási támogatja.  | Támogatott identitások: Identitásrendszerében a felhasználók helyi alkalmazásfiókok (bármilyen e-mail-cím vagy a felhasználó neve), vagy bármely közvetlen összevonással közösségi identitás támogatott.
A partner felhasználók melyik címtárban: Partner szervezet felhasználóitól származó a külső felügyelt alkalmazottak ugyanabban a címtárban, de kifejezetten feliratozva. Azok az alkalmazottak azonos módon felügyelhetők, is hozzáadhatók ugyanazok a csoportok és így tovább  | Melyik felhasználó ügyfélentitás a directory: az alkalmazás könyvtárában. Elkülönülten kezelhetőek az alkalmazotti és partneri címtárában (ha van ilyen.
Egyszeri bejelentkezés (SSO) az összes Azure AD-hez csatlakozó alkalmazás használata támogatott. Megadhatja például, hozzáférés az Office 365 vagy a helyszíni alkalmazások és más SaaS-alkalmazások, például Salesforce vagy a Workday.  |  Vásárlói kezelésű belül az Azure AD B2C-bérlők egyszeri bejelentkezés használata támogatott. Egyszeri bejelentkezés az Office 365-höz vagy más Microsoft- és nem Microsoft SaaS - alkalmazások nem támogatott.
Partner életciklus: a gazdagép/meghívása felügyeli szervezet.  | Ügyféléletciklus: önkiszolgáló vagy az alkalmazás által felügyelt.
Biztonsági házirend és megfelelőség: a gazdagép/meghívása felügyeli szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Biztonsági házirend és megfelelőség: az alkalmazás által kezelt.
Védjegyzési: Gazdagép/meghívott szervezet védjegyét szolgál.  |    Márkajelzési: Alkalmazás által kezelt. Általában általában termékeire, a szervezet áttűnés be a háttérben a terméknek.
További információ: [blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentációja](what-is-b2b.md)  | További információ: [termékoldalán](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

