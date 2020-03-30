---
title: Virtuálisgép-méretezési készlet konfigurálása egy meglévő Azure Load Balancer-rel - Azure portal
description: Ismerje meg, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer-rel.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349726"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Virtuálisgép-méretezési csoport konfigurálása egy meglévő Azure Load Balancer-rel az Azure Portal használatával

Ebben a cikkben megtudhatja, hogyan konfigurálhatja a virtuális gép méretezési készlet egy meglévő Azure Load Balancer. 

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Egy meglévő szabványos sku terheléselosztó az előfizetésben, ahol a virtuális gép méretezési csoport lesz telepítve.
- Egy Azure virtuális hálózat a virtuális gép méretezési készlet.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési készlet üzembe helyezése meglévő terheléselosztóval

Ebben a szakaszban egy virtuális gép méretezési készletét hoz létre az Azure Portalon egy meglévő Azure-terheléselosztóval.

> [!NOTE]
> A következő lépések feltételezik, hogy egy **myVNet** nevű virtuális hálózat és egy **Azure loadbalancer** nevű Azure loadbalancer korábban telepített.

1. A képernyő bal felső részén kattintson az Erőforrás >  > számítási**virtuálisgép-méretezési készlet** létrehozása **elemre,** vagy keressen a virtuális gép**Compute** **méretezési készletére** a piactéri keresésben.

2. Kattintson a **Létrehozás** gombra.

3. A **Virtuálisgép-méretezési csoport létrehozása csoportban**írja be vagy jelölje ki ezt az információt az **Alapok** lapon:

    | Beállítás                        | Érték                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Projekt részletei**            |                                                                                                       |
    | Előfizetés                   | Válassza ki az Azure-előfizetését                                                                        |
    | Erőforráscsoport                 | Válassza az Új létrehozása, írja be a **MyResourceGroup**lehetőséget, majd válassza az OK gombot, vagy jelöljön ki egy meglévő erőforráscsoportot. |
    | **Készlet részleteinek méretezése**          |                                                                                                       |
    | A virtuálisgép-méretezési csoport neve | Adja meg **a myVMSS-t**                                                                                      |
    | Régió                         | **Usa keleti része 2**                                                                                    |
    | Rendelkezésreállási zóna              | Nincs **kijelölése**                                                                                       |
    | **Példány részletei**           |                                                                                                       |
    | Kép                          | Válassza az **Ubuntu Server 18.04 LTS lehetőséget**                                                                    |
    | Azure Spot-példány            | Válassza a **Nem** lehetőséget                                                                                         |
    | Méret                           | Kilépés alapértelmezés szerint                                                                                      |
    | **Rendszergazdai fiók**      |                                                                                                       |
    | Hitelesítés típusa            | **Jelszó** kiválasztása                                                                                   |
    | Felhasználónév                       | Adja meg a rendszergazdai felhasználónevét        |
    | Jelszó                       | Adja meg rendszergazdai jelszavát    |
    | Jelszó megerősítése               | Írja be újra a rendszergazdai jelszót |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Hozzon létre virtuálisgép-méretezési készletet." border="true":::

4. Válassza a **Hálózat** lapot.

5. Írja be vagy jelölje ki ezt az információt a **Hálózat** lapon:

     Beállítás                           | Érték                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Virtuális hálózat konfigurációja** |                                                          |
    | Virtuális hálózat                   | Válassza ki **a myVNet** vagy a meglévő virtuális hálózat.      |
    | **Terheléselosztás**                |                                                          |
    | Terheléselosztó használata               | Válassza az **Igen** lehetőséget                                           |
    | **Terheléselosztási beállítások**       |                                                          |
    | Terheléselosztási lehetőségek            | Válassza ki **az Azure terheléselosztót**                           |
    | Terheléselosztó kiválasztása            | Válassza ki **a myLoadBalancer-t** vagy a meglévő terheléselosztót |
    | Háttérkészlet kiválasztása             | Válassza ki **a myBackendPool** vagy a meglévő háttérkészletét.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Hozzon létre virtuálisgép-méretezési készletet." border="true":::

6. Válassza a **Kezelés** lapot.

7. A **Kezelés** lapon állítsa a **Rendszerindításdia imát** **Ki**beállításra.

8. Válassza a kék **Véleményezés + létrehozás** gombot.

9. Tekintse át a beállításokat, és válassza a **Létrehozás** gombot.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy virtuális gép méretezési készlet egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési készletekről és a terheléselosztóról a következő témakörben olvashat bővebben:

- [Mi az az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
