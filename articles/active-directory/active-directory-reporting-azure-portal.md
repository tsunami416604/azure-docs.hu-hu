---
title: Jelentéskészítés az Azure Active Directoryban | Microsoft Docs
description: Az Azure Active Directory jelentéskészítés általános áttekintését nyújtja.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 16f5ffbfbb3ec4e0c01dd2ad151f282e5b2d0a87
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213683"
---
# <a name="azure-active-directory-reporting"></a>Jelentéskészítés az Azure Active Directoryban

Az Azure Active Directory jelentéskészítéssel betekintést nyerhet a környezet működésébe.  
A megadott adatokkal a következőket teheti:

- Meghatározhatja, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat
- Észlelheti a környezet állapotát befolyásoló lehetséges kockázatokat
- Elháríthatja azokat a hibákat, amelyek megakadályozzák a felhasználók munkavégzését  

A jelentéskészítési architektúra két alappillérre támaszkodik:

- Biztonsági jelentések
- Tevékenységjelentések

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Biztonsági jelentések

Az Azure Active Directory biztonsági jelentései segítenek a szervezet identitásainak védelmében.  
Az Azure Active Directoryban két típusú biztonsági jelentés létezik:

- **Kockázatosként megjelölt felhasználók** – A [kockázatosként megjelölt felhasználók biztonsági jelentésben](active-directory-reporting-security-user-at-risk.md) azon felhasználói fiókok áttekintését találja, amelyek biztonsága esetleg sérült.

- **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés biztonsági jelentés](active-directory-reporting-security-risky-sign-ins.md) olyan bejelentkezési kísérleteket jelöl, amelyeket elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiókok jogos tulajdonosa. 

**Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?**  
Az Azure Active Directory minden kiadása biztosítja a kockázatosként megjelölt felhasználók és a kockázatos bejelentkezések jelentéseit.  
A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Már az **Azure Active Directory ingyenes és alapszintű kiadásaiban** is szerepel a kockázatosként megjelölt felhasználók és a kockázatos bejelentkezések listája. 

- Az **Azure Active Directory 1. prémium** kiadása kibővíti ezt a modellt, mert lehetővé teszi azt is, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat a mögöttes kockázatos eseményekről, és lehetővé teszi olyan biztonsági házirendek konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.


## <a name="activity-reports"></a>Tevékenységjelentések

Az Azure Active Directoryban két típusú tevékenységjelentés létezik:

- **Naplók** – A [naplók tevékenységjelentés](active-directory-reporting-activity-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.

- **Bejelentkezések** – A [bejelentkezések tevékenységjelentéssel](active-directory-reporting-activity-sign-ins.md) meghatározhatja, hogy ki hajtotta végre a naplók jelentés által jelentett feladatokat.



A **naplók jelentés** a rendszertevékenységek rekordjait tartalmazza megfelelőségi célokból.
Többek között a megadott adatok lehetővé teszik például a következő gyakori helyzetek kezelését:

- A bérlőmben valaki hozzáfért egy rendszergazdai csoporthoz. Ki biztosított neki hozzáférést? 

- Tudni szeretném az egy adott alkalmazásba bejelentkező felhasználók listáját, mert nemrég helyeztem üzembe az alkalmazást, és látni szeretném, hogy jól működik-e

- Tudni szeretném, hogy hány jelszó-visszaállítás történik a bérlőmön


**Milyen Azure AD-licencre van szükség a naplójelentéshez való hozzáféréshez?**  
A naplójelentés olyan szolgáltatásokhoz érhető el, amelyekhez licenccel rendelkezik. Ha rendelkezik licenccel egy adott szolgáltatáshoz, annak naplóinformációihoz is hozzáfér.

További részleteket az [Azure Active Directory funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features) fejezet **Az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásainak összehasonlítása** szakaszában talál.   



A **bejelentkezési tevékenységjelentéshez** lehetővé teszi, hogy az alábbi kérdésekre például:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?


**Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységjelentéshez való hozzáféréshez?**  
A bejelentkezési tevékenységjelentés eléréséhez a bérlőnek prémium szintű Azure AD-licenccel kell rendelkeznie.


## <a name="programmatic-access"></a>Szoftveres hozzáférés

A felhasználói felület mellett az Azure Active Directory jelentéskészítés a jelentésadatok [szoftveres hozzáférését](active-directory-reporting-api-getting-started-azure-portal.md) is biztosítja. Ezen jelentések adatai nagyon hasznosak lehetnek az alkalmazások számára, például a SIEM rendszerekhez, a naplózáshoz és az üzleti intelligencia eszközökhöz. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. 


## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure Active Directory különböző jelentéstípusairól, lásd:

- [Kockázatosként megjelölt felhasználók jelentés](active-directory-reporting-security-user-at-risk.md)
- [Kockázatos bejelentkezések jelentés](active-directory-reporting-security-risky-sign-ins.md)
- [Naplók jelentés](active-directory-reporting-activity-audit-logs.md)
- [Bejelentkezési naplók jelentés](active-directory-reporting-activity-sign-ins.md)

Ha szeretne többet tudni a jelentési adatokat a jelentéskészítési API-val való eléréséről, lásd: 

- [Bevezetés az Azure Active Directory Premium Reporting API használatába](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png