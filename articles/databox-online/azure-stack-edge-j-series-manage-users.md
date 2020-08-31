---
title: Azure Stack Edge GPU felhasználók kezelése | Microsoft Docs
description: Ismerteti, hogyan kezelheti a felhasználókat az Azure Stack Edge GPU-ban a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 883250bbf4bc745c1c2a0e8f442cab4d73c4479b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147167"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge"></a>A Azure Portal használata a felhasználók Azure Stack Edge-beli kezeléséhez

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk ismerteti, hogyan kezelheti a felhasználókat a Azure Stack Edge-ben. Az Azure Stack Edge a Azure Portal vagy a helyi webes felületen keresztül kezelhető. Az Azure Portalon felhasználókat vehet fel, módosíthat vagy törölhet.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználó hozzáadása
> * Felhasználó módosítása
> * Felhasználó törlése

## <a name="about-users"></a>Információk a felhasználókról

A felhasználók csak olvasási vagy teljes körű jogosultsággal rendelkezhetnek. Ahogy a név is mutatja, a csak olvasási jogosultsággal rendelkező felhasználók kizárólag megtekinteni tudják a megosztott adatokat. A teljes körű jogosultsággal rendelkező felhasználók olvasni és írni is tudják a megosztott adatokat, valamint módosíthatják és törölhetik is azokat.

 - **Teljes körű jogosultsággal rendelkező felhasználó** – Teljes körű hozzáféréssel rendelkező helyi felhasználó.
 - **Csak olvasási jogosultsággal rendelkező felhasználó** – Csak olvasási hozzáféréssel rendelkező helyi felhasználó. Ezek a felhasználók csak olvasási műveleteket megengedő megosztásokhoz vannak társítva.

A felhasználói engedélyek első alkalommal a megosztás létrehozása során, a felhasználó létrehozásakor lesznek meghatározva. A felhasználóhoz társított engedélyek ezt követően a Fájlkezelőben módosíthatók.


## <a name="add-a-user"></a>Felhasználó hozzáadása

Felhasználó hozzáadásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **áttekintés > felhasználók**lehetőséget. Válassza a **+ felhasználó hozzáadása** elemet a parancssorban.

    ![Válassza a felhasználó hozzáadása elemet.](media/azure-stack-edge-j-series-manage-users/add-user-1.png)

2. Adja meg a hozzáadni kívánt felhasználó felhasználónevét és jelszavát. Erősítse meg a jelszót, és válassza a **Hozzáadás**lehetőséget.

    ![Felhasználónév és jelszó megadása](media/azure-stack-edge-j-series-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > A következő rendszer által lefoglalt felhasználók nem használhatóak: Rendszergazda, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Vendég.  

3. Egy értesítés jelenik meg, amikor a felhasználó létrehozása megkezdődik és befejeződött. A felhasználó létrehozása után a parancssorból válassza a **frissítés** lehetőséget a felhasználók frissített listájának megtekintéséhez.


## <a name="modify-user"></a>Felhasználó módosítása

A felhasználó létrehozását követően lehetőség van a hozzá tartozó jelszó módosítására. Válasszon a felhasználók listájából. Adja meg és erősítse meg az új jelszót. Mentse a módosításokat.
 
![Felhasználó módosítása](media/azure-stack-edge-j-series-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törléséhez hajtsa végre a következő lépéseket az Azure Portalon.


1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **áttekintés > felhasználók**lehetőséget.

    ![Válassza ki a törölni kívánt felhasználót](media/azure-stack-edge-j-series-manage-users/delete-user-1.png)

2. Válasszon ki egy felhasználót a felhasználók listájából, majd válassza a **Törlés**lehetőséget. A rendszer kérésére erősítse meg a törlést.

    ![Válassza ki a törölni kívánt felhasználót](media/azure-stack-edge-j-series-manage-users/delete-user-2.png)

A törlés végrehajtása után annak megfelelően frissül a felhasználók listája.

![A felhasználók frissített listája](media/azure-stack-edge-j-series-manage-users/delete-user-4.png)

## <a name="next-steps"></a>Következő lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](azure-stack-edge-j-series-manage-bandwidth-schedules.md) talál.
