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
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 42fbb8b08a2dc24ced436c4a6104f03ae3bca1e9
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982810"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>A B2B-együttműködés és az Azure Active Directory B2C összehasonlítása

Az Azure Active Directory (Azure AD) B2B-együttműködés és az Azure AD B2C is lehetővé teszi, hogy külső felhasználókkal dolgozzon az Azure AD-ben. De mi a különbség?

Az **Azure AD B2B** olyan vállalatok számára készült, ahol az együttműködéshez igény van a külső felhasználókkal történő biztonságos fájl- és erőforrás megosztásra. Az Azure rendszergazda az Azure portálon beállítja a B2B funkciót, az Azure AD pedig gondoskodik a vállalat és a külső partnerek szövetségéről. A felhasználók saját munkahelyi, iskolai vagy bármely e-mail fiókjukkal lépnek be egy egyszerű meghívási és érvényesítési folyamat után a megosztott erőforrásokhoz.
 
Az **Azure AD B2C** elsődlegesen ügyfelek által használt alkalmazásokat készítő vállalatoknak és fejlesztőknek szól. Az Azure AD B2C esetén a fejlesztők teljeskörű identitáskezelő rendszerként használhatják az Azure AD-t az alkalmazásaikhoz, miközben az ügyfelek beléphetnek saját, már létrehozott identitásukkal (mint a Facebook vagy Gmail).

Az alábbi táblázat részletes összehasonlítást ad meg.


B2B együttműködési lehetőségek |     Azure AD B2C önálló ajánlat
-------- | --------
Kinek készült: olyan szervezeteknek, ahol igény van a partner szervezetek felhasználóinak hitelesítésére az identitásszolgáltatótól függetlenül. | Mire készült: A mobil- és webalkalmazás ügyfelek meghívása az Azure AD-be, akár egyéni, intézményi vagy szervezeti ügyfelekről legyen is szó.
Támogatott identitások: Munkahelyi vagy iskolai fiókkal rendelkező alkalmazottak, munkahelyi vagy iskolai fiókkal vagy e-mail címmel rendelkező partnerek. Hamarosan támogatni fogja a közvetlen összevonást.  | Támogatott identitások: Fogyasztói felhasználók helyi vonatkozású fiókkal (bármilyen e-mail-cím vagy felhasználónév), vagy bármely támogatott közösségi identitás közvetlen összevonással.
Mely címtárban vannak a partner felhasználók: A külső szervezet partner felhasználóit ugyanaz a címtár kezeli, mint az alkalmazottakat, de külön jelölést kapnak. Ugyanúgy kezelhetők, mint az alkalmazottak, hozzáadhatók ugyanahhoz a csoporthoz és így tovább  | Mely címtárban vannak a fogyasztó felhasználók: Az alkalmazás címtárban. Elkülönítve a szervezet alkalmazottaitól és partner címtárától (ha van).
Egyszeri bejelentkezés (SSO) támogatása az összes Azure AD-hez csatlakozó alkalmazásra. Hozzáférést adhat például az Office 365-höz, vagy a helyszíni alkalmazásokhoz és egyéb SaaS-alkalmazásokhoz, amilyen például a Salesforce vagy a Workday.  |  Támogatja az Azure AD B2C bérlőkön belül az ügyfél tulajdonú alkalmazásokra az SSO-t. Egyszeri bejelentkezés az Office 365-höz és más Microsoft- és nem Microsoft SaaS – alkalmazásokhoz nem támogatott.
Partner életciklus: A vendéglátó/meghívó szervezet felügyeli.  | Ügyféléletciklus: Önkiszolgáló vagy az alkalmazás felügyeli.
Biztonsági házirend és megfelelőség: A vendéglátó/meghívó szervezet felügyeli (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Biztonsági házirend és megfelelőség: Az alkalmazás kezeli.
Védjegyek: A vendéglátó/meghívó szervezet védjegyét használja.  |    Védjegyek: Az alkalmazás kezeli. Általában termékek márkái láthatók, a szervezet inkább háttérbe szorul.
További információk: [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentáció](what-is-b2b.md)  | További információk: [Termék oldala](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

