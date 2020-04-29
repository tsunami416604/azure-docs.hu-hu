---
title: Nevesített helyek konfigurálása Azure Active Directory használatával | Microsoft Docs
description: Ismerje meg, hogyan történik a nevesített helyek konfigurálása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: df45ab0a7b1729ae6c1602c9769cd5b6da26f6ac
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74014360"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Rövid útmutató: Nevesített helyek konfigurálása Azure Active Directory használatával

A nevesített helyek segítségével megcímkézhetők a szervezeten belül a megbízható IP-címtartományok. Az Azure AD a nevesített helyeket a következő célokra használja:
- Azonosíthatja a [kockázati észlelések](concept-risk-events.md)téves pozitív értékeit. A megbízható helyről történő bejelentkezésnél kisebb a felhasználó bejelentkezési kockázata.   
- Konfigurálja a [hely-alapú feltételes hozzáférést](../conditional-access/location-condition.md).

Ebben a rövid útmutatóban megismerheti, hogyan lehet nevesített helyeket konfigurálni a saját környezetében.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* Egy Azure AD-bérlő. Regisztráljon az [ingyenes próbaverzióra](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Egy felhasználó, aki a bérlő globális rendszergazdája lesz.
* Egy IP-címtartomány, amely jól ismert és hiteles a szervezeten belül. Az IP-címtartománynak a **CIDR** formátumban kell lennie.

## <a name="configure-named-locations"></a>Nevesített helyek konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali ablaktáblán válassza a **Azure Active Directory**lehetőséget, majd a **biztonsági** szakaszban válassza a **feltételes hozzáférés** lehetőséget.

    ![Feltételes hozzáférés lap](./media/quickstart-configure-named-locations/entrypoint.png)

3. A **Feltételes hozzáférési** lapon jelölje be **Nevesített helyeket** és válassza az **Új hely** lehetőséget.

    ![Névvel ellátott helyek](./media/quickstart-configure-named-locations/namedlocation.png)

6. Töltse ki az űrlapot az új oldalon. 

   * A **Név** mezőbe írja be a megnevezett hely nevét.
   * Az **IP-címtartományok** mezőbe írja be az IP-címtartományt CIDR formátumban.  
   * Kattintson a **Létrehozás**gombra.
    
     ![Az új panel](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Azure ad feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md).
- [Tartózkodási feltételek az Azure AD feltételes hozzáférésben](../conditional-access/location-condition.md)
- [Kockázatos bejelentkezések jelentés](concept-risky-sign-ins.md).  
