---
title: 'Rövid útmutató: VMware CloudSimple szolgáltatás létrehozása'
titleSuffix: Azure VMware Solution by CloudSimple
description: Ismerje meg, hogyan hozhat létre a CloudSimple szolgáltatást, vásárolhat csomópontokat és foglalhat le csomópontokat
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024432"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Rövid útmutató – Azure VMware-megoldás létrehozása a CloudSimple szolgáltatással

Első lépésként hozza létre az Azure VMware-megoldást a CloudSimple által az Azure Portalon.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware-megoldás a CloudSimple által – Szolgáltatás áttekintése

A CloudSimple szolgáltatás lehetővé teszi, hogy a CloudSimple által az Azure VMware-megoldás felhasználását használja.  A szolgáltatás létrehozása lehetővé teszi a csomópontok kiépítését, a csomópontok lefoglalását és a privát felhők létrehozását.  Hozzáadja a CloudSimple szolgáltatást minden olyan Azure-régióban, ahol a CloudSimple szolgáltatás elérhető.  A szolgáltatás határozza meg az Azure VMware-megoldás felhőalapú hálózatát.  Ez a peremhálózati hálózat olyan szolgáltatásokhoz használatos, amelyek vpn-, ExpressRoute-alapú és a privát felhőkhöz való internetkapcsolatot tartalmaznak.

A CloudSimple szolgáltatás hozzáadásához létre kell hoznia egy átjáró-alhálózatot. Az átjáró alhálózat a peremhálózati hálózat létrehozásakor használatos, és /28 CIDR blokkot igényel. Az átjáró alhálózati címterének egyedinek kell lennie. Nem lehet átfedésben a helyszíni hálózati címterek vagy az Azure virtuális hálózati címtér.

## <a name="before-you-begin"></a>Előkészületek

/28 CIDR blokk lefoglalása az átjáró alhálózatához.  Az átjáró-alhálózat szükséges a CloudSimple szolgáltatás, és egyedi a régióban, amelyben létrehozták. Az átjáró alhálózat a CloudSimple peremhálózati szolgáltatásai az Azure VMware-megoldáshoz használatosak, és /28 CIDR blokkot igényel. Az átjáró alhálózati címterének egyedinek kell lennie. Nem fedheti át a CloudSimple környezettel kommunikáló hálózatot.  A CloudSimple-lel kommunikáló hálózatok közé tartoznak a helyszíni hálózatok és az Azure virtuális hálózatok.

Tekintse át [a hálózati előfeltételeket.](cloudsimple-network-checklist.md) 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-the-service"></a>A szolgáltatás létrehozása

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatást.**

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

## <a name="provision-nodes"></a>Csomópontok kiépítése

A cloudsimple-i privát felhőkörnyezet fizetős kapacitásának beállítása, az Azure Portal első üzembe helyezhető csomópontjai.

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple csomópontokat.**

    ![Keresés CloudSimple csomópontok](media/create-cloudsimple-node-search.png)

3. Válassza a **CloudSimple csomópontok lehetőséget.**
4. Csomópontok létrehozásához kattintson a **Hozzáadás** gombra.

    ![CloudSimple-csomópontok hozzáadása](media/create-cloudsimple-node-add.png)

5. Válassza ki azt az előfizetést, amelyhez CloudSimple-csomópontokat szeretne kiépíteni.
6. Válassza ki a csomópontok erőforráscsoportját. Új erőforráscsoport hozzáadásához kattintson az **Új létrehozása gombra.**
7. Adja meg az előtagot a csomópontok azonosításához.
8. Válassza ki a csomóponti erőforrások helyét.
9. Válassza ki a csomópont-erőforrások üzemeltetéséhez szükséges dedikált helyet.
10. Válassza ki a [csomópont típusát](cloudsimple-node.md).
11. Válassza ki a kiépítve kiépítandó csomópontok számát.
12. Válassza **a Véleményezés + Létrehozás lehetőséget.**
13. Tekintse át a beállításokat. A beállítások módosításához kattintson az **Előző**gombra.
14. Kattintson a **Létrehozás** gombra.

## <a name="next-steps"></a>További lépések

* [Magánfelhő létrehozása és környezet konfigurálása](quickstart-create-private-cloud.md)
* További információ a [CloudSimple szolgáltatásról](https://docs.azure.cloudsimple.com/cloudsimple-service)
