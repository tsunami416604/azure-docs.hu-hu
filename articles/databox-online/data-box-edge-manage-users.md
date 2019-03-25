---
title: Felhasználók kezelése az Azure Data Box Edge |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Data Box Edge lévő felhasználók kezelése az Azure portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: 1fae648b4dc946227564e588c6cd9b70b910b2f7
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403442"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge lévő felhasználók kezelése az Azure portal használatával

Ez a cikk ismerteti, hogyan kezelheti a felhasználókat az Azure Data Box Edge. Az Azure Data Box Edge az Azure Portalon kezelheti, vagy keresztül a helyi webes felhasználói Felületét. Az Azure Portalon felhasználókat vehet fel, módosíthat vagy törölhet.

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

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **áttekintés > felhasználók**. Válassza ki **+ Hozzáadás felhasználó** a parancssávon.

    ![Válassza ki a felhasználó hozzáadása](media/data-box-edge-manage-users/add-user-1.png)

2. Adja meg a hozzáadni kívánt felhasználó felhasználónevét és jelszavát. Erősítse meg a jelszót, és válassza ki **Hozzáadás**.

    ![Adja meg a felhasználónevet és jelszót](media/data-box-edge-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > Ezeket a felhasználókat a rendszer számára vannak fenntartva, és nem használható: Rendszergazda, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Vendég.  

3. Egy értesítés jelenik meg, ha a felhasználó létrehozása elindul, és befejeződött. A parancssávon, a felhasználó létrehozása után válassza ki a **frissítése** felhasználók frissített listájának megtekintéséhez.


## <a name="modify-user"></a>Felhasználó módosítása

A felhasználó létrehozását követően lehetőség van a hozzá tartozó jelszó módosítására. Válassza ki a felhasználók listájából. Adja meg, és az új jelszót. Mentse a módosításokat.
 
![Felhasználó módosítása](media/data-box-edge-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>Felhasználó törlése

Felhasználó törléséhez hajtsa végre a következő lépéseket az Azure Portalon.


1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **áttekintés > felhasználók**.

    ![Válassza ki a felhasználó törlése](media/data-box-edge-manage-users/delete-user-1.png)

2. A felhasználók listájából válasszon ki egy felhasználót, majd **törlése**.  

   ![Válassza a Törlés](media/data-box-edge-manage-users/delete-user-2.png)

3. A rendszer kérésére erősítse meg a törlést. 

   ![Törlés megerősítése](media/data-box-edge-manage-users/delete-user-3.png)

A törlés végrehajtása után annak megfelelően frissül a felhasználók listája.

![Frissített felhasználók listája](media/data-box-edge-manage-users/delete-user-4.png)


## <a name="next-steps"></a>További lépések

- További tudnivalókat a [sávszélesség-kezeléssel foglalkozó részben](data-box-edge-manage-bandwidth-schedules.md) talál.
