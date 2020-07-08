---
title: Csatlakozás Azure Cosmos-fiókhoz az Azure Private link használatával
description: Megtudhatja, hogyan érheti el biztonságosan az Azure Cosmos-fiókot egy virtuális gépről egy privát végpont létrehozásával.
author: malopMSFT
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: d074128376d481902a203de3d32ef89aa72d7b3a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84737716"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Csatlakozzon privát módon egy Azure Cosmos-fiókokhoz az Azure Private Linkkel

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi az Azure-erőforrások, például a virtuális gépek (VM-EK) számára a magánjellegű kapcsolati erőforrásokkal való kommunikációt.

Ebből a cikkből megismerheti, hogyan hozhat létre virtuális gépet egy Azure virtuális hálózaton és egy privát végponttal rendelkező Azure Cosmos-fiókot az Azure Portalon. Ezt követően biztonságosan hozzáférhet Azure Cosmos-fiókjához a virtuális gépről.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be a [Azure Portalba.](https://portal.azure.com)

## <a name="create-a-vm"></a>Virtuális gép létrehozása

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és az alhálózatot, amely a privát kapcsolati erőforráshoz (ebben a példában egy Azure Cosmos-fiók) való hozzáféréshez használt virtuális GÉPET üzemelteti.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup|
| **\<virtual-network-name>** | myVirtualNetwork         |
| **\<region-name>**          | USA nyugati középső régiója     |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki az előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter**lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2**értéket. |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adjon meg egy tetszőleges felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újra a jelszót. |
    | **BEJÖVŐ PORTOK SZABÁLYAI** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **nincs**értéket. |
    | **PÉNZ MEGTAKARÍTÁSA** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem**értéket. |
    |||

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **virtuális gép létrehozása – lemezek**területen hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés**lehetőséget.

1. A **virtuálisgép-hálózat létrehozása**területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **http** és az **RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A **felülvizsgálat + létrehozás** oldalon az Azure ellenőrzi a konfigurációt.

1. Amikor megjelenik az **átadott üzenet ellenőrzése** lehetőség, válassza a **Létrehozás**lehetőséget.

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos-fiók létrehozása

Hozzon létre egy [Azure Cosmos SQL API-fiókot](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account). Az egyszerűség kedvéért létrehozhat egy Azure Cosmos-fiókot ugyanabban a régióban, mint a többi erőforrást (ez a "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Hozzon létre egy privát végpontot az Azure Cosmos-fiókhoz

Hozzon létre egy privát hivatkozást az Azure Cosmos-fiókhoz a csatolt cikk Azure Portal szakaszának használatával, a [privát hivatkozás létrehozása](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal) című témakörben leírtak szerint.

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a virtuális gép *myVm* az internetről a következőképpen:

1. A portál keresési sávján adja meg a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött *. rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Hozzáférés az Azure Cosmos-fiókhoz a virtuális gépről

Ebben a szakaszban a magánhálózati végpont használatával csatlakozik az Azure Cosmos-fiókhoz. 

1. Az IP-cím és a DNS-hozzárendelés hozzáadásához jelentkezzen be a virtuális gép *myVM*, nyissa meg a `c:\Windows\System32\Drivers\etc\hosts` fájlt, és adja meg a DNS-adatokat az előző lépésből a következő formátumban:

   [Magánhálózati IP-cím] [Fiók végpontja]. Documents. Azure. com

   **Példa:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. A *myVM*távoli asztal telepítse a [Microsoft Azure Storage Explorert](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).

1. Kattintson a jobb gombbal a **Cosmos db fiókok (előzetes verzió)** elemre.

1. Válassza **a kapcsolódás Cosmos db**lehetőséget.

1. Válassza ki az **API** lehetőséget.

1. A korábban másolt adatok beillesztésével adja meg a kapcsolatok karakterláncát.

1. Válassza a **Tovább** lehetőséget.

1. Kattintson a **Csatlakozás** gombra.

1. Tallózással keresse meg az Azure Cosmos-adatbázisokat és-tárolókat a *mycosmosaccount*.

1. (Opcionálisan) új elemeket adhat hozzá a *mycosmosaccount*.

1. A távoli asztali kapcsolat bezárásával *myVM*.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha elkészült a privát végponttal, az Azure Cosmos-fiókkal és a virtuális géppel, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 

1. Adja meg a *myResourceGroup* a portál tetején található **keresőmezőbe** , és válassza a *myResourceGroup* lehetőséget a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy Azure Cosmos-fiókon és egy privát végponton. Az internetről csatlakozik a virtuális géphez, és biztonságosan kommunikál az Azure Cosmos-fiókkal a privát hivatkozás használatával.

* További információ a privát végpontról: [Mi az az Azure Private Endpoint?](private-endpoint-overview.md).

* Ha többet szeretne megtudni a privát végpontok Azure Cosmos DB használatával való használatának korlátozásáról, tekintse meg [Az Azure Private-hivatkozás Azure Cosmos db](../cosmos-db/how-to-configure-private-endpoints.md) című cikket.
