---
title: Előfizetés egy ajánlatra az Azure Stackben |} A Microsoft Docs
description: Előfizetések-ajánlat létrehozása az Azure Stackben
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: sethm
ms.openlocfilehash: b6739a194f6374cf90e6508f4a4316892daaf3dd
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079242"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Ajánlatok, előfizetések létrehozása az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Miután [hozzon létre egy ajánlatot](azure-stack-create-offer.md), felhasználók, hogy az ajánlat előfizetéssel kell rendelkeznie, hogy használhassák azt. Két módon, hogy felhasználók is lekérése az előfizetett egy ajánlatra:

- Felhő-felelősként létrehozhat egy előfizetést a felügyeleti portálon a felhasználók. Előfizetések létrehozása a nyilvános és privát ajánlatok is lehet.
- Bérlő felhasználójaként feliratkozhat egy nyilvános ajánlat a felhasználói portál használata esetén.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Hozzon létre egy előfizetésben, a felhő üzemeltetője

Felhő üzemeltetői a felügyeleti portál használatával hozzon létre egy előfizetés egy ajánlatra, a felhasználó számára.  Létrehozhat saját directory-bérlő tagjai számára az előfizetések.  Amikor [több-bérlős](azure-stack-enable-multitenancy.md) van engedélyezve, is létrehozhat a felhasználók számára előfizetés további címtár bérlők.

Ha nem szeretné a bérlők számára saját előfizetések létrehozása, az ajánlatok megjelölés privátként, és hozzon létre előfizetéseket a bérlők számára. Ez a megközelítés akkor gyakori, ha külső számlázási vagy szolgáltatás katalogizáló rendszerek integrálása az Azure Stack.

Miután egy felhasználó egy előfizetést hoz létre, jelentkezzen be a felhasználói portálra, és tekintse meg az Ön előfizetni az ajánlatot.  

### <a name="to-create-a-subscription-for-a-user"></a>Egy felhasználó-előfizetés létrehozása

1. A felügyeleti portálon, lépjen a **felhasználói előfizetések.**
2. Válassza a **Hozzáadás** lehetőséget. A **új felhasználói előfizetés**, adja meg a következőket:  

   - **Megjelenített név** – azonosításához az előfizetést, így jelenik meg egy rövid nevet a *felhasználói előfizetés neve*.
   - **Felhasználói** – adja meg a felhasználó az előfizetés egy elérhető directory-bérlőhöz. A felhasználó neve jelenik meg *tulajdonosa*.  A felhasználónév formátuma attól függ, hogy az identitáskezelési megoldás. Példa:

     - **Azure ad-ben:** `<user1>@<contoso.onmicrosoft.com>`

     - **AZ AD FS:** `<user1>@<azurestack.local>` 

   - **Címtárbérlő** – válassza ki a directory-bérlővel, ahol a felhasználói fiók tartozik. Ha még nem engedélyezte a több-bérlős, csak a helyi címtárban bérlő érhető el.

3. Válassza ki **ajánlat**. A **kínál**, válasszon egy **ajánlat** ehhez az előfizetéshez. Mert az előfizetés egy felhasználó próbál létrehozni, válassza ki a **privát** , a kisegítő lehetőségek állapota.

4. Válassza ki **létrehozás** létrehozni az előfizetést. Látni fogja az új előfizetés **felhasználói előfizetés**. Ha a felhasználó jelentkezik be a felhasználói portál megjelenik az előfizetés részleteit.

### <a name="to-make-an-add-on-plan-available"></a>Így elérhetővé téve egy kiegészítő csomag

A felhő üzemeltetője egy kiegészítő csomag adhat hozzá egy korábban létrehozott előfizetést bármikor:

1. A felügyeleti portálon, válassza ki a **minden szolgáltatás** , majd a a **felügyeleti erőforrások** kategória, jelölje be **felhasználói előfizetések**. Válassza ki a módosítani kívánt előfizetést.

2. Válassza ki **bővítmények** majd **+ Hozzáadás**.  

3. A **Hozzáadás terv**, válassza ki a kívánt tervet bővítményként.

## <a name="create-a-subscription-as-a-user"></a>Felhasználói előfizetés létrehozása

A felhasználó bejelentkezhet a felhasználói portálra, keresse meg, és iratkozzon fel a nyilvános ajánlatokat és kiegészítő csomagok a directory-bérlő (szervezet).

>[!NOTE]
>Ha az Azure Stack-környezet támogatja a [több-bérlős](azure-stack-enable-multitenancy.md) is fizethet ajánlatok egy távoli directory-bérlőhöz.

### <a name="to-subscribe-to-an-offer"></a>Az előfizetés egy ajánlatra

1. [Jelentkezzen be a](azure-stack-connect-azure-stack.md) az Azure Stack felhasználói portálra (https://portal.local.azurestack.external) válassza **előfizetés beszerzése**.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. A **előfizetés beszerzése**, adja meg az előfizetés rövid nevét **megjelenítendő név**. Válassza ki **ajánlat** és **válasszon ajánlatot**, válasszon ki egy ajánlatot. Válassza ki **létrehozás** létrehozni az előfizetést.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Miután előfizetett egy ajánlatra, frissítse a portált tekintse meg, hogy mely szolgáltatások tartoznak az új előfizetés.
4. A létrehozott előfizetés megtekintéséhez válasszon **minden szolgáltatás** , majd a a **általános** kategória kiválasztása **előfizetések**. Válassza ki az előfizetést, az előfizetés részleteinek megtekintéséhez.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Előfizetés a riasztásoktól egy kiegészítő csomag

Ha egy ajánlatot egy kiegészítő csomag, az előfizetés bármikor is hozzáadhat a csomagot.  

1. Válassza ki a felhasználói portálon **minden szolgáltatás**. A következő a **általános** kategória kiválasztása **előfizetések**, és válassza ki az előfizetését, hogy szeretné-e módosítás. Ha bármilyen kiegészítő csomagok is rendelkezésre áll, **+ Hozzáadás terv** aktív, ahol egy csempe az **kiegészítő csomagok**.

   >[!NOTE]
   >Ha **+ Hozzáadás terv** nem aktív, akkor nincs bármely az adott előfizetéshez társított ajánlat kiegészítő csomagjai.

1. Válassza ki **+ Hozzáadás terv** vagy a **kiegészítő csomagok** csempére. A **kiegészítő csomagok**, válassza ki a hozzáadni kívánt csomagot.

## <a name="next-steps"></a>További lépések

[Virtuális gép kiépítése](azure-stack-provision-vm.md)
