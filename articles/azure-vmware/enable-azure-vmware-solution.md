---
title: Az Azure VMware-megoldás erőforrásának engedélyezése
description: Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: dc90a322b5592ca7f400a82deca65ea753711c27
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948731"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Az Azure VMware megoldás erőforrásának engedélyezése
Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.

## <a name="eligibility-criteria"></a>Megfelelőségi feltételek

* Szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](../cost-management-billing/manage/ea-portal-agreements.md) a Microsofttal.
* Egy Azure-előfizetésben Azure-fiókra lesz szüksége.


## <a name="enable-azure-vmware-solution-resource"></a>Azure VMware-megoldás erőforrásának engedélyezése
Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a csomópontok lefoglalásához. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg a kérését, és lefoglalja a csomópontjait. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több csomópontot szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.


1. A Azure Portal a Súgó és **támogatás**területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
   - **Probléma típusa:** Technikai
   - **Előfizetés:** Előfizetés kiválasztása
   - **Szolgáltatás:** Minden szolgáltatás > Azure VMware-megoldás
   - **Erőforrás:** Általános kérdés 
   - **Összefoglalás:** Kapacitás szükséges
   - **Probléma típusa:** Kapacitás-felügyeleti problémák
   - **Probléma altípusa:** Ügyfél iránti kérelem további gazdagép-kvótához/kapacitáshoz

1. A támogatási jegy **leírásában** a **részletek** lapon adja meg a következőt:

   - POC vagy éles üzem 
   - Régiónév
   - Csomópontok száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három csomópontot javasol a saját felhő és a redundancia N + 1 csomópontok kiépítéséhez. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további csomópontokra van szüksége, vegye figyelembe, hogy öt munkanapra van szükség a csomópontok lefoglalásához. 

1. A csomópontok kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.