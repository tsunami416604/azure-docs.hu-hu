---
title: Mi az Azure Active Directory-jelentés? | Microsoft Docs
description: Az Azure Active Directory jelentések általános áttekintését nyújtja.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b655fa2f12eac894e97c61cc931983aa58fff7a9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74007929"
---
# <a name="what-are-azure-active-directory-reports"></a>Mi az Azure Active Directory-jelentés?

Az Azure Active Directory (Azure AD) jelentései átfogó képet nyújtanak a környezetében végzett tevékenységekről. A megadott adatokkal a következőket teheti:

- Meghatározhatja, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat
- Észlelheti a környezet állapotát befolyásoló lehetséges kockázatokat
- Elháríthatja azokat a hibákat, amelyek megakadályozzák a felhasználók munkavégzését  

A jelentéskészítési architektúra két alappillérre támaszkodik:

- [Biztonsági jelentések](#security-reports)
- [Tevékenységjelentések](#activity-reports)

![Jelentéskészítés](./media/overview-reports/01.png)


## <a name="security-reports"></a>Biztonsági jelentések

A biztonsági jelentések segítenek a szervezet identitásának védelmében. A biztonsági jelentéseknek két típusa van:

- **Kockázatosként megjelölt felhasználók** – A [kockázatosként megjelölt felhasználók biztonsági jelentésben](concept-user-at-risk.md) azon felhasználói fiókok áttekintését találja, amelyek biztonsága esetleg sérült.

- **Kockázatos bejelentkezések** – A [kockázatos bejelentkezés biztonsági jelentés](concept-risky-sign-ins.md) olyan bejelentkezési kísérleteket jelöl, amelyeket elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiókok jogos tulajdonosa. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Milyen Azure AD-licencre van szükség a biztonsági jelentések eléréséhez?  

Az Azure AD összes kiadása kockázati és kockázatos bejelentkezési jelentésekhez megjelölt felhasználókat biztosít. A jelentések részletességi szintje azonban különbözik a kiadások között: 

- Az **Azure Active Directory ingyenes és alapszintű kiadásaiban**a kockázatos bejelentkezések számára megjelölt felhasználók listája jelenik meg. 

- Az **Azure Active Directory Premium 1-es** kiadás kiterjeszti ezt a modellt azáltal, hogy lehetővé teszi az egyes jelentéseknél észlelt, mögöttes kockázatészlelések vizsgálatát is. 

- Az **Azure Active Directory Premium 2-kiadás** a legrészletesebb információkat nyújt az alapul szolgáló kockázatészlelésekről, és lehetővé teszi a biztonsági házirendek konfigurálását is, amelyek automatikusan reagálnak a konfigurált kockázati szintekre.


## <a name="activity-reports"></a>Tevékenységjelentések

A tevékenységjelentések segítenek megérteni a szervezet felhasználóinak viselkedését. Az Azure AD-ben kétféle tevékenységjelentés létezik:

- **Naplók** – A [naplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.

- **Bejelentkezések** – A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre a naplók jelentés által jelentett feladatokat.


### <a name="audit-logs-report"></a>Naplók jelentés 

A [naplók jelentés](concept-audit-logs.md) a rendszertevékenységek rekordjait tartalmazza megfelelőségi célokból. Ezek az adatok lehetővé teszik például a következő gyakori helyzetek kezelését:

- A bérlőmben valaki hozzáfért egy rendszergazdai csoporthoz. Ki biztosított neki hozzáférést? 

- Tudni szeretném az egy adott alkalmazásba bejelentkező felhasználók listáját, mert nemrég helyeztem üzembe az alkalmazást, és látni szeretném, hogy jól működik-e

- Tudni szeretném, hogy hány jelszó-visszaállítás történik a bérlőmön


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Milyen Azure AD-licencre van szükség a naplójelentéshez való hozzáféréshez?  

A naplójelentés olyan szolgáltatásokhoz érhető el, amelyekhez licenccel rendelkezik. Ha rendelkezik licenccel egy adott szolgáltatáshoz, annak naplóinformációihoz is hozzáfér. További információt az Azure Active Directory szolgáltatásaiés képességei című [témakörben talál.](https://www.microsoft.com/cloud-platform/azure-active-directory-features)   

### <a name="sign-ins-report"></a>Bejelentkezési jelentés

A [bejelentkezések jelentés](concept-sign-ins.md) lehetővé teszi, hogy megtalálja a választ a kérdésekre, mint például:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Milyen Azure AD-licencre van szükség a bejelentkezési tevékenységjelentéshez való hozzáféréshez?  

A bejelentkezési tevékenységjelentés eléréséhez a bérlőnek prémium szintű Azure AD-licenccel kell rendelkeznie.

## <a name="programmatic-access"></a>Szoftveres hozzáférés

A felhasználói felület mellett az Azure AD is biztosít [programozott hozzáférést](concept-reporting-api.md) biztosít a jelentések adataihoz egy sor REST-alapú API-k. Különböző programnyelvekkel és eszközökkel hívhatja ezeket az API-kat. 

## <a name="next-steps"></a>További lépések

- [Kockázatos bejelentkezési jelentések](concept-risky-sign-ins.md)
- [Naplók jelentés](concept-audit-logs.md)
- [Bejelentkezési naplók jelentés](concept-sign-ins.md)
