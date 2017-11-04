---
title: "Azure Active Directory jelentéskészítés késések |} Microsoft Docs"
description: "További tudnivalók az jelentési eseményeket az Azure-portálon jelenik meg a szükséges idő"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 44e31d30cf5f6d6ca216fb7ed9f6be6e38cd8697
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory jelentéskészítés késések

A [reporting](active-directory-preview-explainer.md) az Azure Active Directoryban, meg kell határoznia, hogyan működik a környezet összes információt lekérni. Mennyi ideig tart a jelentési adatok jelennek meg az Azure-portálon késés is nevezik. 

Ez a témakör ismerteti az Azure portálon található összes jelentési kategóriák késési adatok. 


## <a name="activity-reports"></a>Tevékenységjelentések

Nincsenek Tevékenységjelentés két terület:

- **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
- **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan

A következő táblázat a késési adatok Tevékenységjelentések.

| Jelentés | Minimális | Átlagos | Maximális |
| :-- | --- | --- | --- |
| Naplók             | 30 perc  | 45 percben | 1 óra     |
| Bejelentkezések               | 15 perc  | 15 perc | 2 óra *   |

>[!NOTE]
> Egyes örökölt Office-alkalmazások bejelentkezési tevékenységeinek adatai esetében akár 8 órát is igénybe vehet, amíg a naplóadatok megjelennek. 


## <a name="security-reports"></a>Biztonsági jelentések

Nincsenek biztonsági reporting két terület:

- **Kockázatos bejelentkezések** – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- **Kockázatosként megjelölt felhasználók** – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A következő táblázat a késési adatok biztonsági jelentések.

| Jelentés | Minimális | Átlagos | Maximális |
| :-- | --- | --- | --- |
| Érintett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-events"></a>Kockázati események

Az Azure Active Directory adaptív gépi tanulási algoritmusok és heurisztikus használja, amely kapcsolódik a felhasználói fiókok gyanús tevékenységek észlelése. Minden észlelt gyanús művelet egy rekord hívott kockázat esemény van tárolva.

A következő táblázat a késési adatok kockázati eseményekről.

| Jelentés | Minimális | Átlagos | Maximális |
| :-- | --- | --- | --- |
| Névtelen IP-címről történő bejelentkezések |5 perc |15 perc |2 óra |
| Ismeretlen helyekről történt bejelentkezések |5 perc |15 perc |2 óra |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai |2 óra |4 óra |8 óra |
| Bejelentkezés szokatlan helyekről |5 perc |1 óra |8 óra  |
| Bejelentkezések fertőzött eszközökről |2 óra |4 óra |8 óra  |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |2 óra |4 óra |8 óra  |



## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, további információkat az Azure-portálon a Tevékenységjelentések, lásd:

- [Bejelentkezési tevékenység jelentések az Azure Active Directory portálon](active-directory-reporting-activity-sign-ins.md)
- [Naplózási Tevékenységjelentések az Azure Active Directory portálon](active-directory-reporting-activity-audit-logs.md)

Ha azt szeretné, további információkat a biztonsági jelentések az Azure portálon, lásd:

- [Kockázati biztonsági jelentést az Azure Active Directory portálon a felhasználók](active-directory-reporting-security-user-at-risk.md)
- [Az Azure Active Directory portálon kockázatos bejelentkezések jelentés](active-directory-reporting-security-risky-sign-ins.md)

Ha azt szeretné, további információkat a kockázati eseményekről, lásd: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md).
