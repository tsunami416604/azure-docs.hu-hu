---
title: Azure VMware-megoldások (AVS) privát felhő törlése
description: Útmutató a saját AVS-felhő törléséhez.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024755"
---
# <a name="delete-an-avs-private-cloud"></a>AVS Private Cloud törlése

Az AVS rugalmasságot biztosít az AVS privát felhő törléséhez. Az AVS Private Cloud egy vagy több vSphere-fürtből áll. Minden fürthöz 3 – 16 csomópont tartozhat. Ha töröl egy AVS Private-felhőt, az összes fürt törölve lesz.

## <a name="before-you-begin"></a>Előzetes teendők

Az AVS Private Cloud törlése törli a teljes AVS privát felhőt. Az AVS Private Cloud összes összetevője törölve lesz. Ha meg szeretné őrizni az összes adatmennyiséget, győződjön meg róla, hogy biztonsági másolatot készített a helyszíni tárolóba vagy az Azure Storage szolgáltatásba.

Az AVS Private Cloud összetevői a következők:

* AVS-csomópontok
* Virtuális gépek
* VLAN-ok/alhálózatok
* Az AVS Private Cloud-on tárolt összes felhasználói érték
* Az összes tűzfalszabály melléklete egy VLAN/alhálózat számára

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="delete-an-avs-private-cloud"></a>AVS Private Cloud törlése

1. [Hozzáférés az AVS-portálhoz](access-cloudsimple-portal.md).

2. Nyissa meg az **erőforrások** lapot.

3. Kattintson a törölni kívánt AVS Private-felhőre

4. Az összefoglalás lapon kattintson a **Törlés**gombra.

    ![Az AVS Private Cloud törlése](media/delete-private-cloud.png)

5. A jóváhagyás lapon adja meg az AVS privát felhő nevét, és kattintson a **Törlés**gombra. 

    ![Az AVS Private Cloud törlése – megerősítés](media/delete-private-cloud-confirm.png)

Az AVS Private Cloud törlésre van megjelölve. A törlési folyamat három óra elteltével indul el, és törli a saját AVS-felhőt.

> [!CAUTION]
> Az AVS Private Cloud törlését követően törölni kell a csomópontokat. A csomópontok mérése folytatódni fog, amíg a csomópontok törlődnek az előfizetésből.

## <a name="next-steps"></a>Következő lépések

* [Csomópontok törlése](delete-nodes.md)
