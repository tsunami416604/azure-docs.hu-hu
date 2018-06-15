---
title: Azure-készletben ajánlatot előfizetni |} Microsoft Docs
description: Azure-készletben ajánlatokat az előfizetések létrehozása
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295210"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Azure-készletben ajánlatok-előfizetések létrehozása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Miután [hozzon létre egy ajánlatot](azure-stack-create-offer.md), a felhasználóknak kell, hogy az ajánlat-előfizetéssel ahhoz, hogy használhassák azt. Két módon, hogy a felhasználók előfizetett is get az ajánlatra:

- Felhő operátorként a felügyeleti portálon találja meg a felhasználó számára előfizetést hozhat létre. Előfizetések létrehozása a nyilvános és titkos ajánlatok lehet.
- Bérlői felhasználóként előfizetni, egy nyilvános ajánlat a felhasználói portál használatakor.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>A felhő felelősnek előfizetés létrehozása

A felhő üzemeltetői a felügyeleti portálon hozhat létre a felhasználó ajánlatot-előfizetéssel.  A saját directory-bérlő tagjai előfizetéseket készíthet.  Ha [több-bérlős](azure-stack-enable-multitenancy.md) van engedélyezve, is létrehozhat a felhasználók számára előfizetés a további könyvtár bérlők.

Ha nem szeretné, hogy a bérlők a saját előfizetések létrehozása, megjelölés privátként az ajánlatokat, majd létre az előfizetések a bérlők számára. Ez a megközelítés Azure verem integrálása külső számlázással vagy szolgáltatás katalógus rendszerek esetén közös.

Miután egy felhasználó olyan előfizetést hoz létre, jelentkezzen be a felhasználói portálra, és tekintse meg a folyamatban előfizetni a ajánlat.  

### <a name="to-create-a-subscription-for-a-user"></a>Előfizetés létrehozása a felhasználó számára

1. A felügyeleti portálon Ugrás **felhasználói előfizetések.**
2. Válassza a **Hozzáadás** lehetőséget. A **új felhasználói előfizetés**, adja meg a következő adatokat:  

   - **Megjelenített név** – egy rövid nevet, amely akkor jelenik meg, mint az előfizetés azonosítása a *előfizetés felhasználónév*.
   - **Felhasználói** – adja meg a felhasználó egy elérhető directory-bérlőhöz ehhez az előfizetéshez. A felhasználó neve megjelenik *tulajdonos*.  A felhasználónév formátuma a identitáskezelési megoldás függ. Példa:

     - **Az Azure AD:***&lt;felhasználó1 > @&lt;contoso.onmicrosoft.com >* 

     - **Az AD FS:***&lt;felhasználó1 > @&lt;azurestack.local >* 

   - **Directory-bérlő** – válassza ki a directory-bérlőt, ahol a felhasználói fiók tartozik. Ha még nem engedélyezte a több-bérlős, csak a helyi címtárban bérlő érhető el.

3. Válassza ki **kínálnak**. A **kínál**, válasszon egy **kínálnak** ehhez az előfizetéshez. Az előfizetés egy felhasználó hoz létre, mert kiválasztása **titkos** kisegítő állapotként.

4. Válassza ki **létrehozása** előfizetést. Látni fogja, az új előfizetés alapján **felhasználói előfizetési**. Amikor a felhasználó a felhasználói portálra történő bejelentkezésekor láthatják az előfizetés részletei.

### <a name="to-make-an-add-on-plan-available"></a>Egy bővítmény terv elérhetővé

A felhő üzemeltetője egy korábban létrehozott előfizetéshez bármikor hozzáadhat bővítmény tervének:

1. A felügyeleti portálon, válassza ki **több szolgáltatások** > **felhasználói előfizetések**. Válassza ki a módosítani kívánt előfizetést.

2. Válassza ki **bővítmények** majd **+ Hozzáadás**.  

3. A **Hozzáadás terv**, válassza ki a kívánt tervet bővítményként.

## <a name="create-a-subscription-as-a-user"></a>Hozzon létre egy előfizetést egy olyan felhasználó nevében

A felhasználó bejelentkezhet a felhasználói portálra, keresse meg és a directory bérlője (szervezet) nyilvános ajánlatok és bővítmény tervek előfizetni.

>[!NOTE]
>Ha az Azure-verem környezet támogatja a [több-bérlős](azure-stack-enable-multitenancy.md) is előfizethet ajánlatok egy távoli directory-bérlőhöz.

### <a name="to-subscribe-to-an-offer"></a>Az ajánlat előfizetni

1. [Jelentkezzen be a](azure-stack-connect-azure-stack.md) Azure verem felhasználói portálnak (https://portal.local.azurestack.external) válassza **egy előfizetés**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. A **egy előfizetés**, adja meg az előfizetést, a rövid nevét **megjelenített név**. Válassza ki **kínálnak** és a **válasszon egy ajánlatot**, válasszon egy ajánlatot. Válassza ki **létrehozása** előfizetést.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Miután előfizetett egy ajánlatot, frissítse a portálhoz, és milyen szolgáltatásokat az új előfizetés részét képezik.
4. A létrehozott előfizetés megtekintéséhez válasszon **további szolgáltatások** , és válassza **előfizetések**. Válassza ki az előfizetést, az előfizetés részleteinek megtekintéséhez.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Egy bővítmény terv előfizetni

Ha egy ajánlatot egy bővítmény csomagban, a terv az előfizetéshez bármikor is hozzáadhat.  

1. Válassza ki a felhasználói portál **további szolgáltatások** > **előfizetések**, majd válassza ki az előfizetést, amelyet az módosítása. Ha nincsenek elérhető, a bővítmény tervek **+ Hozzáadás terv** aktív, és mozaikokra **bővítmény tervek**.

   >[!NOTE]
   >Ha **+ Hozzáadás terv** nem aktív, akkor nincs bármely bővítmény tervek az adott előfizetéshez tartozó szolgáltatásokat.

1. Válassza ki **+ Hozzáadás terv** vagy a **bővítmény tervek** csempére. A **bővítmény tervek**, válassza ki a hozzáadni kívánt tervet.

## <a name="next-steps"></a>További lépések

[Virtuális gép kiépítése](azure-stack-provision-vm.md)
