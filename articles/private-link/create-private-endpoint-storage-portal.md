---
title: Magánhálózati kapcsolat létrehozása egy Storage-fiókhoz az Azure Private Endpoint használatával
description: Ismerje meg, hogyan csatlakozhat magántulajdonban lévő Storage-fiókhoz az Azure-ban egy privát végpont használatával.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 2a2a96a823867ea7700933c8253a0ba500b0e1cf
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74899803"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Magánhálózati kapcsolat létrehozása egy Storage-fiókhoz az Azure Private Endpoint használatával
Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára a magánjellegű kapcsolati erőforrásokkal való kommunikációt.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre virtuális gépet egy Azure-beli virtuális hálózaton, egy privát végponttal rendelkező Storage-fiókot a Azure Portal használatával. Ezután biztonságosan hozzáférhet a Storage-fiókhoz a virtuális gépről.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amely a privát kapcsolati erőforrás eléréséhez használt virtuális GÉPET (ebben a példában a Storage-fiókot) tárolja.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot a privát kapcsolati erőforrás elérésére használt virtuális gép üzemeltetéséhez.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.
1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Adja meg a *MyVirtualNetwork*. |
    | Címtér | Adja meg a *10.1.0.0/16*értéket. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Földrajzi egység | Válassza a **WestCentralUS**lehetőséget.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
    |||
1. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.


### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **számítási** > **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Region (Régió) | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Lemezkép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="create-your-private-endpoint"></a>Saját végpont létrehozása
Ebben a szakaszban létrehoz egy privát Storage-fiókot egy privát végpont használatával. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **Storage** > **Storage-fiók**lehetőséget.

1. A **Storage-fiók létrehozása – alapok**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Storage account name (Tárfiók neve)  | Adja meg a *mystorageaccount*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    | Region (Régió) | Válassza a **WestCentralUS**lehetőséget. |
    | Teljesítmény| Hagyja meg az alapértelmezett **standard**értéket. |
    | Fióktípus | Hagyja meg az alapértelmezett **tárolót (általános célú v2)** . |
    | Replikáció | Válassza a **READ-Access geo-redundáns tárolás (ra-GRS)** lehetőséget. |
    |||
  
3. Válassza a **Tovább: hálózatkezelés**lehetőséget.
4. A **Storage-fiók létrehozása – hálózatkezelés**, kapcsolati módszer, válassza a **privát végpont**elemet.
5. A **Storage-fiók létrehozása – hálózatkezelés**területen válassza a **magánhálózati végpont hozzáadása**elemet. 
6. A **privát végpont létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    |Földrajzi egység|Válassza a **WestCentralUS**lehetőséget.|
    |Név|Adja meg a *myPrivateEndpoint*.  |
    |Tároló alerőforrása|Hagyja meg az alapértelmezett **blobot**. |
    | **HÁLÓZATI** |  |
    | Virtuális hálózat  | Válassza ki a *MyVirtualNetwork* az erőforráscsoport *myResourceGroup*. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget. |
    | **MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ**|  |
    | Integrálás saját DNS-zónával  | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett **(új) privatelink.blob.Core.Windows.net**. |
    |||
7. Kattintson az **OK** gombra. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
9. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget. 
10. Tallózással keresse meg az imént létrehozott Storage-fiók erőforrását.
11. A bal oldali tartalom menüben válassza a **hozzáférési kulcsok** elemet.
12. Válassza a **Másolás** lehetőséget a key1 tartozó kapcsolatok karakterláncában.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a *letöltött. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** kell kiválasztania > **eltérő fiókot használjon**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-storage-account-privately-from-the-vm"></a>Hozzáférés a Storage-fiókhoz a virtuális gépről

Ebben a szakaszban a privát végponton keresztül fog csatlakozni a Storage-fiókhoz.

1. A *myVM*távoli asztal nyissa meg a PowerShellt.
2. Adja meg `nslookup mystorageaccount.blob.core.windows.net` a következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Telepítse a [Microsoft Azure Storage Explorert](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Kattintson a jobb gombbal a **Storage-fiókok** elemre.
5. Válassza a **Kapcsolódás Azure-tárolóhoz**lehetőséget.
6. Válassza **a kapcsolatok karakterlánc használata**lehetőséget.
7. Kattintson a **Tovább** gombra.
8. A korábban másolt adatok beillesztésével adja meg a kapcsolatok karakterláncát.
9. Kattintson a **Tovább** gombra.
10. Kattintson a **Csatlakozás** gombra.
11. A blob-tárolók tallózása a mystorageaccount 
12. Opcionálisan Mappák létrehozása és/vagy fájlok feltöltése a *mystorageaccount*. 
13. A távoli asztali kapcsolat bezárásával *myVM*. 

További lehetőségek a Storage-fiók eléréséhez:
- A Microsoft Azure Storage Explorer egy önálló ingyenes alkalmazás a Microsofttól, amely lehetővé teszi, hogy vizuálisan működjön az Azure Storage-adatokkal Windows, macOS és Linux rendszeren. Telepítheti az alkalmazást a Storage-fiók tartalmának magánjellegű tallózásához. 
 
- A AzCopy segédprogram egy másik lehetőség az Azure Storage-hoz készült nagy teljesítményű, parancsfájl-továbbításhoz. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha elkészült a privát végponttal, a Storage-fiókkal és a virtuális géppel, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 
1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza ki a *myResourceGroup* a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Adja meg a *myResourceGroup* az **erőforráscsoport nevének** beírásához, majd válassza a **Törlés**lehetőséget. 

## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban létrehozott egy virtuális GÉPET a virtuális hálózaton és a Storage-fiókban, valamint egy privát végpontot. Az internetről csatlakozik egy virtuális géphez, és biztonságosan kommunikál a Storage-fiókkal a privát hivatkozás használatával. További információ a privát végpontról: [Mi az az Azure Private Endpoint?](private-endpoint-overview.md).
