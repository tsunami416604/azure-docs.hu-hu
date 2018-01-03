---
title: "Azure Active Directory jelentéskészítés késések |} Microsoft Docs"
description: "További tudnivalók az jelentési eseményeket az Azure-portálon jelenik meg a szükséges idő"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory jelentéskészítés késések

A [reporting](active-directory-preview-explainer.md) az Azure Active Directoryban, meg kell határoznia, hogyan működik a környezet összes információt lekérni. Mennyi ideig tart a jelentési adatok jelennek meg az Azure-portálon késés is nevezik. 

Ez a témakör ismerteti az Azure portálon található összes jelentési kategóriák késési adatok. 


## <a name="activity-reports"></a>Tevékenységjelentések

Nincsenek Tevékenységjelentés két terület:

- **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
- **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan

A következő táblázat a késési adatok Tevékenységjelentések.

| Jelentés | Minimális | Átlag | Megjegyzések |
| :-- | --- | --- | :-- |
| Naplók | 30 perc  | 1 óra  |Bizonyos esetekben a naplózási tevékenység adatai jelennek meg legfeljebb 2 óráig is eltarthat.|
| Bejelentkezések | 15 perc  | 2 óra |Bizonyos esetekben a bejelentkezési tevékenység adatai jelennek meg akár 24 óráig is eltarthat. Ez magában foglalja az örökölt office-alkalmazások érkező bejelentkezések-tevékenységek adatait. |







## <a name="security-reports"></a>Biztonsági jelentések

Nincsenek biztonsági reporting két terület:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A következő táblázat a késési adatok biztonsági jelentések.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Érintett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztikus használja, amely kapcsolódik a felhasználói fiókok gyanús tevékenységek észlelése. Minden észlelt gyanús művelet egy rekord hívott kockázat esemény van tárolva.

A következő táblázat a késési adatok kockázati eseményekről.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Névtelen IP-címről történő bejelentkezések |5 perc |15 perc |2 óra |
| Ismeretlen helyekről történt bejelentkezések |5 perc |15 perc |2 óra |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai |2 óra |4 óra |8 óra |
| Bejelentkezés szokatlan helyekről |5 perc |1 óra |8 óra  |
| Bejelentkezések fertőzött eszközökről |2 óra |4 óra |8 óra  |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |2 óra |4 óra |8 óra  |



## <a name="next-steps"></a>További lépések

Ha azt szeretné, további információkat az Azure-portálon a Tevékenységjelentések, lásd:

- [Bejelentkezési tevékenység jelentések az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins.md)
- [Naplózási Tevékenységjelentések az Azure Active Directory portálon](active-directory-reporting-activity-audit-logs.md)

Ha azt szeretné, további információkat a biztonsági jelentések az Azure portálon, lásd:

- [Kockázati biztonsági jelentést az Azure Active Directory portálon a felhasználók](active-directory-reporting-security-user-at-risk.md)
- [Az Azure Active Directory portálon kockázatos bejelentkezések jelentés](active-directory-reporting-security-risky-sign-ins.md)

Ha azt szeretné, további információkat a kockázati eseményekről, lásd: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md).
