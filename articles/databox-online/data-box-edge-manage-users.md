---
title: Az Azure Data Box Edge kezelheti a felhasználókat | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure Portalt a felhasználók kezelésére az Azure Data Box Edge-en.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 443ed983a0eec5dfd8f7a917fbc1440cd66c3db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946137"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Az Azure-portál használata az Azure Data Box Edge felhasználóinak kezeléséhez

Ez a cikk ismerteti, hogyan kezelheti a felhasználókat az Azure Data Box Edge. Az Azure Data Box Edge az Azure Portalon keresztül vagy a helyi webes felhasználói felületen keresztül kezelhető. Az Azure Portalon felhasználókat vehet fel, módosíthat vagy törölhet.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Felhasználó hozzáadása
> * Felhasználó módosítása
> * Felhasználó törlése

## <a name="about-users"></a>Információk a felhasználókról

A felhasználók csak olvasási vagy teljes körű jogosultsággal rendelkezhetnek. Ahogy a név is mutatja, a csak olvasási jogosultsággal rendelkező felhasználók kizárólag megtekinteni tudják a megosztott adatokat. A teljes körű jogosultsággal rendelkező felhasználók olvasni és írni is tudják a megosztott adatokat, valamint módosíthatják és törölhetik is azokat.

 - **Teljes körű jogosultsággal rendelkező felhasználó** – Teljes körű hozzáféréssel rendelkező helyi felhasználó.
 - **Csak olvasási jogosultsággal rendelkező felhasználó** – Csak olvasási hozzáféréssel rendelkező helyi felhasználó. Ezek a felhasználók csak olvasási műveleteket megengedő megosztásokhoz vannak társítva.

A felhasználói engedélyek első alkalommal a megosztás létrehozása során, a felhasználó létrehozásakor lesznek meghatározva. A megosztásszintű engedélyek módosítása jelenleg nem támogatott.

## <a name="add-a-user"></a>Felhasználó hozzáadása

Felhasználó hozzáadásához hajtsa végre az alábbi lépéseket az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa **meg az Áttekintés > felhasználók lehetőséget.** Válassza a **+ Felhasználó hozzáadása** lehetőséget a parancssávon.

    ![Felhasználó hozzáadása kiválasztása](media/data-box-edge-manage-users/add-user-1.png)

2. Adja meg a hozzáadni kívánt felhasználó felhasználónevét és jelszavát. Erősítse meg a jelszót, és válassza a **Hozzáadás**lehetőséget.

    ![Felhasználónév és jelszó megadása](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > A következő rendszer által lefoglalt felhasználók nem használhatóak: Rendszergazda, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Vendég.  

3. Egy értesítés jelenik meg, amikor a felhasználó létrehozása elindul, és befejeződött. A felhasználó létrehozása után a parancssávon válassza a **Frissítés** lehetőséget a frissített felhasználói lista megtekintéséhez.


## <a name="modify-user"></a>Felhasználó módosítása

A felhasználó létrehozását követően lehetőség van a hozzá tartozó jelszó módosítására. Válasszon a felhasználók listájából. Adja meg és erősítse meg az új jelszót. Mentse a módosításokat.
 
![Felhasználó módosítása](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törléséhez hajtsa végre a következő lépéseket az Azure Portalon.


1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa **meg az Áttekintés > felhasználók lehetőséget.**

    ![A törölni kívánt felhasználó kiválasztása](media/data-box-edge-manage-users/delete-user-1.png)

2. Jelöljön ki egy felhasználót a felhasználók listájából, majd válassza a **Törlés**lehetőséget.  

   ![Törlés kijelölése](media/data-box-edge-manage-users/delete-user-2.png)

3. A rendszer kérésére erősítse meg a törlést. 

   ![Törlés megerősítése](media/data-box-edge-manage-users/delete-user-3.png)

A törlés végrehajtása után annak megfelelően frissül a felhasználók listája.

![Frissített felhasználók listája](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.
