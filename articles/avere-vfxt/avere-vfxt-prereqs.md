---
title: Avere vFXT Előfeltételek – Azure
description: Az Azure-ban Avere vFXT előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 04af92f21cecaa832e857a7017b67f815f6ab685
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417972"
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

Az előfizetés tulajdonosi engedélyekkel rendelkező felhasználók a vFXT fürtöt hozzon létre. Előfizetés tulajdonosi engedélyekkel van szükség az ezeket a műveleteket, többek között:

* Fogadja el a feltételeket a Avere vFXT szoftver
* A fürt csomópont hozzáférés szerepkör létrehozása 

Ha nem szeretné tulajdonosi hozzáférés biztosítása a felhasználók számára a vFXT létrehozása, van két lehetséges megoldások:

* Egy erőforráscsoport-tulajdonosnak hozhat létre egy fürtöt, ha ezek a feltételek teljesülnek:

  * Egy előfizetés tulajdonosának kell [fogadja el a Avere vFXT szoftverlicenc](#accept-software-terms) és [hozza létre a fürt csomópont hozzáférés szerepkört](#create-the-cluster-node-access-role). 
  * Minden Avere vFXT erőforrásokat kell üzembe helyezni az erőforráscsoportban, többek között:
    * Fürt-vezérlő
    * Fürtcsomópontok
    * Blob Storage
    * A hálózati elemek
 
* Nincsenek tulajdonosi jogosultságokkal rendelkező felhasználók vFXT fürtök hozhatók szerepköralapú hozzáférés-vezérlés (RBAC) előre a jogosultságok hozzárendelése a felhasználóhoz. Ez a módszer jelentős engedélyt ad a felhasználóknak. [Ez a cikk](avere-vfxt-non-owner.md) nem tulajdonosok-fürtök létrehozásának engedélyezése egy hozzáférési szerepkör létrehozásának módját ismerteti.

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

## <a name="create-access-roles"></a>Access-szerepkörök létrehozása 

[Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/index.yml) (RBAC) révén a vFXT fürt vezérlő és a fürt csomópontjai engedély szükséges feladatok végrehajtásához.

* A fürt vezérlő létrehozásához és módosításához a virtuális gépek a fürt létrehozásához engedélyre van szüksége. 

* Az egyes vFXT elvégzésére van szükség, olvassa el az Azure erőforrás-tulajdonságok, tárhely kezelése és más csomópontok hálózatiadapter-beállítások felügyelete normál fürt művelet részeként.

A Avere vFXT fürt létrehozása előtt meg kell határoznia egy egyéni szerepkört a fürt csomópontjai használata. 

A fürt vezérlő elfogadhatja az alapértelmezett szerepkör a sablonból. Az alapértelmezett biztosítja a fürt vezérlő erőforrás tulajdonosi jogosultságokkal rendelkeznek. Ha inkább hozzon létre egy egyéni szerepkört, a vezérlő, [testre szabott hozzáférés szerepkört](avere-vfxt-controller-role.md).

> [!NOTE] 
> Csak egy előfizetés tulajdonosa vagy a tulajdonos vagy a felhasználói hozzáférés adminisztrátora szerepkörrel rendelkező felhasználók szerepköröket hozhat létre. A szerepkörök időben hozhatók létre.  

### <a name="create-the-cluster-node-access-role"></a>A fürt csomópont hozzáférés szerepkör létrehozása

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Az Azure-fürtön a Avere vFXT létrehozása előtt létre kell hoznia a fürtszerepkör csomópont.

> [!TIP] 
> A Microsoft belső felhasználók "Avere fürt-futtatókörnyezet operátor" nevű kísérlet helyett a meglévő szerepkör segítségével hozzon létre egyet. 

1. Másolja ezt a fájlt. Adja hozzá az előfizetés-Azonosítóját az AssignableScopes sorban.

   (Ez a fájl jelenlegi verziója, a github.com/Azure/Avere tárházban tárolt [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. Mentse a fájlt az ``avere-operator.json`` vagy egy hasonló könnyen megjegyezhető nevét. 


1. Nyisson meg egy Azure Cloud shellt, és jelentkezzen be az előfizetés-azonosító (leírt [a jelen dokumentum korábbi](#accept-software-terms)). Ez a parancs használatával hozza létre a szerepkört:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

A szerepkör neve szolgál a fürt létrehozásakor. Ebben a példában a neve a következő ``avere-operator``.

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