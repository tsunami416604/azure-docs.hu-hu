---
title: Nevesített helyek konfigurálása Azure Active Directory használatával | Microsoft Docs
description: Ismerje meg, hogyan történik a nevesített helyek konfigurálása.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d99f077449529fb4a4a7f124fe1c0263d4113dee
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363445"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Rövid útmutató: Nevesített helyek konfigurálása Azure Active Directory használatával

A nevesített helyek segítségével megcímkézhetők a szervezeten belül a megbízható IP-címtartományok. Az Azure AD a nevesített helyeket a következő célokra használja:
- Észlelheti a vakriasztású [kockázati eseményeket](concept-risk-events.md). A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata.   
- [Helyalapú feltételes hozzáférés](../conditional-access/location-condition.md) konfigurálása.

Ebben a rövid útmutatóban megismerheti, hogyan lehet nevesített helyeket konfigurálni a saját környezetében.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Egy Azure AD-bérlő. Regisztráljon [ingyenes próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Egy felhasználó, aki a bérlő globális rendszergazdája lesz.
* Egy IP-címtartomány, amely jól ismert és hiteles a szervezeten belül. Az IP-címtartománynak a **CIDR** formátumban kell lennie.

## <a name="configure-named-locations"></a>Nevesített helyek konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali panelen válassza ki az **Azure Active Directory**-t, majd a **Feltételes hozzáférést** a **Biztonság** szakaszban.

    ![Feltételes hozzáférés fül](./media/quickstart-configure-named-locations/entrypoint.png)

3. A **Feltételes hozzáférési** lapon jelölje be **Nevesített helyeket** és válassza az **Új hely** lehetőséget.

    ![Névvel ellátott helyek](./media/quickstart-configure-named-locations/namedlocation.png)

6. Töltse ki az űrlapot az új oldalon. 

    * A **Név** mezőbe írja be a megnevezett hely nevét.
    * Az **IP-címtartományok** mezőbe írja be az IP-címtartományt CIDR formátumban.  
    * Kattintson a **Create** (Létrehozás) gombra.
    
    ![Az új panel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Feltételes hozzáférés az Azure Active Directoryban](../active-directory-conditional-access-azure-portal.md).

- [Helyre vonatkozó feltételek az Azure AD feltételes hozzáférésnél](../conditional-access/location-condition.md)

- [Kockázatos bejelentkezésekre vonatkozó jelentések az Azure AD portálon](concept-risky-sign-ins.md).  
