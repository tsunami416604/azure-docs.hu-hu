---
title: "Elnevezett helyek konfigurálása az Azure Active Directoryban |} Microsoft Docs"
description: "Megtudhatja, hogyan elnevezett helyek konfigurálása."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b7bd6f4bea111815f647af09ebaa868696b25bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Elnevezett helyek konfigurálása az Azure Active Directoryban

Az elnevezett helyek a címkézés megbízható IP-címtartományok a szervezetében. Az Azure Active Directory környezetében elnevezett helyek használja:

- A észlelését [kockázati események](active-directory-reporting-risk-events.md) jelentett vakriasztások számának csökkentése érdekében.  

- [Feltételes hozzáférés helyalapú](active-directory-conditional-access-locations.md).


Ez a cikk azt ismerteti, hogyan konfigurálhat nevű helyek a környezetben.


## <a name="entry-points"></a>Belépési pontok

Az elnevezett hely konfigurációs lapján végezheti el a **biztonsági** kattintva Azure Active Directory lap részében:

![Belépési pontok](./media/active-directory-named-locations/34.png)

- **Feltételes hozzáférés:**

    - Az a **kezelése** kattintson **helyek nevű**.
    
        ![A nevesített helyek parancs](./media/active-directory-named-locations/06.png)

- **Kockázatos bejelentkezések:**

    - A felső eszköztáron kattintson **Hozzáadás ismert IP-címtartományok**.

       ![A nevesített helyek parancs](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>Konfiguráció – példa

**Egy elnevezett hely konfigurálása:**

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali ablaktáblában **Azure Active Directory**.

    ![A bal oldali panelen az Azure Active Directory hivatkozás](./media/active-directory-named-locations/01.png)

3. Az a **Azure Active Directory** lap a **biztonsági** kattintson **feltételes hozzáférés**.

    ![A feltételes hozzáférés parancs](./media/active-directory-named-locations/05.png)


4. A a **feltételes hozzáférés** lap a **kezelése** kattintson **helyek nevű**.

    ![A nevesített helyek parancs](./media/active-directory-named-locations/06.png)


5. Az a **helyek nevű** kattintson **új helyre**.

    ![Az új hely parancs](./media/active-directory-named-locations/07.png)


6. Az a **új** lapján tegye a következőket:

    ![Az új panel](./media/active-directory-named-locations/56.png)

    a. Az a **neve** mezőbe írja be a nevesített hely nevét.

    b. Az a **IP-címtartományok** mezőbe írjon be egy IP-címtartományt. Az IP-címtartomány kell lennie a *Classless Inter-Domain Routing* (CIDR) formátumban.  

    c. Kattintson a **Create** (Létrehozás) gombra.



## <a name="next-steps"></a>További lépések

További információkért lásd:

- [Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md).

- [Azure Active Directory feltételes hozzáférési feltételek helye](active-directory-conditional-access-locations.md)

- [Az Azure Active Directory kockázati események](active-directory-reporting-risk-events.md).

- [Az Azure Active Directory portálon kockázatos bejelentkezések jelentés](active-directory-reporting-security-risky-sign-ins.md).  
