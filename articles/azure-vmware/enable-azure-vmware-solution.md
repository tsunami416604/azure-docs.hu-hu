---
title: Az Azure VMware-megoldás erőforrásának engedélyezése
description: Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 72a7aca97067ce4e9ed0e901e4016c82b3549eb1
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817896"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Az Azure VMware megoldás erőforrásának engedélyezése
Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.

## <a name="eligibility-criteria"></a>Megfelelőségi feltételek

* Szüksége lesz egy [Azure nagyvállalati Szerződésra (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) a Microsofttal.
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