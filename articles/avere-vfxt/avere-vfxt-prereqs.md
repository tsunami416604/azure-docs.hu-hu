---
title: A avere vFXT előfeltételei – Azure
description: Az Azure-hoz készült avere-vFXT előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 27b0fd1e6a9238e978518e06ab7f0aeeae5501d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75414391"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Felkészülés az Avere vFXT létrehozására

Ez a cikk a avere vFXT-fürt létrehozásához szükséges előfeltételeket ismerteti.

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

Első lépésként hozzon létre egy új Azure-előfizetést. Minden avere-vFXT-projekthez külön előfizetést használhat, így egyszerűen nyomon követheti az összes projekt erőforrását és kiadását, más projekteket is megadhat a lehetséges erőforrás-szabályozásból a kiépítés során, és leegyszerűsítheti a tisztítást.

Új Azure-előfizetés létrehozása a Azure Portalban:

* Navigáljon az [előfizetések](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) panelre
* Kattintson a felül található **+ Hozzáadás** gombra
* Bejelentkezés, ha a rendszer kéri
* Válasszon egy ajánlatot, és végigvezeti az új előfizetés létrehozásának lépésein

## <a name="configure-subscription-owner-permissions"></a>Előfizetés tulajdonosi engedélyeinek konfigurálása

Az előfizetéshez tartozó tulajdonosi engedélyekkel rendelkező felhasználónak létre kell hoznia a vFXT-fürtöt. Az előfizetés tulajdonosának engedélyekre van szükség a szoftver használati feltételeinek elfogadásához és egyéb műveletek végrehajtásához.

Vannak olyan megkerülő megoldási forgatókönyvek, amelyek lehetővé teszik, hogy a nem tulajdonos avere vFTX hozzon létre az Azure-fürthöz. Ezek a forgatókönyvek az erőforrások korlátozásával és a létrehozóhoz való további szerepkörök hozzárendelésével járnak. Mindkét esetben az előfizetéshez tartozó tulajdonosnak is [el kell fogadnia a avere vFXT](#accept-software-terms) .

| Alkalmazási helyzet | Korlátozások | A avere vFXT-fürt létrehozásához szükséges hozzáférési szerepkörök |
|----------|--------|-------|
| Erőforráscsoport rendszergazdája | A virtuális hálózatot, a tartományvezérlőt és a fürtcsomópontok létrehozását az erőforráscsoport keretében kell létrehozni | A [felhasználói hozzáférés rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörei, mindkettő a célként megadott erőforráscsoport hatóköre |
| Külső virtuális hálózat | A tartományvezérlő és a fürtcsomópontok az erőforráscsoporthoz jönnek létre, de egy másik erőforráscsoport meglévő virtuális hálózatát használja a rendszer. | (1) a [felhasználói hozzáférés rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörei a vFXT erőforráscsoporthoz tartoznak; és (2) a [virtuális gép közreműködői](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), a [felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator)és a [avere közreműködői](../role-based-access-control/built-in-roles.md#avere-contributor) szerepkörök a virtuális hálózat erőforráscsoporthoz vannak korlátozva. |

Alternatív megoldásként hozzon létre egy egyéni szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört az idő előtt, és rendelje hozzá a jogosultságokat a felhasználóhoz a [jelen cikkben](avere-vfxt-non-owner.md)leírtak szerint. Ez a metódus jelentős engedélyeket biztosít ezekhez a felhasználókhoz.

## <a name="quota-for-the-vfxt-cluster"></a>Kvóta a vFXT-fürthöz

A következő Azure-összetevőkhöz elegendő kvótával kell rendelkeznie. Ha szükséges, [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Az itt felsorolt virtuális gépek és SSD-összetevők a vFXT-fürthöz tartoznak. A számítási farmhoz használni kívánt virtuális gépek és SSD-k esetében további kvótára lesz szüksége.  Győződjön meg arról, hogy a kvóta engedélyezve van ahhoz a régióhoz, amelyen futtatni kívánja a munkafolyamatot.

|Azure-összetevő|Kvóta|
|----------|-----------|
|Virtuális gépek|3 vagy több E32s_v3|
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Háttérbeli adattárolás (nem kötelező) |Egy új LRS blob-tároló |

## <a name="accept-software-terms"></a>Szoftverlicenc elfogadása

> [!NOTE]
> Erre a lépésre nincs szükség, ha az előfizetés tulajdonosa létrehozza a avere vFXT-fürtöt.

A fürt létrehozása során el kell fogadnia a avere vFXT szoftver használati feltételeit. Ha Ön nem előfizetés-tulajdonos, akkor az előfizetés tulajdonosának el kell fogadnia a feltételeket az idő előtt. Ezt a lépést csak egyszer kell elvégezni az előfizetések esetében.

A szoftver feltételeinek elfogadásához előre:

1. Nyisson meg egy Cloud shellt a Azure Portalban, vagy tallózással keresse meg a <https://shell.azure.com>. Jelentkezzen be az előfizetés-azonosítójával.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. A parancs kiadása a szolgáltatási feltételek elfogadására és a programozott hozzáférés engedélyezésére a avere vFXT for Azure szoftver rendszerképéhez:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Tárolási szolgáltatási végpont létrehozása a virtuális hálózaton (ha szükséges)

A [szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózaton kívülről is megőrzik a helyi Azure Blob-forgalmat. Ajánlott minden olyan Azure-fürthöz tartozó avere-vFXT, amely Azure-blobot használ a háttérbeli adattároláshoz.

Ha meglévő virtuális hálózatot biztosít, és egy új Azure BLOB-tárolót hoz létre a háttérbeli tárolóhoz a fürt létrehozása során, akkor a Microsoft Storage hálózatában szolgáltatási végponttal kell rendelkeznie. A végpontnak léteznie kell a fürt létrehozása előtt, vagy a létrehozás sikertelen lesz.

A Storage szolgáltatás végpontja ajánlott az Azure-beli blob Storage-t használó Azure-fürtök bármely avere-vFXT, még akkor is, ha később hozzáadja a tárolót.

> [!TIP]
>
>* Hagyja ki ezt a lépést, ha új virtuális hálózatot hoz létre a fürt létrehozása során.
>* Ez a lépés nem kötelező, ha a fürt létrehozása során nem hoz létre BLOB-tárolót. Ebben az esetben később is létrehozhatja a szolgáltatási végpontot, ha úgy dönt, hogy az Azure blobot használja.

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

Az előfeltételek teljesítése után saját maga is létrehozhatja a fürtöt. Útmutatásért olvassa el [a vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) című témakört.
