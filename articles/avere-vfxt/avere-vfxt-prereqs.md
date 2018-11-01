---
title: Avere vFXT Előfeltételek – Azure
description: Az Azure-ban Avere vFXT előfeltételei
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634096"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Készítse elő a Avere vFXT létrehozása

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
* Lehetővé teszi a vezérlő csomópontot erőforráscsoportok és virtuális hálózatok kezelése 
* A fürt létrehozása és módosítása a fürtcsomópontok vezérlő engedélyezése 

Ha nem szeretné tulajdonosi hozzáférés biztosítása a felhasználók számára a vFXT létrehozása, van két lehetséges megoldások:

* Egy erőforráscsoport-tulajdonosnak hozhat létre egy fürtöt, ha ezek a feltételek teljesülnek:

  * Egy előfizetés tulajdonosának kell [fogadja el a Avere vFXT szoftverlicenc](#accept-software-terms-in-advance) és [hozza létre a fürt csomópont hozzáférés szerepkört](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Minden Avere vFXT erőforrásokat kell üzembe helyezni az erőforráscsoportban, többek között:
    * Fürt-vezérlő
    * Fürtcsomópontok
    * Blob Storage
    * A hálózati elemek
 
* Nincsenek tulajdonosi jogosultságokkal rendelkező felhasználók vFXT fürtöket hozhat létre, ha egy extra hozzáférést szerepkör létrehozása és hozzárendelése a felhasználónak előre. Ez a szerepkör azonban jelentős engedélyt ad ezekre a felhasználókra. [Ez a cikk](avere-vfxt-non-owner.md) azt ismerteti, hogyan nem tulajdonosok-fürtök létrehozásának engedélyezéséhez.

## <a name="quota-for-the-vfxt-cluster"></a>A vFXT fürt kvóta

Kvóta elegendő, a következő Azure-összetevőket kell rendelkeznie. Ha szükséges, [a kvóta növelésére](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> A virtuális gépek és az itt felsorolt SSD-összetevők olyan a vFXT fürtnek. A virtuális gépek és a számítási farm használni kívánt SSD kell további kvótát.  Ellenőrizze, hogy a régió, ahol a munkafolyamatot futtatni kívánt engedélyezve van a kvótát.

|Az Azure-összetevő|Kvóta|
|----------|-----------|
|Virtual machines (Virtuális gépek)|3 vagy több D16s_v3 vagy E32s_v3|
|Prémium szintű SSD-tárolóval|200 GB-OS terület és 1 TB-os 4 TB-os gyorsítótár terület csomópontonként |
|Storage-fiók (nem kötelező) |v2|
|Háttérbeli adattárolás (nem kötelező) |Egy új LRS Blob-tároló |

## <a name="accept-software-terms-in-advance"></a>Szoftver előre a feltételek elfogadása

> [!NOTE] 
> Ebben a lépésben nincs szükség, ha az előfizetés tulajdonosa a Avere vFXT fürtöt hoz létre.

A fürt létrehozása előtt el kell fogadnia a Avere vFXT szoftver szolgáltatás használati feltételeit. Ha nem egy előfizetés tulajdonosa, lehet az elfogadására időben előfizetés tulajdonosa. Ezt a lépést csak egyszer kell megtennie előfizetésenként van szüksége.

A szoftverek előzetes feltételek elfogadásának: 

1. Nyissa meg a cloud shellt az Azure Portalon, vagy tallózással <https://shell.azure.com>. Jelentkezzen be az előfizetés-azonosítójára.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Ez a parancs, fogadja el a szolgáltatási feltételek vonatkoznak, és engedélyezze a programozott hozzáférést a Avere vFXT Azure szoftver lemezképek ki: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>Következő lépés: a vFXT fürt létrehozása

Miután befejezte az Előfeltételek, a fürt, másrészt létrehozásának is ugorhat. Olvasási [vFXT-fürt üzembe helyezése](avere-vfxt-deploy.md) útmutatást.