---
title: Mi a házirend az áttelepítés az Azure Active Directory feltételes hozzáférés? | Microsoft Docs
description: Ismerje meg, mit kell tudni az Azure Portalon klasszikus szabályzatok migrálása.
services: active-directory
keywords: feltételes hozzáférés az alkalmazásokhoz, feltételes hozzáférés az Azure AD-vel, biztonságos hozzáférés a vállalati erőforrásokhoz, feltételes hozzáférési szabályzatok
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/24/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3246a7efa36e6c6ef0dac93e659a73dac17090a
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516875"
---
# <a name="what-is-a-policy-migration-in-azure-active-directory-conditional-access"></a>Mi a házirend az áttelepítés az Azure Active Directory feltételes hozzáférés? 


[Feltételes hozzáférés](../active-directory-conditional-access-azure-portal.md) , jogosult felhasználók hozzáférésének a felhőalapú alkalmazások egy Azure Active directory (Azure AD), amelyek segítségével szabályozhatja, hogy hogyan képességét. Erre a célra nem változtak, amíg az új Azure Portalon, a kiadás jelentős mértékben továbbfejlesztette való feltételes hozzáférés működését.

Gondolja át, nem hozott létre az Azure Portalon mivel a házirendek áttelepítése:

- Most meg lehet oldani a forgatókönyvek, mielőtt nem tudta kezelni.

- A szabályzatok által összevonni őket felügyelni kell számát csökkentheti.   

- Egyetlen központi helyen a feltételes hozzáférési szabályzatokat is kezelheti.

- A klasszikus Azure portálon kivonjuk a forgalomból.   

Ez a cikk bemutatja, mit kell tudni, hogy a meglévő feltételes hozzáférési szabályzatok áttelepítése az új keretrendszert.
 
## <a name="classic-policies"></a>Klasszikus szabályzatok

