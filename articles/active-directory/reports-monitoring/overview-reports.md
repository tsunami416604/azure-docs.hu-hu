---
title: Mi az Azure Active Directory-jelentés? | Microsoft Docs
description: Az Azure Active Directory jelentések általános áttekintését nyújtja.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5ef8d2453c12fd5082eb13d6951ee5048a319d4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209591"
---
# <a name="what-are-azure-active-directory-reports"></a>Mi az Azure Active Directory-jelentés?

Az Azure Active Directory (Azure AD) jelentések tevékenység a környezetében átfogó képet biztosítanak. A megadott adatokkal a következőket teheti:

- Meghatározhatja, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat
- Észlelheti a környezet állapotát befolyásoló lehetséges kockázatokat
- Elháríthatja azokat a hibákat, amelyek megakadályozzák a felhasználók munkavégzését  

A jelentéskészítési architektúra két alappillérre támaszkodik:

- [Biztonsági jelentések](#security-reports)
- [Tevékenységjelentések](#activity-reports)

![Jelentéskészítés](./media/overview-reports/01.png)


## <a name="security-reports"></a>Biztonsági jelentések

Biztonsági jelentések segítenek a szervezet identitásait védelme. Biztonsági jelentések két típusa van:

- **Kockázatosként megjelölt felhasználók** – A [kockázatosként megjelölt felhasználók biztonsági jelentésben](concept-user-at-risk.md) azon felhasználói fiókok áttekintését találja, amelyek biztonsága esetleg sérült.

- **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés biztonsági jelentés](concept-risky-sign-ins.md) olyan bejelentkezési kísérleteket jelöl, amelyeket elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiókok jogos tulajdonosa. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD minden kiadása biztosítja a megjelölt felhasználók kockázat és a kockázatos bejelentkezések jelentéseit. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az a **Azure Active Directory ingyenes és alapszintű kiadásaiban**, kockázat és a kockázatos bejelentkezések megjelölt felhasználók listájának beolvasása. 

- Az **Azure Active Directory 1. prémium** kiadása kibővíti ezt a modellt, mert lehetővé teszi azt is, hogy megvizsgáljon néhány, az egyes jelentésekhez észlelt mögöttes kockázatos eseményt. 

- Az **Azure Active Directory 2. prémium** kiadása nyújtja a legrészletesebb információkat a mögöttes kockázatos eseményekről, és lehetővé teszi olyan biztonsági házirendek konfigurálását, amelyek automatikusan, a konfigurált kockázati szinteknek megfelelően válaszolnak.


## <a name="activity-reports"></a>Tevékenységjelentések

Tevékenységre vonatkozó jelentések segítenek jobban átláthatja a szervezeti felhasználók viselkedését. Az Azure AD-tevékenységre vonatkozó jelentések két típusa van:

- **Naplók** – A [naplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.

- **Bejelentkezések** – A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre a naplók jelentés által jelentett feladatokat.


### <a name="audit-logs-report"></a>Naplók jelentés 

A [naplók jelentés](concept-audit-logs.md) a rendszertevékenységek rekordjait tartalmazza megfelelőségi célokból. Ezek az adatok lehetővé teszik például a következő gyakori helyzetek kezelését:

- A bérlőmben valaki hozzáfért egy rendszergazdai csoporthoz. Ki biztosított neki hozzáférést? 

- Tudni szeretném az egy adott alkalmazásba bejelentkező felhasználók listáját, mert nemrég helyeztem üzembe az alkalmazást, és látni szeretném, hogy jól működik-e

- Tudni szeretném, hogy hány jelszó-visszaállítás történik a bérlőmön


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Milyen Azure AD-licencre van szüksége a naplók jelentés eléréséhez?  

A naplójelentés olyan szolgáltatásokhoz érhető el, amelyekhez licenccel rendelkezik. Ha rendelkezik licenccel egy adott szolgáltatáshoz, annak naplóinformációihoz is hozzáfér. További részletekért lásd: [Azure Active Directory funkciók és képességek](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   

### <a name="sign-ins-report"></a>Bejelentkezések jelentés

A [bejelentkezésekre](concept-sign-ins.md) lehetővé teszi, hogy az alábbi kérdésekre például:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Milyen Azure AD-licencre van szüksége a bejelentkezési Tevékenységjelentés eléréséhez?  

A bejelentkezési tevékenységjelentés eléréséhez a bérlőnek prémium szintű Azure AD-licenccel kell rendelkeznie.

## <a name="programmatic-access"></a>Szoftveres hozzáférés

A felhasználói felület mellett az Azure AD emellett lehetőséget biztosít a [programozás alapú hozzáférést](concept-reporting-api.md) jelentéseket adataihoz, REST-alapú API-kon keresztül. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. 

## <a name="next-steps"></a>További lépések

- [Kockázatos bejelentkezések jelentés](concept-risky-sign-ins.md)
- [Naplók jelentés](concept-audit-logs.md)
- [Bejelentkezési naplók jelentés](concept-sign-ins.md)
