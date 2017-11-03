---
title: "Az Azure Active Directory helyek nevű |} Microsoft Docs"
description: "Úgy konfigurálja a helyek nevű, elkerülheti a rendelkezik a szervezete tulajdonában lévő IP-címeket létrehozni a Impossible a vakriasztások utazik és bejelentkezés szokatlan helyekről kockázat eseménytípus."
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
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Az Azure Active Directoryban elnevezett helyek

Az elnevezett helyek szolgáltatás az Azure Active Directory a címkézés megbízható IP-címtartományok a szervezetben. A környezetben, használhatja a észlelését, az adott környezetben elnevezett helyét [kockázati események](active-directory-reporting-risk-events.md). A szolgáltatás segítségével a jelentett vakriasztások számának csökkentése a *Impossible utazik bejelentkezés szokatlan helyekről* eseménytípus kockázatát. 

## <a name="configuration"></a>Konfiguráció

Egy elnevezett hely konfigurálása:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali ablaktáblában **Azure Active Directory**.

    ![A bal oldali panelen az Azure Active Directory hivatkozás](./media/active-directory-named-locations/01.png)

3. Az a **Azure Active Directory** panelen, a a **biztonsági** kattintson **feltételes hozzáférési**.

    ![A feltételes hozzáférés parancs](./media/active-directory-named-locations/05.png)


4. A a **feltételes hozzáférés** panelen, a a **kezelése** kattintson **helyek nevű**.

    ![A nevesített helyek parancs](./media/active-directory-named-locations/06.png)


5. Az a **helyek nevű** panelen kattintson a **új helyre**.

    ![Az új hely parancs](./media/active-directory-named-locations/07.png)


6. Az a **új** panelen tegye a következőket:

    ![Az új panel](./media/active-directory-named-locations/56.png)

    a. Az a **neve** mezőbe írja be a nevesített hely nevét.

    b. Az a **IP-címtartományok** mezőbe írjon be egy IP-címtartományt. Az IP-címtartomány kell lennie a *Classless Inter-Domain Routing* (CIDR) formátumban.  

    c. Kattintson a **Create** (Létrehozás) gombra.



## <a name="what-you-should-know"></a>Tudnivalók

**Tömeges frissítés**: elnevezett helyek tömeges frissíti, frissítése vagy létrehozásakor feltölteni, vagy az IP-címtartományai a CSV-fájl letöltése. Feltöltés a fájlban az IP-címtartományok hozzáadása a listához, a lista felülírja helyett.

![A feltöltés és letöltés hivatkozások](./media/active-directory-named-locations/09.png)


**Korlátozások**: legfeljebb 60 elnevezett helyek, a hozzájuk rendelt egy IP-címtartomány adhat meg. Ha csak egy elnevezett helyen konfigurálva van, akkor azt legfeljebb 500 IP-címtartományok adhat meg.


## <a name="next-steps"></a>Következő lépések

Kockázati események kapcsolatos további információkért lásd: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md).

