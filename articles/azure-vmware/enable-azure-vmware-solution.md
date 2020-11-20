---
title: Az Azure VMware-megoldás erőforrásának engedélyezése
description: Megtudhatja, hogyan küldhet be támogatási kérést az Azure VMware-megoldási erőforrásának engedélyezéséhez. További gazdagépeket is igényelhet a meglévő Azure VMware-megoldás privát felhőben.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: cd58611f77c2f2fb1694597fc9ef049420093ccc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967363"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Az Azure VMware megoldás erőforrásának engedélyezése
Megtudhatja, hogyan küldhet be támogatási kérést az [Azure VMware-megoldási](introduction.md) erőforrásának engedélyezéséhez. További gazdagépeket is igényelhet a meglévő Azure VMware-megoldás privát felhőben.

## <a name="eligibility-criteria"></a>Megfelelőségi feltételek

Egy Azure-előfizetésben Azure-fiókra lesz szüksége. Az Azure-előfizetésnek meg kell felelnie az alábbi feltételek egyikének:

* Egy [Azure nagyvállalati szerződés (EA)](../cost-management-billing/manage/ea-portal-agreements.md) alá tartozó előfizetés a Microsofttal.
* Egy felhőalapú megoldás-szolgáltató (CSP) által felügyelt előfizetés egy Azure-csomag alatt.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Azure VMware-megoldás engedélyezése az EA-ügyfelek számára
Az Azure VMware-megoldási erőforrás létrehozása előtt egy támogatási jegyet kell benyújtania a gazdagépek lefoglalásához. Miután a támogatási csapat megkapja a kérést, akár öt munkanapot is igénybe vesz, hogy erősítse meg kérelmét, és foglalja le a gazdagépeket. Ha van egy meglévő Azure VMware-megoldás saját felhője, és több gazdagépet szeretne lefoglalni, akkor ugyanezt a folyamatot kell megtennie.


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
   - Gazdagépek száma
   - Bármilyen más részlet

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három gazdagép használatát javasolja a saját felhő és a redundancia N + 1 gazdagépek számára. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további gazdagépeket kér, vegye figyelembe, hogy öt munkanapon belül le kell foglalni a gazdagépeket. 

1. A gazdagépek kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Azure VMware-megoldás engedélyezése a CSP-ügyfelek számára 

A CSP-nek a [Microsoft partner centert](https://partner.microsoft.com) kell használnia az Azure VMware-megoldás az ügyfelek számára történő engedélyezéséhez. 

   >[!IMPORTANT] 
   >Az Azure VMware megoldási szolgáltatás nem biztosít több-bérlős környezetet, ezért az üzemeltetési partnerek még nem támogatottak. 

1. A **partner Centerben** válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft partner Center – ügyfelek" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Válassza ki az ügyfelet, majd válassza a **termékek hozzáadása** elemet.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partnerközpont" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Válassza az **Azure-csomag** lehetőséget, majd válassza **a Hozzáadás a kosárhoz** lehetőséget. 

1. Tekintse át és fejezze be az Azure-csomag előfizetésének általános beállítását az ügyfél számára. További információt a [Microsoft partner Center dokumentációjában](https://docs.microsoft.com/partner-center/azure-plan-manage)talál.

Miután konfigurálta az Azure-csomagot és a szükséges vSphere-RBAC engedélyeket a CSP-ként, a Microsofttal engedélyezheti az Azure-csomag előfizetésének kvótáját. Hozzáférési Azure Portal a partner Centertől a (z) (AOBO) eljárásban a **rendszergazda** használatával.

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com).

1. Válassza a **CSP** lehetőséget az **ügyfelek** területen való hozzáféréshez.

1. Bontsa ki az ügyfél adatait, és válassza a **a Microsoft Azure felügyeleti portálja** lehetőséget.

1. Azure Portal a Súgó és **támogatás** területen hozzon létre egy **[új támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support)** , és adja meg a következő információkat a jegyhez:
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
   - Gazdagépek száma
   - Bármilyen más részlet
   - Több ügyfél üzemeltetésére szolgál?

   >[!NOTE]
   >Az Azure VMware-megoldás legalább három gazdagép használatát javasolja a saját felhő és a redundancia N + 1 gazdagépek számára. 

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kérelem elküldéséhez.

   A támogatási képviselők akár öt munkanapot is igénybe vesznek, hogy erősítse a kérelmét.

   >[!IMPORTANT] 
   >Ha már rendelkezik egy meglévő Azure VMware-megoldással, és további gazdagépeket kér, vegye figyelembe, hogy öt munkanapon belül le kell foglalni a gazdagépeket. 

1. Miután hozzáadta az Azure-csomaghoz, és engedélyezte a kvótát, az ügyfél vagy a partner rendszergazdája üzembe helyezhet egy Azure VMware-megoldást a saját Azure Portal. A gazdagépek kiépítése előtt győződjön meg arról, hogy regisztrálja a **Microsoft. AVS** erőforrás-szolgáltatót a Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Az erőforrás-szolgáltató regisztrálásának további módjaiért tekintse meg az [Azure erőforrás-szolgáltatók és-típusok](../azure-resource-manager/management/resource-providers-and-types.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Miután engedélyezte az Azure VMware-megoldás erőforrását, és rendelkezik a megfelelő hálózatkezelési lehetőségekkel, [létrehozhat egy privát felhőt](tutorial-create-private-cloud.md).