---
title: "Az Azure Active Directory Identity Protection-értesítések |} Microsoft Docs"
description: "Ismerje meg, hogyan támogatják a különböző értesítések a vizsgálati tevékenységet."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Az Azure Active Directory Identity Protection-értesítések

Az Azure AD Identity Protection küld a kétféle típusú automatizált értesítési e-mailek felhasználói kockázat és a kockázati eseményekről kezeléséhez:

- Az észlelt kockázati e-mail felhasználói
- Heti kivonatoló e-mail

Ez a cikk mindkét értesítési e-mailek áttekintést nyújt.


## <a name="users-at-risk-detected-email"></a>Az észlelt kockázati e-mail felhasználói

Olyan veszélyben észlelt fiók válaszolva az Azure AD Identity Protection riasztást állít elő, e-mailt a **észlelt veszélyben felhasználók** tulajdonos szerint. Az e-mailek egy hivatkozást tartalmaz a  **[felhasználók meg van jelölve, a kockázat](active-directory-reporting-security-user-at-risk.md)**  jelentés. Ajánlott eljárásként a felhasználók veszélyben azonnal ki kell vizsgálni.

![Az észlelt kockázati e-mail felhasználói](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>Konfiguráció

Ha Ön rendszergazda a következők adhatók meg:

- **A kockázati szintjét az e-mailt előállítását kiváltó** -alapértelmezés szerint a kockázati szintje "Magas" kockázata.
- **Ez az e-mail címzettjei** -alapértelmezés szerint a címzettek tartalmazza az összes globális rendszergazda. Globális rendszergazdák is címzettek szerint más globális rendszergazdák, biztonsági rendszergazdák, biztonsági olvasók is felvehet.  


A kapcsolódó párbeszédpanel megnyitásához kattintson **riasztások** a a **beállítások** szakasza a **Identity Protection** lap.

![Az észlelt kockázati e-mail felhasználói](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail

A heti kivonatoló e-mail új kockázati események összegzését tartalmazza.  
Ezek a következők:

- Érintett felhasználók

- A gyanús tevékenységek

- Észlelt biztonsági rések

- A kapcsolódó jelentéseket a Identity Protection mutató hivatkozások

    ![Szervizelés](./media/active-directory-identityprotection-notifications/400.png "szervizelés")

### <a name="configuration"></a>Konfiguráció

A rendszergazdák válthat heti kivonatoló e-mailek küldésekor.

![Felhasználói kockázatok](./media/active-directory-identityprotection-notifications/62.png "felhasználói kockázatok")

A kapcsolódó párbeszédpanel megnyitásához kattintson **heti kivonatoló** a a **beállítások** szakasza a **Identity Protection** lap.

![Az észlelt kockázati e-mail felhasználói](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>Lásd még:

- [Az Azure Active Directory azonosító adatok védelmét](active-directory-identityprotection.md)
