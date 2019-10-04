---
title: VÁLLALATKÖZI együttműködés és B2C – Azure Active Directory összehasonlítása | Microsoft Docs
description: Mi a különbség az Azure Active Directory B2B együttműködés és az Azure AD B2C között?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/22/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e7f5aa324db869e30a8b2fe214416129baca8d9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380745"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>A B2B-együttműködés és az Azure Active Directory B2C összehasonlítása

Az Azure Active Directory (Azure AD) B2B-együttműködés és az Azure AD B2C is lehetővé teszi, hogy külső felhasználókkal dolgozzon az Azure AD-ben. De mi a különbség?

Az **Azure AD B2B** olyan vállalatok számára készült, ahol az együttműködéshez igény van a külső felhasználókkal történő biztonságos fájl- és erőforrás megosztásra. Az Azure rendszergazda az Azure portálon beállítja a B2B funkciót, az Azure AD pedig gondoskodik a vállalat és a külső partnerek szövetségéről. A felhasználók saját munkahelyi, iskolai vagy bármely e-mail fiókjukkal lépnek be egy egyszerű meghívási és érvényesítési folyamat után a megosztott erőforrásokhoz.
 
Az **Azure AD B2C** elsődlegesen ügyfelek által használt alkalmazásokat készítő vállalatoknak és fejlesztőknek szól. Az Azure AD B2C esetén a fejlesztők teljeskörű identitáskezelő rendszerként használhatják az Azure AD-t az alkalmazásaikhoz, miközben az ügyfelek beléphetnek saját, már létrehozott identitásukkal (mint a Facebook vagy Gmail).

Az alábbi táblázat részletes összehasonlítást ad meg.


B2B együttműködési lehetőségek |     Azure AD B2C önálló ajánlat
-------- | --------
A következő célra szánt: Azok a szervezetek, amelyek személyazonosság-szolgáltatótól függetlenül szeretnék hitelesíteni a felhasználókat a partner szervezettől. | A következő célra szánt: A mobil-és webalkalmazások ügyfeleinek meghívása, akár magánszemélyek, akár intézményi vagy szervezeti ügyfelek az Azure AD-be.
Támogatott identitások: Munkahelyi vagy iskolai fiókkal rendelkező alkalmazottak, munkahelyi vagy iskolai fiókkal rendelkező partnerek, vagy bármely e-mail-cím. Hamarosan támogatni fogja a közvetlen összevonást.  | Támogatott identitások: Felhasználói felhasználók helyi alkalmazás-fiókkal (bármely e-mail-címmel vagy felhasználónévvel) vagy bármely támogatott közösségi identitással közvetlen összevonással.
A külső felhasználók kezelése az alkalmazottakkal megegyező könyvtárban történik, de kifejezetten megjegyzésekkel ellátva. Az alkalmazottakkal megegyező módon kezelhetők, de hozzáadhatók ugyanahhoz a csoporthoz, és így tovább  | A külső felhasználókat az alkalmazás könyvtára kezeli. Ezeket külön kezelik a szervezet alkalmazotti és partneri könyvtára (ha van ilyen).
Egyszeri bejelentkezés (SSO) támogatása az összes Azure AD-hez csatlakozó alkalmazásra. Hozzáférést adhat például az Office 365-höz, vagy a helyszíni alkalmazásokhoz és egyéb SaaS-alkalmazásokhoz, amilyen például a Salesforce vagy a Workday.  |  Támogatja az Azure AD B2C bérlőkön belül az ügyfél tulajdonú alkalmazásokra az SSO-t. Az Office 365-hez vagy más Microsoft SaaS-alkalmazásokhoz való egyszeri bejelentkezés nem támogatott.
Partneri életciklus: A gazdagép/meghívó szervezet kezeli.  | Ügyfél életciklusa: Az alkalmazás önkiszolgáló vagy felügyelt.
Biztonsági házirend és megfelelőség: A gazdagép/meghívó szervezet (például [feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)) kezeli.  | Biztonsági házirend és megfelelőség: Az alkalmazás kezeli.
Branding A rendszer a gazdagép és a meghívó szervezet márkáját használja.  |    Branding Az alkalmazás kezeli. Általában termékek márkái láthatók, a szervezet inkább háttérbe szorul.
További információ: [Blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [dokumentáció](what-is-b2b.md)  | További információ: [Termék lap](https://azure.microsoft.com/services/active-directory-b2c/), [dokumentáció](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)

