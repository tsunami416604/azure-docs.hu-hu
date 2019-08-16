---
title: B2B együttműködés licencelési útmutatója – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-együttműködéshez nincs szükség fizetős Azure AD-licencekre, de fizetős szolgáltatásokat is kaphat a B2B vendég felhasználói számára
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 081061eae07fa3765d032ad155e59ebf5aa3cbc9
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512563"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Útmutató az Azure Active Directory vállalatközi együttműködés licenceléséhez

A Azure Active Directory (Azure AD) vállalatok közötti (B2B) együttműködéssel külső felhasználókat (vagy "vendég felhasználókat") hívhat meg a fizetős Azure AD-szolgáltatások használatára. Egyes funkciók ingyenesek, de a fizetős Azure AD-funkciók esetében akár öt vendég felhasználó is meghívható minden olyan Azure AD-licenccel, amelyet egy alkalmazott vagy egy nem vendég felhasználó a bérlőn belül használ.

> [!NOTE]
> Az Azure AD díjszabásával és a B2B csoportmunka-szolgáltatásokkal kapcsolatos részletekért tekintse meg [Azure Active Directory díjszabását](https://azure.microsoft.com/pricing/details/active-directory/) .

A VÁLLALATKÖZI vendég felhasználói licencelését a rendszer a 1:5 arány alapján automatikusan kiszámítja és jelenteni kívánja. Jelenleg nem lehetséges VÁLLALATKÖZI vendég felhasználói licenceket közvetlenül a vendég felhasználóihoz rendelni.

Emellett a vendég felhasználók ingyenes Azure AD-funkciókat is használhatnak további licencelési követelmények nélkül. A vendég felhasználók akkor is hozzáférhetnek az ingyenes Azure AD-funkciókhoz, ha nem rendelkezik fizetős Azure AD-licenccel. 

## <a name="examples-calculating-guest-user-licenses"></a>Példák: Vendég felhasználói licencek kiszámítása
Miután meghatározta, hogy hány vendégnek kell hozzáférnie a fizetős Azure AD-szolgáltatásokhoz, győződjön meg arról, hogy elegendő Azure AD-licenccel rendelkezik a szükséges 1:5 arányban a vendég felhasználói számára. Néhány példa:

- Az Azure AD-alkalmazásokhoz vagy-szolgáltatásokhoz szeretne meghívni 100 vendégeket, és a hozzáférés-kezelést és az üzembe helyezést az összes vendég felhasználóhoz szeretné rendelni. A vendég felhasználói számára a 50-as és a feltételes hozzáférést is szeretné megkövetelni. Ennek a kombinációnak a biztosításához 10 alapszintű Azure AD licencre és 10 prémium szintű Azure AD P1 licencre van szükség. Ha az Identity Protection funkcióit szeretné használni a vendég felhasználói számára, akkor a vendég felhasználói számára prémium szintű Azure AD P2-licenceket kell megadnia ugyanazon a 1:5-arányban.
- Olyan 60 vendég felhasználókat szeretne meghívni, akik mindegyike MFA-t igényel, ezért legalább 12 prémium szintű Azure AD P1-licenccel kell rendelkeznie. 10 prémium szintű Azure AD P1 licenccel rendelkező alkalmazottja van, ami akár 50 vendég felhasználó számára is lehetővé tenné az 1:5 licencelési arányt. Két további prémium P1-licencet kell vásárolnia, hogy 10 további vendég felhasználót fedjen fel.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure AD B2B együttműködés alábbi forrásait:

* [Díjszabás Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
