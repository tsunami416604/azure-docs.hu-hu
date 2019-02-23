---
title: B2C-vel – Azure Active Directory és a B2B-együttműködés összehasonlítása |} A Microsoft Docs
description: Mi a különbség az Azure Active Directory B2B együttműködés és az Azure AD B2C között?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba06952b4d01e06d7925f70ee4bc26407b48e130
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2019
ms.locfileid: "56670589"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>A B2B-együttműködés és az Azure Active Directory B2C összehasonlítása

Az Azure Active Directory (Azure AD) B2B-együttműködés és az Azure AD B2C is lehetővé teszi, hogy külső felhasználókkal dolgozzon az Azure AD-ben. De mi a különbség?

Az **Azure AD B2B** olyan vállalatok számára készült, ahol az együttműködéshez igény van a külső felhasználókkal történő biztonságos fájl- és erőforrás megosztásra. Az Azure rendszergazda az Azure portálon beállítja a B2B funkciót, az Azure AD pedig gondoskodik a vállalat és a külső partnerek szövetségéről. A felhasználók saját munkahelyi, iskolai vagy bármely e-mail fiókjukkal lépnek be egy egyszerű meghívási és érvényesítési folyamat után a megosztott erőforrásokhoz.
 
Az **Azure AD B2C** elsődlegesen ügyfelek által használt alkalmazásokat készítő vállalatoknak és fejlesztőknek szól. Az Azure AD B2C esetén a fejlesztők teljeskörű identitáskezelő rendszerként használhatják az Azure AD-t az alkalmazásaikhoz, miközben az ügyfelek beléphetnek saját, már létrehozott identitásukkal (mint a Facebook vagy Gmail).

Az alábbi táblázat részletes összehasonlítást ad meg.


B2B együttműködési lehetőségek |     Azure AD B2C önálló ajánlat
-------- | --------
Célközönsége: Az szervezeteknek, amelyek szeretnék a, függetlenül az identitásszolgáltató egy fiókpartner-szervezet felhasználóinak hitelesítéséhez. | Célközönsége: Ügyfeleink a mobil és webes alkalmazások, e figyelmét személyeket, intézményi vagy a szervezeti felhasználók az Azure AD-be.
Támogatott identitások: Az alkalmazottak munkahelyi vagy iskolai fiókokhoz, a munkahelyi vagy iskolai fiókkal rendelkező partnerek vagy bármilyen e-mail-címmel. Hamarosan támogatni fogja a közvetlen összevonást.  | Támogatott identitások: Helyi alkalmazás fiókok (bármilyen e-mail-cím vagy a felhasználó neve), vagy bármely identitásrendszerében a felhasználók közvetlen összevonással közösségi identitás támogatott.
A külső felhasználók felügyelt alkalmazottak ugyanabban a címtárban, de kifejezetten feliratozva. Kezelhető legyen ugyanúgy, mint az alkalmazottak, felveheti ugyanazokhoz a csoportokhoz, és így tovább  | A külső felhasználók kezelése történik az alkalmazás könyvtárában. Már külön felügyelettel történik a szervezet alkalmazotti és partneri címtár (ha van).
Egyszeri bejelentkezés (SSO) támogatása az összes Azure AD-hez csatlakozó alkalmazásra. Hozzáférést adhat például az Office 365-höz, vagy a helyszíni alkalmazásokhoz és egyéb SaaS-alkalmazásokhoz, amilyen például a Salesforce vagy a Workday.  |  Támogatja az Azure AD B2C bérlőkön belül az ügyfél tulajdonú alkalmazásokra az SSO-t. Egyszeri bejelentkezés az Office 365-höz és más Microsoft- és nem Microsoft SaaS – alkalmazásokhoz nem támogatott.
Partner életciklus: A gazdagép/meghívása felügyeli szervezet.  | Ügyféléletciklus: Önkiszolgáló és az alkalmazás által felügyelt.
Biztonsági házirend és megfelelőség: A gazdagép/meghívása felügyeli szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Biztonsági házirend és megfelelőség: Az alkalmazás által kezelt.
Védjegyzési: Gazdagép/meghívott szervezet védjegyét szolgál.  |    Védjegyzési: Felügyelt alkalmazás. Általában termékek márkái láthatók, a szervezet inkább háttérbe szorul.
További információ: [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentációja](what-is-b2b.md)  | További információ: [Termékoldalán](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

