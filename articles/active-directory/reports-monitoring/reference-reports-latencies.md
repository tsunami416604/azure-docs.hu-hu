---
title: Azure Active Directory jelentéskészítési késések | Microsoft dokumentumok
description: Megtudhatja, hogy mennyi időbe telik, amíg az események jelentése megjelenik az Azure Portalon
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007707"
---
# <a name="azure-active-directory-reporting-latencies"></a>Az Azure Active Directory jelentéskészítés késése

A késés az az idő, amerre az Azure Active Directory (Azure AD) jelentési adatai megjelennek az [Azure Portalon.](https://portal.azure.com) Ez a cikk a különböző típusú jelentések várható késését sorolja fel. 

## <a name="activity-reports"></a>Tevékenységjelentések

A tevékenységjelentéseknek két típusa van:

- [Bejelentkezések](concept-sign-ins.md) – A felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatával kapcsolatos információk
- [Naplónaplók](concept-audit-logs.md) – Rendszertevékenység-információkat tartalmaz a felhasználókról és csoportokról, a felügyelt alkalmazásokról és a címtártevékenységekről

Az alábbi táblázat a tevékenységjelentések késési adatait sorolja fel. 

> [!NOTE]
> **A késés (95. percentilis)** arra az időre utal, amerre a naplók 95%-a, a **késés (99 százalékos)** pedig arra az időre utal, amerre a naplók 99%-a jelentésre kerül. 
>

| Jelentés | Késés (95. percentilis) |Késés (99. percentilis)|
| :-- | --- | --- |
| Naplók | 2 perc  | 5 perc  |
| Bejelentkezések | 2 perc  | 5 perc |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Milyen hamar láthatom a tevékenységek adatait a prémium licenc megszerzése után?

Ha már rendelkezik tevékenységi adatokkal az ingyenes licencével, akkor azonnal láthatja a frissítést. Ha nem rendelkezik adatokkal, akkor egy vagy két napba telik, amíg az adatok megjelennek a jelentésekben a prémium licencre való frissítés után.

## <a name="security-reports"></a>Biztonsági jelentések

A biztonsági jelentéseknek két típusa van:

- [Kockázatos bejelentkezések](concept-risky-sign-ins.md) – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- [Kockázatosként megjelölt felhasználók](concept-user-at-risk.md) – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

Az alábbi táblázat a biztonsági jelentések késési adatait sorolja fel.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Veszélyeztetett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-detections"></a>Kockázatészlelések

Az Azure AD adaptív gépi tanulási algoritmusokat és heurisztikát használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. Minden észlelt gyanús művelet et egy **kockázatészlelésnek**nevezett rekord tárol.

Az alábbi táblázat a kockázatészlelésk késési adatait sorolja fel.

| Jelentés | Minimális | Átlag | Maximum |
| :-- | --- | --- | --- |
| Bejelentkezések névtelen IP-címről |5 perc |15 perc |2 óra |
| Bejelentkezések ismeretlen helyekről |5 perc |15 perc |2 óra |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai |2 óra |4 óra |8 óra |
| Bejelentkezés szokatlan helyekről |5 perc |1 óra |8 óra  |
| Bejelentkezések fertőzött eszközökről |2 óra |4 óra |8 óra  |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |2 óra |4 óra |8 óra  |


## <a name="next-steps"></a>További lépések

* [Az Azure AD-jelentések áttekintése](overview-reports.md)
* [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
* [Az Azure Active Directory kockázatészlelései](concept-risk-events.md)
