---
title: Pillanatképek kezelése Azure NetApp Files használatával | Microsoft Docs
description: Ismerteti, hogyan lehet pillanatképeket létrehozni és kezelni Azure NetApp Files használatával.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: d70558efb1ea54f069981062e5379d995dbeddd2
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950340"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure NetApp Filesszal

Azure NetApp Files támogatja az igény szerinti pillanatképek létrehozását és a pillanatkép-szabályzatok használatát az automatikus pillanatkép-létrehozás ütemezéséhez.  A pillanatképet új kötetre is visszaállíthatja.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Igény szerinti pillanatkép létrehozása kötethez

Igény szerint kötet-pillanatképeket is létrehozhat. 

1.  Lépjen arra a kötetre, amelyhez pillanatképet szeretne készíteni. Kattintson a **Pillanatképek**elemre.

    ![Pillanatképek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kattintson a **+ Pillanatkép hozzáadása** lehetőségre egy kötethez igény szerinti pillanatkép létrehozásához.

    ![Pillanatkép hozzáadása](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Az új pillanatkép ablakban adja meg a létrehozandó új pillanatkép nevét.   

    ![Új pillanatkép](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kattintson az **OK** gombra. 

## <a name="manage-snapshot-policies"></a>Pillanatkép-házirendek kezelése

A pillanatkép-szabályzatok használatával automatikusan elvégezheti a kötetek pillanatképeit. Szükség szerint módosíthatja a pillanatkép-szabályzatot, vagy törölheti a már nem szükséges pillanatkép-szabályzatot is.  

### <a name="register-the-feature"></a>A szolgáltatás regisztrálása

A **Pillanatkép-házirend** szolgáltatás jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a szolgáltatást, először regisztrálnia kell a szolgáltatást. 

1. Regisztrálja a szolgáltatást: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. A szolgáltatás regisztrálási állapotának ellenõrzése: 

    > [!NOTE]
    > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Használhatja az Azure CLI-parancsokat is, [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) és [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

### <a name="create-a-snapshot-policy"></a>Pillanatkép-szabályzat létrehozása 

A pillanatkép-szabályzat lehetővé teszi a pillanatképek létrehozásának gyakoriságát óránként, naponta, hetente vagy havonta. Meg kell adnia a köteten megőrizni kívánt Pillanatképek maximális számát is.  

1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend**elemre.

    ![Pillanatkép-házirend navigációja](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  A pillanatkép-házirend ablakban állítsa be a házirend állapotát **engedélyezve**értékre. 

3.  Óránkénti, napi, heti vagy havi pillanatkép-szabályzatok létrehozásához kattintson az **óránként**, **naponta**, **hetente**vagy **havonta** fülre. A **megőrizni kívánt Pillanatképek számának**meghatározása.  

    Tekintse meg a kötetek számára engedélyezett maximális számú pillanatképet tartalmazó [Azure NetApp Files erőforrás-korlátozásait](azure-netapp-files-resource-limits.md) . 

    Az alábbi példa az óránkénti Pillanatképek házirend-konfigurációját mutatja be. 

    ![Pillanatkép-szabályzat óránként](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Az alábbi példa a napi Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat naponta](../media/azure-netapp-files/snapshot-policy-daily.png)

    Az alábbi példa a heti Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat hetente](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Az alábbi példa a havi Pillanatképek házirend-konfigurációját mutatja be.

    ![Pillanatkép-szabályzat havonta](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Kattintson a **Mentés** gombra.  

Ha további pillanatkép-házirendeket kell létrehoznia, ismételje meg a 3. lépést.
A létrehozott szabályzatok megjelennek a pillanatkép-szabályzat lapon.

Ha azt szeretné, hogy a kötet a pillanatkép-szabályzatot használja, a [kötetre kell alkalmaznia a házirendet](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Pillanatkép-szabályzat alkalmazása kötetre

Ha azt szeretné, hogy a kötet egy létrehozott pillanatkép-szabályzatot használjon, a kötetre kell alkalmaznia a szabályzatot. 

1.  Lépjen a **kötetek** lapra, kattintson a jobb gombbal arra a kötetre, amelyre a pillanatkép-szabályzatot alkalmazni kívánja, majd válassza a **Szerkesztés**lehetőséget.

    ![Kötetek jobb gombbal kattintva menü](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  A Szerkesztés ablak **Pillanatkép-házirend**területén válassza ki a kötethez használni kívánt szabályzatot.  A szabályzat alkalmazásához kattintson **az OK** gombra.  

    ![Pillanatkép-szabályzat szerkesztése](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Pillanatkép-szabályzat módosítása 

Módosíthat egy meglévő pillanatkép-szabályzatot a házirend állapotának, a pillanatképek gyakoriságának (óránként, naponta, hetente vagy havonta), illetve a megtartani kívánt Pillanatképek számának módosításához.  
 
1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend**elemre.

2.  Kattintson a jobb gombbal a módosítani kívánt pillanatkép-szabályzatra, majd válassza a **Szerkesztés**lehetőséget.

    ![Pillanatkép-házirend kattintson a jobb gombbal a menüre](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Hajtsa végre a megjelenő pillanatkép-házirend ablak módosításait, majd kattintson a **Mentés**gombra. 

### <a name="delete-a-snapshot-policy"></a>Pillanatkép-szabályzat törlése 

Törölheti azt a pillanatkép-szabályzatot, amelyet már nem szeretne megőrizni.   

1.  A NetApp-fiók nézetében kattintson a **Pillanatkép-házirend**elemre.

2.  Kattintson a jobb gombbal a módosítani kívánt pillanatkép-szabályzatra, majd válassza a **Törlés**lehetőséget.

    ![Pillanatkép-házirend kattintson a jobb gombbal a menüre](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni kívánja a pillanatkép-szabályzatot.   

    ![Pillanatkép-szabályzat törlésének megerősítése](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása új kötetre

Jelenleg csak egy új kötetre állíthatja vissza a pillanatképet. 
1. A pillanatképek listájának megjelenítéséhez válassza a kötetek panel **Pillanatképek** elemét. 
2. Kattintson a jobb gombbal a visszaállítani kívánt pillanatképre, majd válassza a menü **új kötet visszaállítása** elemét.  

    ![Pillanatkép visszaállítása új kötetre](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. A kötet létrehozása ablakban adja meg az új kötet adatait:  
    * **név**   
        Adja meg a létrehozni kívánt kötet nevét.  
        
        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Kvóta**  
        Itt adhatja meg a kötethez lefoglalni kívánt logikai tárterület mennyiségét.  

    ![Visszaállítás új kötetre](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Kattintson a **felülvizsgálat + létrehozás**gombra.  Kattintson a **Create** (Létrehozás) gombra.   
    Az új kötet ugyanazt a protokollt használja, mint amelyet a pillanatkép használ.   
    Az új kötet, amelybe a pillanatkép vissza lett állítva, megjelenik a kötetek panelen.

## <a name="next-steps"></a>Következő lépések

* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