Az a [az Azure portal](https://portal.azure.com), a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lap, a belépési pont, a feltételes hozzáférési szabályzatokat. Azonban a környezetben, előfordulhat, hogy akkor is ezt oldal használatával nem létrehozott feltételes hozzáférési szabályzatokat. Ezek a szabályzatok nevezzük *klasszikus szabályzatok*. Klasszikus szabályzatok feltételes hozzáférési szabályzatokat, létrehozta:

- A klasszikus Azure portálon
- Az Intune klasszikus portálján
- Az Intune App Protection portálon


Az a **feltételes hozzáférési** lapon kattintva elérheti a klasszikus szabályzatok [ **(előzetes) klasszikus szabályzatok** ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) a a **kezelés** a szakasz. 


![Azure Active Directory](./media/policy-migration/71.png)


A **klasszikus szabályzatok** nézetet nyújt lehetőséget:

- Szűrheti a klasszikus szabályzatok.
 
    ![Azure Active Directory](./media/policy-migration/72.png)

- Klasszikus szabályzatok letiltása.

    ![Azure Active Directory](./media/policy-migration/73.png)
   
- Tekintse át a beállításokat a klasszikus szabályzat (és a tiltsa le).

    ![Azure Active Directory](./media/policy-migration/74.png)


Klasszikus szabályzat le van tiltva, ha többé nem tudja visszaállítani ezt a lépést. Ezért a csoporttagságot a klasszikus szabályzat használatával módosíthatja a **részletei** megtekintése. 

![Azure Active Directory](./media/policy-migration/75.png)

Vagy módosítja a kiválasztott csoporthoz vagy adott csoportok kizárásával a szabályzat részeként elérhető felhasználók és csoportok letiltása előtt tesztelheti egy letiltott néhány tesztfelhasználók klasszikus szabályzat hatásának. 



## <a name="azure-ad-conditional-access-policies"></a>Az Azure AD feltételes hozzáférési szabályzatok

A feltételes hozzáférés az Azure Portalon a szabályzatok egyetlen központi helyen kezelheti. Feltételes hozzáférés megvalósításának jelentősen módosult, mert meg kell ismerkednie az alapvető fogalmakat a klasszikus szabályzatok áttelepítése előtt.

Lásd:

- [Mi a feltételes hozzáférés az Azure Active Directory](../active-directory-conditional-access-azure-portal.md) alapvető fogalmait és a terminológia ismerteti.

- [Ajánlott eljárások az Azure Active Directory feltételes hozzáférés](best-practices.md) útmutatásra a feltételes hozzáférés a szervezet üzembe helyezéséhez.

- [Többtényezős hitelesítés az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md) való ismerkedésre a felhasználói felület az Azure Portalon.


 
## <a name="migration-considerations"></a>Az áttelepítés szempontjai

Ez a cikk az Azure AD feltételes hozzáférési szabályzatokat is nevezzük *új szabályzatokat*.
A klasszikus szabályzatok továbbra is az új házirendek vezetik végig csak letiltja, vagy törölheti őket. 

A következő szempontokat fontosak házirend összesítés környezetében:

- Klasszikus szabályzatok egy adott felhőalkalmazás vannak társítva, míg az új házirendet kell annyi felhőalapú alkalmazások is választhat.

- Klasszikus szabályzat és a egy új szabályzatot a cloud app-ellenőrzési funkciókhoz szükséges az összes vezérlő (*és*) is teljesülnek. 


- Egy új házirendet, a következőket teheti:
 
    - Több feltétel kombinálásával, ha a forgatókönyv szerint szükséges. 

    - Válassza ki a több vonatkoznak, mint a hozzáférés szabályozására, és kombinálhatja őket a logikai biztosítanak *vagy* (a kijelölt feltételek egyikének megkövetelése) és a egy logikai *és* (az összes kijelölt vezérlő megkövetelése).

        ![Azure Active Directory](./media/policy-migration/25.png)




### <a name="office-365-exchange-online"></a>Az Office 365 Exchange online-hoz

Ha a klasszikus szabályzatok migrálása **Office 365 Exchange online** , amelyek tartalmaznak **Exchange Active Sync** ügyfél alkalmazások feltételként nem fogja tudni konszolidálhatják egy új szabályzatot. 

Ez történik, például a Ha azt szeretné, minden ügyfél típusú alkalmazások támogatásához. Egy új szabályzatot, amely rendelkezik a **Exchange Active Sync** ügyfél alkalmazások feltételként más ügyfélalkalmazások nem választhat.

![Azure Active Directory](./media/policy-migration/64.png)

A konszolidáció be egy új szabályzat akkor sem lehetséges, ha a klasszikus szabályzatok különböző feltételeket tartalmaz. Egy új szabályzatot, amely rendelkezik **Exchange Active Sync** ügyfél alkalmazásként konfigurált feltétel nem támogatja a más feltételek:   

![Azure Active Directory](./media/policy-migration/08.png)

Ha egy új szabályzatot, amely rendelkezik **Exchange Active Sync** ügyfél alkalmazásként feltétel konfigurálni, kell győződjön meg arról, hogy semmilyen más feltételt nincsenek konfigurálva. 

![Azure Active Directory](./media/policy-migration/16.png)
 

[Alkalmazás-alapú](technical-reference.md#approved-client-app-requirement) klasszikus szabályzatok az Office 365 Exchange Online-hoz, beleértve az **Exchange Active Sync** ügyfél alkalmazások feltételként engedélyezése **támogatott** és **nemtámogatott** [eszközplatformok](technical-reference.md#device-platform-condition). Bár az egyes eszközplatformok kapcsolódó új házirendet nem lehet konfigurálni, korlátozhatja a támogatási [támogatott eszközplatformok](technical-reference.md#device-platform-condition) csak. 

![Azure Active Directory](./media/policy-migration/65.png)

Több klasszikus szabályzat, amely tartalmazza a konszolidáció **Exchange Active Sync** ügyfél alkalmazások feltételként, ha rendelkezik:

- Csak **Exchange Active Sync** feltételként 

- Több követelmény a konfigurált hozzáférés biztosítása

Egy gyakori forgatókönyv esetén,:

- Eszköz-alapú klasszikus szabályzat a klasszikus Azure portálon 
- Egy alkalmazás-alapú klasszikus szabályzat az Intune app protection portálon 
 
Ebben az esetben a klasszikus szabályzatok is kialakíthattunk egy új szabályzatot, amely mindkét kiválasztott követelményekkel rendelkezik.

![Azure Active Directory](./media/policy-migration/62.png)



### <a name="device-platforms"></a>Eszközplatformok

Klasszikus szabályzatok [alkalmazásalapú vezérlők](technical-reference.md#approved-client-app-requirement) előre konfigurálva, az iOS és Android rendszerhez, mint a [eszköz platform feltétel](technical-reference.md#device-platform-condition). 

Az új házirend, ki kell választania a [eszközplatformok](technical-reference.md#device-platform-condition) külön-külön támogatni kíván.

![Azure Active Directory](./media/policy-migration/41.png)



 
 


## <a name="next-steps"></a>További lépések

- Ha azt szeretné tudni, hogyan lehet feltételes hozzáférési szabályzat konfigurálása, lásd: [többtényezős hitelesítés megkövetelése az Azure Active Directory feltételes hozzáférés az adott alkalmazások](app-based-mfa.md).

- Ha kész feltételes hozzáférési szabályzatokat konfigurálni a környezetében, tekintse át [az Azure Active Directory feltételes hozzáféréssel kapcsolatos ajánlott eljárásait](best-practices.md). 
