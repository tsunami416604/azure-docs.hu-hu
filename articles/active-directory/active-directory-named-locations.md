---
title: "Az Azure Active Directory helyek nevű |} Microsoft Docs"
description: "Ismerje meg, mi nevű helyek vannak és konfigurálásuk módját."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 126646d7460831f0235221595b8a93c88be6146d
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Az Azure Active Directoryban elnevezett helyek

Az elnevezett helyek a címkézés megbízható IP-címtartományok a szervezetében. Az Azure Active Directory környezetében elnevezett helyek használja:

- A észlelését [kockázati események](active-directory-reporting-risk-events.md) jelentett vakriasztások számának csökkentése érdekében.  

- [Feltételes hozzáférés helyalapú](active-directory-conditional-access-azure-portal.md#locations).


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



## <a name="what-you-should-know"></a>Tudnivalók

**Tömeges frissítés**: elnevezett helyek tömeges frissíti, frissítése vagy létrehozásakor feltölteni, vagy az IP-címtartományai a CSV-fájl letöltése. Feltöltés a fájlban az IP-címtartományok hozzáadása a listához, a lista felülírja helyett.

![A feltöltés és letöltés hivatkozások](./media/active-directory-named-locations/09.png)


**Korlátozások**: legfeljebb 60 elnevezett helyek, a hozzájuk rendelt egy IP-címtartomány adhat meg. Ha csak egy elnevezett helyen konfigurálva van, akkor azt legfeljebb 500 IP-címtartományok adhat meg.


## <a name="next-steps"></a>Következő lépések

További részletek:

- **Kockázati események**, lásd: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md).

- **Feltételes hozzáférés**, lásd: [feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md).

- **Kockázatos bejelentkezések jelentések**, lásd: [kockázatos bejelentkezések a jelentés az Azure Active Directory portálon](active-directory-reporting-security-risky-sign-ins.md).  
