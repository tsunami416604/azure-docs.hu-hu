---
title: "Azure-készletben ajánlatot előfizetni |} Microsoft Docs"
description: "Azure-készletben ajánlatokat az előfizetések létrehozása"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/06/2018
ms.author: brenduns
ms.openlocfilehash: 9b35b497c264fab2b8352eda82fe6d4e1fc274e9
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure-készletben ajánlatok-előfizetések létrehozása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Miután [hozzon létre egy ajánlatot](azure-stack-create-offer.md), a felhasználóknak kell, hogy az ajánlat-előfizetéssel ahhoz, hogy használhassák azt.   
- Felhő operátorként a felügyeleti portálon találja meg a felhasználó számára előfizetést hozhat létre.  Előfizetések létrehozása a nyilvános és titkos ajánlatok lehet.
- Bérlői felhasználóként előfizetni, egy nyilvános ajánlat a felhasználói portál használatakor.  

A következő szakaszok útmutatás nyújtása a felhő üzemeltetői felhasználók és az előfizetés felhasználóként egy ajánlatot az előfizetések létrehozásakor.

## <a name="create-a-subscription-as-a-cloud-operator"></a>A felhő felelősnek előfizetés létrehozása
A felhő üzemeltetői a felügyeleti portálon hozhat létre a felhasználó ajánlatot-előfizetéssel.  A saját directory-bérlő tagjai előfizetéseket készíthet.  Ha [több-bérlős](azure-stack-enable-multitenancy.md) van engedélyezve, is létrehozhat a felhasználók számára előfizetés a további könyvtár bérlők.

A nyilvános és titkos ajánlatok előfizetéseket hozhat létre.  Ha nem szeretné, hogy a bérlők számára a saját előfizetések létrehozása teszi az összes a ajánlatok magán és majd a nevében a bérlők előfizetések létrehozása. Ez a megközelítés Azure verem integrálása külső számlázással vagy szolgáltatás katalógus rendszerek esetén közös.

Miután egy felhasználó olyan előfizetést hoz létre, hogy a felhasználó a felhasználói portál be tud jelentkezni, és megkeresi, hogy azok előfizetett ajánlatot.  

### <a name="to-create-the-subscription-for-a-user"></a>A felhasználó-előfizetés létrehozása
1.  A felügyeleti portálon Ugrás **felhasználói előfizetések.**
2.  Válassza ki **Hozzáadás** megnyitásához a **új felhasználói előfizetés** ablaktáblán. Itt adhatja meg a következő adatokat:  

  a. **Megjelenített név** – egy rövid nevet, amely akkor jelenik meg, mint az előfizetés azonosítása a *előfizetés felhasználónév*.

  b. **Felhasználói** – adja meg a felhasználó egy elérhető directory-bérlőhöz ehhez az előfizetéshez. A felhasználó neve megjelenik *tulajdonos*.  A felhasználónév formátuma a identitáskezelési megoldás függ. Példa:   

     -  **Az Azure AD:***&lt;felhasználó1 > @&lt;contoso.onmicrosoft.com >* 

     -   **Az AD FS:***&lt;felhasználó1 > @&lt;azurestack.local >*      

  c.    **Directory-bérlő** – válassza ki a directory-bérlőt, ahol a felhasználói fiók tartozik. Ha nem engedélyezte a több-bérlős, csak a helyi címtárban bérlő érhető el.

3.  Válassza ki **kínálnak** megnyitásához a **kínál** ablaktáblán, majd egy **kínálnak** ehhez az előfizetéshez. A felhasználó előfizetést hoz létre, mert olyan magán- vagy nyilvános ajánlat választhatja ki.

4.  Válassza ki **létrehozása** előfizetést. A **felhasználói előfizetések** ablaktábla most már az új előfizetés jeleníti meg.  Később amikor a felhasználó jelentkezik be a felhasználói portálra, akkor megtekintheti az előfizetés adatait.

### <a name="to-make-an-add-on-plan-available"></a>Egy bővítmény terv elérhetővé  
A felhő üzemeltetője egy korábban létrehozott előfizetéshez bármikor hozzáadhat bővítmény tervének:   
1.  A felügyeleti portálon Ugrás **több szolgáltatások** > **felhasználói előfizetések**, majd válassza ki a módosítani kívánt előfizetést.   

2.  Válassza ki **bővítmények** > **Hozzáadás** megnyitásához a **Hozzáadás terv** ablaktáblán.  

3.  Válassza ki a tervet, bővítményként hozzáadásához.  A konzol majd megjeleníti az előfizetéshez tartozó tervek.




## <a name="create-a-subscription-as-a-user"></a>Hozzon létre egy előfizetést egy olyan felhasználó nevében
A felhasználó bejelentkezhet a felhasználói portálra, keresse meg és a directory-bérlőhöz (szervezet) nyilvános ajánlatok és bővítmény tervek előfizetni. Ha az Azure-verem környezet támogatja a [több-bérlős](azure-stack-enable-multitenancy.md) előfizethet ajánlatok egy távoli directory-bérlőhöz.

### <a name="to-subscribe-to-an-offer"></a>Az ajánlat előfizetni
1. [Jelentkezzen be a](azure-stack-connect-azure-stack.md) a Azure verem felhasználói portál (https://portal.local.azurestack.external), majd kattintson **egy előfizetés**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. A a **megjelenített név** mezőben, írja be az előfizetés nevét, kattintson a **kínálnak**, kattintson az offers egyikére a **válasszon egy ajánlatot** ablaktáblán, és kattintson  **Hozzon létre**.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. A létrehozott előfizetés megtekintéséhez kattintson **további szolgáltatások**, kattintson a **előfizetések**, majd kattintson az új előfizetés.  

Miután előfizetett egy ajánlatot, frissítse a portálhoz, és milyen szolgáltatásokat az új előfizetés részét képezik.

### <a name="to-subscribe-to-an-add-on-plan"></a>Egy bővítmény terv előfizetni
Ha egy ajánlatot egy bővítmény csomagban, a terv az előfizetéshez bármikor is hozzáadhat.  

1. Válassza ki a felhasználói portál **további szolgáltatások** > **előfizetések**, majd válassza ki az előfizetést, amelyet az módosítása.

2. Válassza ki **hozzáadása terv** gombra, és válassza ki a kívánt bővítmény tervet. Ha **Hozzáadás terv** van nem érhető el, majd nem tervezik bővítmény a szolgáltatásokat az adott előfizetéshez tartozó.



## <a name="next-steps"></a>További lépések
[Virtuális gép kiépítése](azure-stack-provision-vm.md)
