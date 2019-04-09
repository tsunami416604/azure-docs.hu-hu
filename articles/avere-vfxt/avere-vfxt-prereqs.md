---
title: Avere vFXT Előfeltételek – Azure
description: Az Azure-ban Avere vFXT előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275878"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Felkészülés az Avere vFXT létrehozására

Ez a cikk ismerteti az előkészületi teendők elvégzése, egy Avere vFXT fürt létrehozásához.

## <a name="create-a-new-subscription"></a>Hozzon létre egy új előfizetést

Először hozzon létre egy új Azure-előfizetést. Használjon különálló előfizetést egyes Avere vFXT projekt lehetővé teszi, hogy egyszerűen nyomon követheti a Projekt erőforrások és a költségeket, más projektek védheti a lehetséges erőforrás-szabályozási kiépítése során, valamint egyszerűbb karbantartása.  

Új Azure-előfizetés létrehozása az Azure Portalon:

* Keresse meg a [előfizetések panel](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Kattintson a **+ Hozzáadás** az gombbal
* Jelentkezzen be, ha a rendszer kéri
* Ajánlat kiválasztása, és haladjon végig a lépéseket egy új előfizetés létrehozása

## <a name="configure-subscription-owner-permissions"></a>Előfizetés tulajdonosi engedélyekkel konfigurálása

Az előfizetés tulajdonosi engedélyekkel rendelkező felhasználók a vFXT fürtöt hozzon létre. Előfizetés tulajdonosi engedélyekkel fogadja el a szoftverlicenc-szolgáltatás, és egyéb műveletekre van szükség. 

Van néhány megoldás forgatókönyvek, amelyek lehetővé teszik a nem tulajdonosi egy Avere vFTX az Azure-fürt létrehozásához. Ezek a forgatókönyvek erőforrások korlátozása és a további szerepkörök hozzárendelése a létrehozó járnak. Mindkét esetben az előfizetés tulajdonosa is kell [fogadja el a Avere vFXT szoftverlicenc](#accept-software-terms) előre. 

| Forgatókönyv | Korlátozások | A Avere vFXT fürt létrehozásához szükséges hozzáférési szerepkörök | 
|----------|--------|-------|
| Erőforrás-csoport rendszergazdája | A virtuális hálózat, a fürt vezérlő és a fürt csomópontjai az erőforráscsoporton belül kell létrehozni | [Felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkörök, mindkettő hatóköre úgy, hogy a céloldali erőforráscsoport | 
| Külső virtuális hálózat | A fürt vezérlő és a fürt csomópontjai az erőforráscsoporton belül jönnek létre, de a meglévő virtuális hálózat egy másik erőforráscsoportban található használatos | (1) [felhasználói hozzáférés rendszergazdája](../role-based-access-control/built-in-roles.md#user-access-administrator) és [közreműködői](../role-based-access-control/built-in-roles.md#contributor) hatóköre az vFXT erőforráscsoport; és (2) szerepkörök [virtuális gépek Közreműködője](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [felhasználói hozzáférés Rendszergazdai](../role-based-access-control/built-in-roles.md#user-access-administrator), és [Avere közreműködői](../role-based-access-control/built-in-roles.md#avere-contributor) szerepkörök hatókörrel rendelkező virtuális hálózatok közötti erőforrás-csoportba. |
 
Alternatív az, hogy hozzon létre egy egyéni, szerepköralapú hozzáférés-vezérlés (RBAC) szerepkört előre és jogosultságok hozzárendelése a felhasználóhoz a [Ez a cikk](avere-vfxt-non-owner.md). Ez a módszer jelentős engedélyt ad a felhasználóknak. 

## <a name="quota-for-the-vfxt-cluster"></a>A vFXT fürt kvóta

Kvóta elegendő, a következő Azure-összetevőket kell rendelkeznie. Ha szükséges, [a kvóta növelésére](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> A virtuális gépek és az itt felsorolt SSD-összetevők olyan a vFXT fürtnek. A virtuális gépek és a számítási farm használni kívánt SSD kell további kvótát.  Ellenőrizze, hogy a régió, ahol a munkafolyamatot futtatni kívánt engedélyezve van a kvótát.

|Azure-összetevő|Kvóta|
|----------|-----------|
|Virtual machines (Virtuális gépek)|3 vagy több E32s_v3|
|Prémium szintű SSD-tár|200 GB operációsrendszer-tárhely és 1–4 TB gyorsítótártér csomópontonként |
|Tárfiók (nem kötelező) |v2|
|Háttérbeli adattárolás (nem kötelező) |Egy új LRS Blob-tároló |

## <a name="accept-software-terms"></a>Szoftverfrissítési feltételek elfogadása

> [!NOTE] 
> Ebben a lépésben nincs szükség, ha az előfizetés tulajdonosa a Avere vFXT fürtöt hoz létre.

Fürt létrehozása során el kell fogadnia a Avere vFXT szoftver szolgáltatás használati feltételeit. Ha nem egy előfizetés tulajdonosa, lehet az elfogadására időben előfizetés tulajdonosa. Ezt a lépést csak egyszer kell megtennie előfizetésenként van szüksége.

A szoftverek előzetes feltételek elfogadásának: 

1. Nyissa meg a cloud shellt az Azure Portalon, vagy tallózással <https://shell.azure.com>. Jelentkezzen be az előfizetés-azonosítójára.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Adja ki ezt a parancsot fogadja el a szolgáltatási feltételek vonatkoznak, és a szoftver az Azure lemezkép Avere vFXT programozott hozzáférés engedélyezése: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Hozzon létre egy tárolási végpontot a virtuális hálózaton (ha szükséges)

A [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) helyett átirányítására a virtuális hálózaton kívül helyi tartja az Azure Blob-forgalmat. Az Azure által a háttér-adatokat tároló Azure Blob-fürt bármely Avere vFXT ajánlott. 

Ha meg van adva egy meglévő virtuális hálózatot hoz létre egy új Azure Blob-tárolót a háttér-tároláshoz a fürt létrehozása során, rendelkeznie kell egy végpontot a virtuális hálózat, a Microsoft Storage. Ez a végpont a fürt létrehozása előtt léteznie kell, vagy a létrehozás sikertelen lesz. 

A storage-szolgáltatásvégpont ajánlott bármely Avere vFXT az Azure-fürt által használt Azure-blobtárhelyre, akkor is, ha később adja hozzá a tárolót. 

> [!TIP] 
> * Hagyja ki ezt a lépést, ha a fürt létrehozása során egy új virtuális hálózatot hoz létre. 
> * Ez a lépés nem kötelező, ha nem hoz létre a Blob storage a fürt létrehozásakor. Ebben az esetben a szolgáltatásvégpont később létrehozhatja, ha úgy dönt, hogy használja az Azure Blob.

A storage-szolgáltatásvégpont létrehozása az Azure Portalról. 

1. A portálon kattintson **virtuális hálózatok** a bal oldalon.
1. Válassza ki a virtuális hálózathoz a fürt számára. 
1. Kattintson a **Szolgáltatásvégpontokat** a bal oldalon.
1. Kattintson a **Hozzáadás** tetején.
1. Hagyja meg a szolgáltatás ``Microsoft.Storage`` , és válassza ki a fürt alhálózat.
1. Kattintson a lap alján, **Hozzáadás**.

   ![Jegyzetekkel lépéseit a szolgáltatásvégpont létrehozása az Azure portal képernyőképe](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Következő lépés: A vFXT fürt létrehozása

Miután befejezte az Előfeltételek, a fürt, másrészt létrehozásának is ugorhat. Olvasási [vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) útmutatást.