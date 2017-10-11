---
title: "Az Azure Active Directory Identity Protection-értesítések |} Microsoft Docs"
description: "Ismerje meg, hogyan támogatják a különböző értesítések a vizsgálati tevékenységet."
services: active-directory
keywords: "az Azure active directory azonosító adatok védelmét, a cloud app discovery, alkalmazások, biztonság, kockázat, kockázati szint, biztonsági rés, biztonsági házirend kezelése"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 079d16bbf75cd2b3b94269d684e1ae1a0e6aa967
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Az Azure Active Directory Identity Protection-értesítések
Az Azure AD Identity Protection küld a kétféle típusú automatizált értesítési e-mailek felhasználói kockázat és a kockázati eseményekről kezeléséhez:

* Felhasználói értesítés e-mailek biztonsága sérült
* Heti kivonatoló e-mail

## <a name="user-compromised-alert-email"></a>Felhasználói értesítés e-mailek biztonsága sérült
Egy felhasználó sérült biztonságú e-mail riasztást generál, ha az Azure AD Identity Protection egy fiókot, biztonsági sérülés. Az e-mailben a felhasználóknak meg van jelölve a kockázat a jelentést az Identity Protection-irányítópult a hivatkozást tartalmaz. Azt javasoljuk, hogy a sérült biztonságú fiókok értesítések azonnal vizsgálata.

## <a name="weekly-digest-email"></a>Heti kivonatoló e-mail
A heti kivonatoló e-mail új kockázati események összegzését tartalmazza.<br>
Ezek a következők:

* Érintett felhasználók
* A gyanús tevékenységek
* Észlelt biztonsági rések
* A kapcsolódó jelentéseket a Identity Protection mutató hivatkozások

<br>
![Szervizelés](./media/active-directory-identityprotection-notifications/400.png "szervizelés")
<br>

Válthat heti kivonatoló e-mailek küldésekor.
<br><br>
![Felhasználói kockázatok](./media/active-directory-identityprotection-notifications/62.png "felhasználói kockázatok")
<br>

**A kapcsolódó konfigurációs párbeszédpanel megnyitásához**:

1. Az a **Azure AD Identity Protection** panelen kattintson a **beállítások**.
   <br><br>
   ![Felhasználói kockázat házirendnek](./media/active-directory-identityprotection-notifications/401.png "felhasználói kockázat házirendnek")
   <br>
2. Az a **általános** kattintson **értesítések**.
   <br><br>
   ![Felhasználói kockázat házirendnek](./media/active-directory-identityprotection-notifications/405.png "felhasználói kockázat házirendnek")
   <br>

## <a name="see-also"></a>Lásd még:
* [Az Azure Active Directory azonosító adatok védelmét](active-directory-identityprotection.md)
