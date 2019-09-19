---
title: Magánhálózati kapcsolat létrehozása egy Storage-fiókhoz az Azure Private Endpoint használatával
description: Ismerje meg, hogyan csatlakozhat magántulajdonban lévő Storage-fiókhoz az Azure-ban egy privát végpont használatával.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104719"
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

1. A képernyő bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **virtuális hálózat**lehetőséget.
1. A **virtuális hálózat létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a *MyVirtualNetwork*. |
    | Címtartomány | Adja meg a *10.1.0.0/16*értéket. |
    | Subscription | Válassza ki előfizetését.|
    | Resource group | Válassza az **új létrehozása**elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra**. |
    | Location | Válassza a **WestCentralUS**lehetőséget.|
    | Alhálózat – név | Adja meg a *mySubnet*. |
    | Alhálózat – címtartomány | Adja meg a *10.1.0.0/24*értéket. |
    |||
1. Hagyja a többi értéket alapértelmezettként, és válassza a **Létrehozás**lehetőséget.


### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás** > létrehozása**számítási** > **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Image | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Size | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Windows 10 | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza **a Next (tovább) lehetőséget: Lemezek**.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és **válassza a Next (tovább) gombot: Hálózatkezelés**.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtartomány | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Subnet | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="create-your-private-endpoint"></a>Saját végpont létrehozása
Ebben a szakaszban létrehoz egy privát Storage-fiókot egy privát végpont használatával. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás** > létrehozása**Storage** > -**fiók**lehetőséget.

1. A **Storage-fiók létrehozása – alapok**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Tárfiók neve  | Adja meg a *mystorageaccount*. Ha ezt a nevet hozza, hozzon létre egy egyedi nevet. |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Teljesítmény| Hagyja meg az alapértelmezett **standard**értéket. |
    | Fiók típusa | Hagyja meg az alapértelmezett **tárolót (általános célú v2)** . |
    | Replikálás | Válassza a **READ-Access geo-redundáns tárolás (ra-GRS)** lehetőséget. |
    |||
  
3. Válassza **a Next (tovább) lehetőséget: Hálózatkezelés**.
4. A **Storage-fiók létrehozása – hálózatkezelés**, kapcsolati módszer, válassza a **privát végpont**elemet.
5. A **Storage-fiók létrehozása – hálózatkezelés**területen válassza a **magánhálózati végpont hozzáadása**elemet. 
6. A **privát végpont létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Value |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Subscription | Válassza ki előfizetését. |
    | Resource group | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    |Location|Válassza a **WestCentralUS**lehetőséget.|
    |Name (Név)|Adja meg a *myPrivateEndpoint*.  |
    |Tároló alerőforrása|Hagyja meg az alapértelmezett **blobot**. |
    | **HÁLÓZATI** |  |
    | Virtuális hálózat  | Válassza ki a *MyVirtualNetwork* az erőforráscsoport *myResourceGroup*. |
    | Subnet | Válassza a *mySubnet*lehetőséget. |
    | **MAGÁNHÁLÓZATI DNS-INTEGRÁCIÓ**|  |
    | Integrálás saját DNS-zónával  | Hagyja meg az alapértelmezett **Igen értéket**. |
    | saját DNS zóna  | Hagyja meg az alapértelmezett  ** (új) privatelink.blob.Core.Windows.net**. |
    |||
7. Kattintson **az OK gombra**. 
8. Válassza a **felülvizsgálat + létrehozás**lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt. 
9. Amikor megjelenik az **átadott** üzenet ellenőrzése lehetőség, válassza a **Létrehozás**lehetőséget. 
10. Tallózással keresse meg az imént létrehozott Storage-fiók erőforrását.
11. A bal oldali tartalom menüben válassza a **hozzáférési kulcsok** elemet.
12. Válassza a **Másolás** lehetőséget a key1 tartozó kapcsolatok karakterláncában.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza ki **RDP-fájl letöltése**. Az Azure létrehoz egy RDP protokoll ( *. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **több választási lehetőséget** > kell választania**egy másik fiók használatával**.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-storage-account-privately-from-the-vm"></a>Hozzáférés a Storage-fiókhoz a virtuális gépről

Ebben a szakaszban a privát végponton keresztül fog csatlakozni a Storage-fiókhoz.

> [!IMPORTANT]
> A tároló DNS-konfigurációjának manuális módosítására van szükség a gazdagépek fájlján, hogy az tartalmazza az adott fiók teljes tartománynevét. módosítsa a következő fájlt a Windows rendszerhez készült rendszergazdai engedélyekkel: c:\Windows\System32\Drivers\etc\hosts vagy Linux/etc/hosts A fiók DNS-információinak belefoglalása az előző lépésből a következő formátumban [magánhálózati IP-cím] myaccount.blob.core.windows.net

1. A *myVM*távoli asztal nyissa meg a PowerShellt.
2. A `nslookup mystorageaccount.blob.core.windows.net` következőhöz hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
