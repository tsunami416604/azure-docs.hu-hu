---
title: A avere vFXT előfeltételei – Azure
description: Az Azure-hoz készült avere-vFXT előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 7f89ea553bc7198c1faee5ba3549f88da5ec2b2c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152986"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Felkészülés az Avere vFXT létrehozására

Ez a cikk a avere vFXT-fürt létrehozásához szükséges előfeltételek feladatait ismerteti.

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

Első lépésként hozzon létre egy új Azure-előfizetést. Minden avere-vFXT-projekthez külön előfizetést használhat, amely egyszerűbbé teszi a költségek nyomon követését és tisztítását, valamint annak biztosítását, hogy a fürt munkafolyamatának kiosztása során a kvóták vagy az erőforrás-szabályozás nem érinti a többi

Új Azure-előfizetés létrehozása a Azure Portalban:

* Navigáljon az [előfizetések](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) panelre
* Kattintson a felül található **+ Hozzáadás** gombra
* Bejelentkezés, ha a rendszer kéri
* Válasszon egy ajánlatot, és végigvezeti az új előfizetés létrehozásának lépésein

## <a name="configure-subscription-owner-permissions"></a>Előfizetés tulajdonosi engedélyeinek konfigurálása

Az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező felhasználónak létre kell hoznia a vFXT-fürtöt. A fürt létrehozásához a tulajdonosnak el kell fogadnia a szoftver használati feltételeit, és engedélyeznie kell a hálózati és tárolási erőforrások módosításait.

Vannak olyan megkerülő megoldások, amelyek lehetővé teszik, hogy a nem tulajdonos hozzon létre egy avere-vFXT az Azure-fürthöz. Ezek a forgatókönyvek az erőforrások korlátozását és további szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörök hozzárendelését foglalják magukban. Az előfizetés tulajdonosának minden esetben [el kell fogadnia a avere vFXT](#accept-software-terms) .

| Alkalmazási helyzet | Korlátozások | A avere vFXT-fürt létrehozásához szükséges hozzáférési szerepkörök |
|----------|--------|-------|
| Az erőforráscsoport rendszergazdája létrehozza a vFXT | A virtuális hálózatot, a tartományvezérlőt és a fürtcsomópontok létrehozását az erőforráscsoport keretében kell létrehozni. | A [felhasználói hozzáférés rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörei mind a célként megadott erőforráscsoport hatóköre. |
| Meglévő, külső virtuális hálózat használata | A tartományvezérlő és a fürtcsomópontok a vFXT erőforráscsoport keretében jönnek létre, de egy meglévő virtuális hálózatot használnak egy másik erőforráscsoporthoz. | (1) a [felhasználói hozzáférés rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörei a vFXT erőforráscsoporthoz tartoznak; és (2) a [virtuális gép közreműködői](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), a [felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator)és a [avere közreműködői](../role-based-access-control/built-in-roles.md#avere-contributor) szerepkörök a virtuális hálózat erőforráscsoporthoz vannak korlátozva. |
| Egyéni szerepkör a fürtök létrehozói számára | Nincs erőforrás-elhelyezési korlátozás. Ez a módszer nem tulajdonosai számára jelentős jogosultságokat biztosít. | Az előfizetés tulajdonosa létrehoz egy egyéni RBAC-szerepkört a [jelen cikkben](avere-vfxt-non-owner.md)leírtak szerint. |

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta a vFXT-fürthöz

Győződjön meg arról, hogy elegendő kvóta van a következő Azure-összetevőkhöz. Ha szükséges, [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Az itt felsorolt virtuális gépek és SSD-összetevők a vFXT-fürthöz tartoznak. Ne feledje, hogy a számítási farmhoz használni kívánt virtuális gépek és SSD-k számára is szüksége lesz kvótare.
>
> Győződjön meg arról, hogy a kvóta engedélyezve van ahhoz a régióhoz, amelyen futtatni kívánja a munkafolyamatot.

|Azure-összetevő|Kvóta|
|----------|-----------|
|Virtuális gépek|3 vagy több E32s_v3 (az egyik fürtcsomópont) |
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Adatháttér-tároló (nem kötelező) |Egy új LRS blob-tároló |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Szoftverlicenc elfogadása

> [!TIP]
> Hagyja ki ezt a lépést, ha egy előfizetés tulajdonosa létrehozza a avere vFXT-fürtöt.

A fürt létrehozása során el kell fogadnia a avere vFXT szoftver használati feltételeit. Ha Ön nem előfizetés-tulajdonos, akkor az előfizetés tulajdonosának el kell fogadnia a feltételeket az idő előtt.

Ezt a lépést csak egyszer kell elvégezni az előfizetések esetében.

A szoftver feltételeinek elfogadásához előre:

1. Nyisson meg egy Cloud shellt a Azure Portalban, vagy tallózással keresse meg a <https://shell.azure.com>. Jelentkezzen be az előfizetés-azonosítójával.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. A parancs kiadása a szolgáltatási feltételek elfogadására és a programozott hozzáférés engedélyezésére a avere vFXT for Azure szoftver rendszerképéhez:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Tárolási szolgáltatási végpont létrehozása a virtuális hálózaton (ha szükséges)

A [szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózaton kívülről is megőrzik a helyi Azure Blob-forgalmat. Ajánlott minden olyan Azure-fürthöz tartozó avere-vFXT, amely Azure-blobot használ a háttérbeli adattároláshoz.

Ha új virtuális hálózatot hoz létre a fürt létrehozása során, a rendszer automatikusan létrehoz egy végpontot. Ha meglévő virtuális hálózatot ad meg, akkor rendelkeznie kell egy Microsoft Storage Service-végponttal, ha új blob Storage-tárolót szeretne létrehozni a fürt létrehozása során.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Hagyja ki ezt a lépést, ha új virtuális hálózatot hoz létre a fürt létrehozása során.
>* A végpont nem kötelező, ha a fürt létrehozása során nem hoz létre BLOB-tárolót. Ebben az esetben később is létrehozhatja a szolgáltatási végpontot, ha úgy dönt, hogy az Azure blobot használja.

Hozza létre a Storage szolgáltatás végpontját a Azure Portalból.

1. A portálon nyissa meg a virtuális hálózatok listáját.
1. Válassza ki a fürt virtuális hálózatát.
1. A bal oldali menüben kattintson a **szolgáltatás-végpontok** elemre.
1. Kattintson felül a **Hozzáadás** gombra.
1. Válassza ki a szolgáltatás ``Microsoft.Storage``.
1. Válassza ki a fürt alhálózatát.
1. A lap alján kattintson a **Hozzáadás**gombra.

   ![Azure Portal képernyőkép a szolgáltatás végpontjának létrehozási lépéseivel kapcsolatos megjegyzésekkel](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Következő lépés: a vFXT-fürt létrehozása

Az előfeltételek teljesítése után létrehozhatja a fürtöt. Útmutatásért olvassa el [a vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) című témakört.
