---
title: Az Azure Stackben kvótatípusok |} A Microsoft Docs
description: Megtekintheti és szerkesztheti a különböző kvótatípusok elérhető szolgáltatásokat és erőforrásokat az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: xiaofmao
ms.openlocfilehash: c5b3be1d5b047e77b12d22fd5d24cbc42d88f783
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715653"
---
# <a name="quota-types-in-azure-stack"></a>Az Azure Stackben kvótatípusok

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

[Kvóták](azure-stack-plan-offer-quota-overview.md#plans) erőforrásokat, amelyeket a felhasználói előfizetés is üzembe helyezése és felhasználása a vonatkozó korlátok beállításához. Például kvóták lehetővé teheti a felhasználó legfeljebb öt virtuális gépek létrehozásához. Az egyes erőforrások a saját kvótatípusokat is rendelkezhet.

## <a name="compute-quota-types"></a>COMPUTE kvótatípusok 

| **Típus** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Virtuális gépek maximális száma | 50 | Egy előfizetést hozhat létre ezen a helyen lévő virtuális gépek maximális számát. |
| Virtuálisgép-magok maximális száma | 100 | Egy előfizetést hozhat létre ezen a helyen lévő magok maximális számának (például egy a3-as virtuális gép rendelkezik négy magot). |
| A rendelkezésre állási csoportok maximális száma | 10 | Ezen a helyen létrehozott rendelkezésre állási csoportok maximális száma. |
| A virtuálisgép-méretezési csoport maximális száma | 100 | Ezen a helyen létrehozott virtuális gép méretezési csoportok maximális száma. |
| Maximális kapacitás (GB) standard szintű felügyelt lemez | 2048 | A standard szintű managed disks ezen a helyen létrehozott maximális kapacitását. |
| Maximális kapacitás (GB) prémium szintű felügyelt lemez | 2048 | A prémium szintű maximális kapacitását a felügyelt lemezek, ezen a helyen létrehozott. |

## <a name="storage-quota-types"></a>Tárolási kvóta típusa 

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális kapacitás (GB) |2048 |Ezen a helyen az előfizetés által felhasználható teljes tárolási kapacitást. |
| Tárfiókok teljes száma |20 |Egy előfizetést hozhat létre ezen a helyen storage-fiókok maximális számát. |

> [!NOTE]  
> Mielőtt a tárhelykvótát van érvényben akár két óráig is eltarthat.


## <a name="network-quota-types"></a>Hálózati kvótatípusok

| **Elem** | **Alapértelmezett érték** | **Leírás** |
| --- | --- | --- |
| Maximális nyilvános IP-címek |50 |Egy előfizetést hozhat létre ezen a helyen nyilvános IP-címek maximális számát. |
| Maximális virtuális hálózatok |50 |Ezen a helyen előfizetést hozhat létre virtuális hálózatok maximális száma. |
| Maximális virtuális hálózati átjárók |1 |Virtuális hálózati átjárók (VPN-átjárók), amely egy előfizetést hozhat létre ezen a helyen maximális számát. |
| Maximális hálózati kapcsolatok |2 |A maximális hálózati kapcsolatok száma (point-to-point vagy site-to-site), amely egy előfizetést hozhat létre ezen a helyen az összes virtuális hálózati átjáró között. |
| Maximális terheléselosztók |50 |Egy előfizetést hozhat létre ezen a helyen terheléselosztók maximális számát. |
| Maximális hálózati adapterek |100 |A hálózati adapterek számára olyan előfizetést hozhat létre ezen a helyen maximális számát. |
| Maximális hálózati biztonsági csoportok |50 |Egy előfizetést hozhat létre ezen a helyen hálózati biztonsági csoportok maximális száma. |

## <a name="view-an-existing-quota"></a>Egy meglévő kvóta megtekintése

Egy meglévő kvóta megtekintése két különböző módja van:

### <a name="plans"></a>Tervek

1.  Az adminisztrátori portál bal oldali navigációs panelén válassza **csomagok**.
2.  Válassza ki a csomagot, szeretné, részleteinek megtekintéséhez kattintson annak nevére.
3.  A megnyíló panelen válassza ki a **szolgáltatások és kvóták**.
4.  Válassza ki a kvótát, lásd: ehhez kattintson rá a szeretné a **neve** oszlop.

    [ ![Kvóták](media/azure-stack-quota-types/quotas1sm.png "kvóták megtekintése") ](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Erőforrás-szolgáltatók

1. Keresse meg az alapértelmezett irányítópult, a felügyeleti portálon, a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, hogy meg szeretné tekinteni, például a kvótához **számítási**, **hálózati**, vagy **tárolási**.
3. Válassza ki **kvóták**, majd válassza ki a megtekinteni kívánt kvótát.

## <a name="edit-a-quota"></a>Kvóta szerkesztése

Kvóta szerkesztése két különböző módja van:

### <a name="edit-a-plan"></a>A terv szerkesztése

1.  Az adminisztrátori portál bal oldali navigációs panelén válassza **csomagok**.
2.  Válassza ki a csomagot, amelynek szeretné a kvótát, szerkesztéséhez kattintson annak nevére.
3.  A megnyíló panelen válassza ki a **szolgáltatások és kvóták**.
4.  Válassza ki a szerkesztéséhez kattintson a kívánt kvótát az **neve** oszlop.
    [ ![Kvóták](media/azure-stack-quota-types/quotas1sm.png "kvóták megtekintése") ](media/azure-stack-quota-types/quotas1.png#lightbox)

5.  A megnyíló panelen válassza ki a **Szerkesztés a számítási**, **Szerkesztés a hálózati**, vagy **Storage szerkesztése**.
    ![Kvóták](media/azure-stack-quota-types/quotas3.png "kvóták megtekintése")    

Azt is megteheti akkor is az alábbi eljárás egy kvóta szerkesztése:

1. Az alapértelmezett irányítópult, a felügyeleti portál, keresse meg a **erőforrás-szolgáltatók** csempére.
2. Válassza ki a szolgáltatást, amely a módosítandó, például a kvótához **számítási**, **hálózati**, vagy **tárolási**.
3. Majd **kvóták**, majd válassza ki a módosítani kívánt kvótát.
4. Az a **beállítása tárolási kvótákat**, **kvóták beállítása számítási**, vagy **beállítása hálózati kvóták** ablaktáblán (attól függően, a típus szerkesztését választotta kvóta), az értékeket szerkesztheti, és válassza ki **Mentése**.

### <a name="edit-original-configuration"></a>Eredeti konfiguráció szerkesztése
  
Dönthet úgy, hogy az eredeti konfiguráció helyett a kvóta szerkesztése [egy kiegészítő csomag használatával](create-add-on-plan.md). Ha szerkeszti a kvótát, az új konfigurációt automatikusan globálisan érvényes a kvóta használó összes csomag és az összes meglévő előfizetéseket, amelyek ezeket a csomagokat. A kvóta szerkesztése eltér attól, hogy ha használ egy kiegészítő csomagot adjon meg egy módosított kvóta, amely egy felhasználó úgy dönt, hogy az előfizetés. 

Az új értékekkel kvóta globálisan vonatkoznak, a módosított kvóta használó kívül minden csomagra, és minden meglévő előfizetésekhez, amelyek ezeket a csomagokat. 

## <a name="next-steps"></a>További lépések

- [Ismerje meg, további információk csomagok, ajánlatok és kvóták.](azure-stack-plan-offer-quota-overview.md)
- [Kvóták létrehozása a csomag létrehozásakor.](azure-stack-create-plan.md)
