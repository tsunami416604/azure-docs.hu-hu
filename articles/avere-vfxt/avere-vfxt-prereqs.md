---
title: Avere vFXT előfeltételek - Azure
description: Az Avere vFXT Azure-hoz való előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252544"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Felkészülés az Avere vFXT létrehozására

Ez a cikk az Avere vFXT-fürt létrehozásának előfeltételeit ismerteti.

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

Első ként hozzon létre egy új Azure-előfizetést. Az egyes Avere vFXT-projektekhez külön előfizetést használhat a költségkövetés és -karbantartás egyszerűsítéséhez, valamint annak biztosításához, hogy a fürt-munkafolyamat kiépítésekor ne befolyásoljanak más projekteket kvóták vagy erőforrás-szabályozás.

Új Azure-előfizetés létrehozása az Azure Portalon:

1. Keresse meg az [Előfizetések panelt](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Kattintson a **+ Hozzáadás** gombra a tetején
1. Bejelentkezés, ha a rendszer kéri
1. Válasszon ki egy ajánlatot, és kövesse végig az új előfizetés létrehozásának lépéseit

## <a name="configure-subscription-owner-permissions"></a>Előfizetés tulajdonosi engedélyeinek konfigurálása

Az előfizetéstulajdonosi engedélyekkel rendelkező felhasználónak létre kell hoznia a vFXT-fürtöt. A fürt létrehozásához a tulajdonosnak el kell fogadnia a szoftver szolgáltatási feltételeit, és engedélyeznie kell a hálózati és tárolási erőforrások módosítását.

Vannak olyan megoldások, amelyek lehetővé teszik, hogy egy nem tulajdonos hozzon létre egy Avere vFXT Azure-fürthöz. Ezek a forgatókönyvek az erőforrások korlátozását és további szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendelését foglalják magukban a létrehozóhoz. Minden ilyen esetben az előfizetés tulajdonosának idő előtt el kell [fogadnia az Avere vFXT szoftverfeltételeit.](#accept-software-terms)

| Forgatókönyv | Korlátozások | Az Avere vFXT-fürt létrehozásához szükséges hozzáférési szerepkörök |
|----------|--------|-------|
| Az erőforráscsoport rendszergazdája létrehozza a vFXT-t | A virtuális hálózatot, a fürtvezérlőt és a fürtcsomópontokat az erőforráscsoporton belül kell létrehozni. | [Felhasználói hozzáférés-rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörök, mindkettő a célerőforrás-csoport hatóköre. |
| Meglévő, külső virtuális hálózat használata | A fürtvezérlő és a fürtcsomópontok a vFXT erőforráscsoporton belül jönnek létre, de egy másik erőforráscsoportban lévő meglévő virtuális hálózatot használnak. | (1) A vFXT erőforráscsoporthatókörbe tartozó [felhasználói hozzáférés-rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörök; és (2) [A virtuális gép közreműködője](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [a Felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator)és az [Avere Közreműködő](../role-based-access-control/built-in-roles.md#avere-contributor) szerepkörök a virtuális hálózat erőforráscsoportjára vannak kitéve. |
| Egyéni szerepkör a fürtlétrehozókszámára | Nincsenek erőforrás-elhelyezési korlátozások. Ez a módszer jelentős jogosultságokat ad a nem tulajdonosoknak. | Az előfizetés tulajdonosa egyéni RBAC-szerepkört hoz létre [a cikkben](avere-vfxt-non-owner.md)leírtak szerint. |

## <a name="quota-for-the-vfxt-cluster"></a>A vFXT-fürt kvótája

Ellenőrizze, hogy rendelkezik-e elegendő kvótával a következő Azure-összetevőkhöz. Szükség esetén [kérje a kvóta növelését.](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)

> [!NOTE]
> Az itt felsorolt virtuális gépek és SSD-összetevők magához a vFXT-fürthöz tartoznak. Ne feledje, hogy a számítási farmhoz használt virtuális gépekhez és SSD-khez is szüksége van kvótára.
>
> Győződjön meg arról, hogy a kvóta engedélyezve van ahhoz a régióhoz, ahol futtatni kívánja a munkafolyamatot.

|Azure-összetevő|Kvóta|
|----------|-----------|
|Virtuális gépek|3 vagy több E32s_v3 (fürtcsomópontonként egy) |
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Adatháttér-tárolás (nem kötelező) |Egy új LRS Blob-tároló |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Szoftverhasználati feltételek elfogadása

> [!TIP]
> Hagyja ki ezt a lépést, ha egy előfizetés tulajdonosa hozza létre az Avere vFXT-fürtöt.

A fürt létrehozása során el kell fogadnia az Avere vFXT szoftver szolgáltatási feltételeit. Ha Ön nem előfizetés-tulajdonos, az előfizetés tulajdonosa előre elfogadja a feltételeket.

Ezt a lépést előfizetésenként csak egyszer kell elvégezni.

A szoftverfeltételek előzetes elfogadása:

1. Nyisson meg egy felhőrendszerhéjat az <https://shell.azure.com>Azure Portalon, vagy a t. Jelentkezzen be az előfizetés-azonosítójával.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Adja ki ezt a parancsot a szolgáltatási feltételek elfogadásához és az Avere vFXT azure-beli szoftverlemezkép programozott elérésének engedélyezéséhez:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Tárolási szolgáltatás végpontjának létrehozása a virtuális hálózatban (ha szükséges)

A [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) helyi szinten tartja az Azure Blob-forgalmat, ahelyett, hogy a virtuális hálózaton kívülre irányítana. Ajánlott minden Avere vFXT Azure-fürthöz, amely az Azure Blob háttér-adattároláshoz használja.

Ha a fürt létrehozása közben új virtuális hálózatot hoz létre, a rendszer automatikusan létrehoz egy végpontot. Ha egy meglévő virtuális hálózatot biztosít, rendelkeznie kell egy Microsoft storage-szolgáltatás végpontjával, ha új Blob-tárolót szeretne létrehozni a fürt létrehozása során.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Hagyja ki ezt a lépést, ha új virtuális hálózatot hoz létre a fürt létrehozása részeként.
>* A végpont nem kötelező, ha nem hoz létre Blob storage fürt létrehozása során. Ebben az esetben később létrehozhatja a szolgáltatásvégpontot, ha úgy dönt, hogy az Azure Blobot használja.

Hozza létre a tárolási szolgáltatás végpontját az Azure Portalon.

1. A portálon nyissa meg a virtuális hálózatok listáját.
1. Válassza ki a fürt virtuális hálózatát.
1. Kattintson a bal oldali menü **Szolgáltatás végpontjai parancsára.**
1. Kattintson felül a **Hozzáadás** gombra.
1. Válassza ki ``Microsoft.Storage``a szolgáltatást .
1. Válassza ki a fürt alhálózatát.
1. Alul kattintson a **Hozzáadás**gombra.

   ![Az Azure Portal képernyőképe a szolgáltatásvégpont létrehozásának lépéseit taggal](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>További lépések

Ezen előfeltételek befejezése után létrehozhatja a fürtöt. Olvassa [el a vFXT-fürt telepítése](avere-vfxt-deploy.md) című útmutatót.
