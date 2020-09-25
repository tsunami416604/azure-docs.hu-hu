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
ms.date: 09/04/2020
ms.author: b-juche
ms.openlocfilehash: e1be0879af02fac0f7ae926a02ea23fd6be84de4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325689"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure NetApp Filesszal

Azure NetApp Files támogatja az igény szerinti pillanatképek létrehozását és a pillanatkép-szabályzatok használatát az automatikus pillanatkép-létrehozás ütemezéséhez.  A pillanatképet új kötetre is visszaállíthatja, vagy egyetlen fájlt is visszaállíthat egy ügyfél használatával.  

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
Használhatja az [Azure CLI-parancsokat](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

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

4. Kattintson a **felülvizsgálat + létrehozás**gombra.  Kattintson a **Létrehozás** lehetőségre.   
    Az új kötet ugyanazt a protokollt használja, mint amelyet a pillanatkép használ.   
    Az új kötet, amelybe a pillanatkép vissza lett állítva, megjelenik a kötetek panelen.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Fájl visszaállítása pillanatképből ügyfél használatával

Ha nem szeretné [visszaállítani a teljes pillanatképet egy kötetre](#restore-a-snapshot-to-a-new-volume), lehetősége van visszaállítani egy fájlt egy pillanatképből egy olyan ügyfél használatával, amelyhez a kötet csatlakoztatva van.  

A csatlakoztatott kötet tartalmazza  `.snapshot` az ügyfél számára elérhető pillanatkép-könyvtárat (az NFS-ügyfeleken) vagy `~snapshot` (az SMB-ügyfeleken). A pillanatkép könyvtára a kötet pillanatképeit reprezentáló alkönyvtárakat tartalmaz. Minden alkönyvtár tartalmazza a pillanatkép fájljait. Ha véletlenül töröl vagy felülír egy fájlt, visszaállíthatja a fájlt a szülő írható-olvasható könyvtárára úgy, hogy a fájlt egy pillanatkép-alkönyvtárból az írható-olvasható könyvtárba másolja. 

Ha a kötet létrehozásakor a pillanatkép elérési útjának elrejtése jelölőnégyzet be van jelölve, a pillanatkép-könyvtár rejtett. Megtekintheti a kötet pillanatkép-elérési útjának állapotának elrejtését a kötet kiválasztásával. A pillanatkép elérési útjának elrejtése lehetőségre kattintva szerkesztheti a kötet oldalának **Szerkesztés** elemét.  

![Kötet-pillanatkép beállításainak szerkesztése](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Fájl visszaállítása Linux NFS-ügyfél használatával 

1. A `ls` Linux parancs használatával listázhatja a címtárból visszaállítani kívánt fájlt `.snapshot` . 

    Például:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. A `cp` parancs használatával másolja a fájlt a szülő könyvtárba.  

    Például: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Fájl visszaállítása Windows-ügyfél használatával 

1. Ha a `~snapshot` kötet könyvtára rejtett, jelenítse meg a szülő könyvtár [rejtett elemeit](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) `~snapshot` .

    ![Rejtett elemek megjelenítése](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. A `~snapshot` visszaállítani kívánt fájl megkereséséhez navigáljon a belül található alkönyvtárra.  Kattintson a jobb gombbal a fájlra. Válassza a **Másolás** lehetőséget.  

    ![Fájl másolása a visszaállításhoz](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Térjen vissza a szülő könyvtárhoz. Kattintson a jobb gombbal a szülő könyvtárra, és válassza ki a `Paste` fájlt a könyvtárba.

    ![A visszaállítandó fájl beillesztése](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Azt is megteheti, hogy a jobb gombbal a szülő könyvtárra kattint, majd kiválasztja a **Tulajdonságok**elemet, majd a **korábbi verziók** lapon megtekintheti a pillanatképek listáját, és a **visszaállítás** elemre kattintva állíthatja vissza a fájlt.  

    ![Korábbi verziók tulajdonságai](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Következő lépések

* [Pillanatkép-házirendek – problémamegoldás](troubleshoot-snapshot-policies.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files Pillanatképek 101 videó](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
