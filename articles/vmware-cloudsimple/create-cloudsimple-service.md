---
title: Azure VMware-megoldás a CloudSimple által – CloudSimple szolgáltatás létrehozása
description: A CloudSimple szolgáltatás létrehozása az Azure Portalon
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024823"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Az Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

A CloudSimple Azure VMware-megoldásának első lépéséhez hozza létre az Azure VMware-megoldást a CloudSimple szolgáltatással az Azure Portalon.

## <a name="before-you-begin"></a>Előkészületek

/28 CIDR blokk lefoglalása az átjáró alhálózatához. Az átjáró-alhálózat szükséges a CloudSimple szolgáltatás, és egyedi a régióban, amelyben létrehozták. Az átjáró alhálózat élhálózati szolgáltatásokhoz használatos, és /28 CIDR blokkot igényel. Az átjáró alhálózati címterének egyedinek kell lennie. Nem fedheti át a CloudSimple környezettel kommunikáló hálózatot. A CloudSimple-lel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure virtuális hálózatok.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatásokat.**
    ![Keresés CloudSimple szolgáltatás](media/create-cloudsimple-service-search.png)
3. Válassza a **CloudSimple Services lehetőséget.**
4. Új szolgáltatás létrehozásához kattintson a **Hozzáadás** gombra.
    ![CloudSimple szolgáltatás hozzáadása](media/create-cloudsimple-service-add.png)
5. Válassza ki azt az előfizetést, amelyhez létre szeretné hozni a CloudSimple szolgáltatást.
6. Válassza ki a szolgáltatás erőforráscsoportját. Új erőforráscsoport hozzáadásához kattintson az **Új létrehozása gombra.**
7. A szolgáltatás azonosításához adja meg a nevet.
8. Adja meg a szolgáltatásátjáró CIDR-ét. Adjon meg egy /28 alhálózatot, amely nem fedi át a helyszíni alhálózatokat, az Azure-alhálózatokat vagy a tervezett CloudSimple alhálózatokat. A cidr a szolgáltatás létrehozása után nem módosítható.

    ![A CloudSimple szolgáltatás létrehozása](media/create-cloudsimple-service.png)
9. Kattintson az **OK** gombra.

A szolgáltatás létrejön, és hozzáadódik a szolgáltatások listájához.

## <a name="next-steps"></a>További lépések

* További információ a [csomópontok kiépítéséről](create-nodes.md)
* További információ a [magánfelhő létrehozásáról](create-private-cloud.md)
* További információ [a magánfelhő-környezet konfigurálásáról](quickstart-create-private-cloud.md)
