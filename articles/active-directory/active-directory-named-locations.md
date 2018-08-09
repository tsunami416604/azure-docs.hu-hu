---
title: Nevesített helyek konfigurálása az Azure Active Directoryban |} A Microsoft Docs
description: Ismerje meg, hogyan nevesített helyek konfigurálása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.component: protection
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 62a55672a4326df585fc84699dfd72424be362dc
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627491"
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Nevesített helyek konfigurálása az Azure Active Directoryban

A nevesített helyek a címkézés megbízható IP-címtartományok a szervezetben. Az Azure Active Directory nevesített helyek összefüggésben használja:

- Észlelését [kockázati események](reports-monitoring/concept-risk-events.md) jelentett vakriasztások számának csökkentése érdekében.  

- [Helyalapú feltételes hozzáférés](conditional-access/location-condition.md).


Ez a cikk bemutatja, hogyan konfigurálhat nevű helyeken az adott környezetben.


## <a name="entry-points"></a>Belépési pontok

Az elnevezett hely csatornakonfigurációs lapján érheti a **biztonsági** szakaszában kattintson az Azure Active Directory oldalon:

![Belépési pontok](./media/active-directory-named-locations/34.png)

- **Feltételes hozzáférés:**

    - Az a **kezelés** területén kattintson **nevesített helyek**.
    
        ![A nevesített helyek parancs](./media/active-directory-named-locations/06.png)

- **Kockázatos bejelentkezések:**

    - A felső eszköztáron kattintson **Hozzáadás ismert IP-címtartományok**.

       ![A nevesített helyek parancs](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Konfiguráció – példa

**Egy elnevezett hely konfigurálása:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) globális rendszergazdaként.

2. A bal oldali ablaktáblán kattintson a **Azure Active Directory**.

    ![Az Azure Active Directory-hivatkozás a bal oldali panelen](./media/active-directory-named-locations/01.png)

3. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

    ![A feltételes hozzáférés parancs](./media/active-directory-named-locations/05.png)


4. Az a **feltételes hozzáférési** lap a **kezelés** területén kattintson **nevesített helyek**.

    ![A nevesített helyek parancs](./media/active-directory-named-locations/06.png)


5. Az a **nevesített helyek** kattintson **új helyre**.

    ![Az új hely parancs](./media/active-directory-named-locations/07.png)


6. Az a **új** lapon, tegye a következőket:

    ![Az új panel](./media/active-directory-named-locations/61.png)

    a. Az a **neve** mezőbe írja be a megnevezett hely nevét.

    b. Az a **IP-címtartományok** mezőbe írja be egy IP-címtartományt. Az IP-címtartomány kell lennie a *Classless Inter-Domain Routing* (CIDR) formátumban.  

    c. Kattintson a **Create** (Létrehozás) gombra.



## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Az Azure Active Directory feltételes hozzáférés](active-directory-conditional-access-azure-portal.md).

- [Az Azure Active Directory feltételes hozzáférés a helyszínre vonatkozó feltételek](conditional-access/location-condition.md)

- [Az Azure Active Directory kockázati eseményeivel](reports-monitoring/concept-risk-events.md).

- [Kockázatos bejelentkezések jelentés az Azure Active Directory portálon](reports-monitoring/concept-risky-sign-ins.md).  
