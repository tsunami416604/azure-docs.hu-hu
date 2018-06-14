---
title: " Az Azure (erőforrás-kezelő) folyamat kiszolgáló kezeléséhez |} Microsoft Docs"
description: Ez a cikk ismerteti a feladat-visszavételi folyamatkiszolgáló (erőforrás-kezelő) beállítása az Azure-ban.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: anoopkv
ms.openlocfilehash: 1b75acb13ac4c8990f99f7454a6de5483f6ca2f1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29768395"
---
# <a name="manage-a-process-server-running-in-azure-resource-manager"></a>(Erőforrás-kezelő) Azure-ban futó folyamat kiszolgáló kezelése
> [!div class="op_single_selector"]
> * [Resource Manager](./site-recovery-vmware-setup-azure-ps-resource-manager.md)
> * [Klasszikus ](./site-recovery-vmware-setup-azure-ps-classic.md)

A feladat-visszavétel során ajánlott telepíteni a folyamatkiszolgáló az Azure-ban, ha nincs a nagy késleltetésű az Azure virtuális hálózat és a helyszíni hálózat között. Ez a cikk ismerteti, hogyan állítsa be, konfigurálása és kezelése az Azure-ban futó folyamat kiszolgálók.

> [!NOTE]
> Ez a cikk az, hogy akkor használható, ha a használt **erőforrás-kezelő** mint a feladatátvétel során a virtuális gépek telepítési modelljét. Ha követte **klasszikus** lehetőséget üzemi modellként kövesse a [hogyan beállítása & a feladat-visszavételi folyamatkiszolgáló (klasszikus) konfigurálása](./site-recovery-vmware-setup-azure-ps-classic.md)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]

## <a name="deploy-a-process-server-on-azure"></a>Az Azure-on folyamat-kiszolgáló központi telepítése
1. A tárolóban lévő > **Site Recovery-infrastruktúra** (a "Kezelése" fejléc) > **konfigurációs kiszolgálók** ("A VMware és fizikai gépek" cím) alatt válassza ki a konfigurációs kiszolgálót.
2. A konfigurációs kiszolgáló részleteit megjelenítő oldalon megnyíló kattintson "+ feldolgozni a kiszolgáló"

  ![Folyamat server gyűjtemény hozzáadása](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps.png)

3.  Az a **Hozzáadás folyamatkiszolgáló** lapon, válassza ki a következő értékek

  ![Adja hozzá a folyamatkiszolgáló katalóguseleméhez](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-1.png)
|**Mező neve**|**Érték**|
|-|-|
|Válassza ki, hol szeretné üzembe helyezni a folyamatkiszolgálót|Adja meg az értéket **az Azure-ban feladat-visszavételi folyamatkiszolgáló telepítése** |
|Előfizetés|Válassza ki az Azure-előfizetéssel, ahol sikertelen volt a virtuális gépeket|
|Erőforráscsoport|Létrehozhat egy erőforráscsoportot az a folyamat kiszolgáló telepítéséhez vagy egy meglévő erőforráscsoportot a folyamatkiszolgáló telepítése|
|Hely|Válassza ki az Azure-adatközpont, amelybe a virtuális gépek adott történő feladatátvételt|
|Azure Network-hálózat|Válassza ki az Azure virtuális Network(VNet), amely a virtuális gépek, ha a feladatátvételt. Ha több Azure Vnetekhez történő sikertelen a virtuális gépeket, majd van szüksége a folyamatkiszolgáló telepítése egy virtuális hálózat|

4. Töltse ki a többi tulajdonságai a folyamatkiszolgáló között

  ![Összegzési folyamat-kiszolgáló hozzáadása](./media/site-recovery-vmware-setup-azure-ps-arm/add-ps-page-2.png)
|**Mező neve**|**Érték**|
|-|-|
|Kiszolgáló neve|Megjelenített név és a folyamat kiszolgáló virtuális gép állomásnevét|
| Felhasználónév|A felhasználónevet, amely a virtuális gépen a rendszergazda válik|
|Tárfiók|A tárfiók, ahol kerülnek, a virtuális gép virtuális lemez neve|
|Alhálózat|Az alhálózat az Azure vnet, amelyhez a virtuális gép csatlakozik|
| IP-cím|Elindul a virtuális IP-címet, hogy szeretné-e a folyamatkiszolgáló azt feltételezni egyszer azt|
5. Kattintson az OK gombra kattintva indítsa el a folyamat kiszolgáló virtuális gép telepítését.

> [!NOTE]
> Nem fogja tudni használni a folyamatkiszolgáló feladat-visszavétel, szüksége regisztrálja őket a helyszíni konfigurációs kiszolgáló.

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>(Az Azure-ban futó) a folyamatkiszolgáló a konfigurációs kiszolgálóhoz (a helyszínen fut) regisztrálása

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

## <a name="upgrading-the-process-server-to-latest-version"></a>A folyamatkiszolgáló a legújabb verzióra való frissítést.

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-upgrade-process-server.md)]

## <a name="unregistering-the-process-server-running-in-azure-from-a-configuration-server-running-on-premises"></a>A folyamatkiszolgáló (Azure-beli) regisztrációját a konfigurációs kiszolgálóról (a helyszínen fut)

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]
