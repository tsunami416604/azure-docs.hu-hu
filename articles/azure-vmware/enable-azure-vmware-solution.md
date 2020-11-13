---
title: Az Azure VMware-megoldás erőforrásának engedélyezése
description: Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 7c805e9e622f55593ff1fbb72a355d233b7e3618
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576383"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Az Azure VMware megoldás erőforrásának engedélyezése
Megtudhatja, hogyan küldhet be támogatási kérést az [Azure VMware-megoldási](introduction.md) erőforrásának engedélyezéséhez. További csomópontokat is igényelhet a meglévő Azure VMware-megoldás privát felhőben.

## <a name="eligibility-criteria"></a>Megfelelőségi feltételek

Egy Azure-előfizetésben Azure-fiókra lesz szüksége. Az Azure-előfizetésnek meg kell felelnie az alábbi feltételek egyikének:

* Egy [Azure nagyvállalati szerződés (EA)](../cost-management-billing/manage/ea-portal-agreements.md) alá tartozó előfizetés a Microsofttal.
* Egy felhőalapú megoldás-szolgáltató (CSP) által felügyelt előfizetés egy Azure-csomag alatt.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Azure VMware-megoldás engedélyezése az EA-ügyfelek számára
Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a csomópontok lefoglalásához. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg a kérését, és lefoglalja a csomópontjait. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több csomópontot szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.


1. A Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
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

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Azure VMware-megoldás engedélyezése a CSP-ügyfelek számára 

A CSP-nek a [Microsoft partner centert](https://partner.microsoft.com) kell használnia az Azure VMware-megoldás az ügyfelek számára történő engedélyezéséhez. 

1. A **partner Centerben** válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft partner Center – ügyfelek" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Válassza ki az ügyfelet, majd válassza a **termékek hozzáadása** elemet.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partnerközpont" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Válassza az **Azure-csomag** lehetőséget, majd válassza **a Hozzáadás a kosárhoz** lehetőséget. 

1. Tekintse át és fejezze be az Azure-csomag előfizetésének általános beállítását az ügyfél számára. További információt a [Microsoft partner Center dokumentációjában](https://docs.microsoft.com/partner-center/azure-plan-manage)talál.

Miután konfigurálta az Azure-csomagot, és a szükséges RBAC-engedélyek a CSP-ként vannak konfigurálva, a Microsoft hasonló eljárással fogja felvenni a kvótát az Azure-csomag előfizetéséhez. Miután hozzáadta az Azure-csomaghoz, az ügyfél vagy a partner rendszergazdája üzembe helyezhet egy Azure VMware-megoldást a saját Azure Portalon keresztül. 

## <a name="next-steps"></a>Következő lépések

Miután engedélyezte az Azure VMware-megoldás erőforrását, és rendelkezik a megfelelő hálózatkezelési lehetőségekkel, [létrehozhat egy privát felhőt](tutorial-create-private-cloud.md).