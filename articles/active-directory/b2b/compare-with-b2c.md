---
title: A B2B-együttműködés és az Azure Active Directory B2C összehasonlítása | Microsoft Docs
description: Mi a különbség az Azure Active Directory B2B együttműködés és az Azure AD B2C között?
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: overview
ms.date: 03/15/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 69c8e293186f955e86962a325fce2f54a2eefdc7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432166"
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
Melyik címtárban a partner felhasználók szerepelnek: Partner szervezet felhasználóitól származó a külső felügyelt alkalmazottak ugyanabban a címtárban, de kifejezetten feliratozva. Ugyanúgy kezelhetők, mint az alkalmazottak, hozzáadhatók ugyanahhoz a csoporthoz és így tovább  | Melyik felhasználó ügyfélentitás található: Az alkalmazás könyvtárában. Elkülönítve a szervezet alkalmazottaitól és partner címtárától (ha van).
Egyszeri bejelentkezés (SSO) támogatása az összes Azure AD-hez csatlakozó alkalmazásra. Hozzáférést adhat például az Office 365-höz, vagy a helyszíni alkalmazásokhoz és egyéb SaaS-alkalmazásokhoz, amilyen például a Salesforce vagy a Workday.  |  Támogatja az Azure AD B2C bérlőkön belül az ügyfél tulajdonú alkalmazásokra az SSO-t. Egyszeri bejelentkezés az Office 365-höz és más Microsoft- és nem Microsoft SaaS – alkalmazásokhoz nem támogatott.
Partner életciklus: A gazdagép/meghívása felügyeli szervezet.  | Ügyféléletciklus: Önkiszolgáló és az alkalmazás által felügyelt.
Biztonsági házirend és megfelelőség: A gazdagép/meghívása felügyeli szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Biztonsági házirend és megfelelőség: Az alkalmazás által kezelt.
Védjegyzési: Gazdagép/meghívott szervezet védjegyét szolgál.  |    Védjegyzési: Felügyelt alkalmazás. Általában termékek márkái láthatók, a szervezet inkább háttérbe szorul.
További információ: [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentációja](what-is-b2b.md)  | További információ: [Termékoldalán](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentációja](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

