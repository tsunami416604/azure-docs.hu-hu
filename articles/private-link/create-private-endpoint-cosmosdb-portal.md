---
title: Csatlakozás Azure Cosmos-fiókhoz az Azure Private Link kel
description: Ismerje meg, hogyan érheti el biztonságosan az Azure Cosmos-fiókot egy virtuális gépről egy privát végpont létrehozásával.
author: malopMSFT
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: b7a50a2dabc9503ca5dbdd3388e29cfc69963885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252605"
---
# <a name="connect-privately-to-an-azure-cosmos-account-using-azure-private-link"></a>Csatlakozás privát módon egy Azure Cosmos-fiókhoz az Azure Private Link használatával

Az Azure Private Endpoint az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a Private Link-erőforrásokkal.

Ebben a cikkben megtudhatja, hogyan hozhat létre virtuális gépet egy Azure virtuális hálózaton, és egy Azure Cosmos-fiókot egy privát végpontaz Azure Portalon. Ezután biztonságosan elérheti az Azure Cosmos-fiókot a virtuális gépről.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-vm"></a>Virtuális gép létrehozása

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban egy virtuális hálózatot és az alhálózatot hoz létre a virtuális gép üzemeltetéséhez, amely a private link erőforrás (egy Azure Cosmos-fiók ebben a példában) eléréséhez használt virtuális gép.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroup|
| **\<virtuális hálózat neve>** | myVirtualNetwork         |
| **\<régiónév>**          | USA nyugati középső régiója     |
| **\<IPv4-címtér>**   | 10.1.0.0\16          |
| **\<alhálózat-név>**          | mySubnet        |
| **\<alhálózati címtartomány>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az**Compute** >  **Erőforrás** > létrehozása**virtuális gép**létrehozása lehetőséget.

1. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm*- |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett **Nincs szükség infrastruktúra-redundanciára**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter lehetőséget.** |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2 -t.** |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adja meg a választott jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újra a jelszót. |
    | **BEJÖVŐ PORTSZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **Nincs**. |
    | **TAKARÍTSON MEG PÉNZT** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **Nem értéket.** |
    |||

1. Válassza a **Tovább: Lemezek**lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek**csoportban hagyja meg az alapértelmezett beállításokat, és válassza a **Tovább: Hálózat lehetőséget.**

1. A **Virtuális gép létrehozása - Hálózat csoportban**válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-ip**. |
    | Nyilvános bejövő portok | Válassza **a Kijelölt portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **HTTP** és **az RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos-fiók létrehozása

Hozzon létre egy [Azure Cosmos SQL API-fiókot.](../cosmos-db/create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) Az egyszerűség kedvéért létrehozhatja az Azure Cosmos-fiókot ugyanabban a régióban, mint a többi erőforrás (azaz a "WestCentralUS").

## <a name="create-a-private-endpoint-for-your-azure-cosmos-account"></a>Privát végpont létrehozása az Azure Cosmos-fiókhoz

Hozzon létre egy privát kapcsolatot az Azure Cosmos-fiókjához a Privát kapcsolat létrehozása a csatolt cikk Azure Portal szakaszának használatával című részében leírtak [szerint.](../cosmos-db/how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-the-azure-portal)

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Csatlakozzon a vm *myVm* az internetről az alábbiak szerint:

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a letöltött *.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  

## <a name="access-the-azure-cosmos-account-privately-from-the-vm"></a>Az Azure Cosmos-fiók elérése a virtuális gépről

Ebben a szakaszban privát módon csatlakozik az Azure Cosmos-fiókhoz a privát végpont használatával. 

1. Az IP-cím és a DNS-hozzárendelés felvételéhez jelentkezzen be `c:\Windows\System32\Drivers\etc\hosts` a virtuális *gépmyVM-be,* nyissa meg a fájlt, és az előző lépésDNS-adatait a következő formátumban adja meg:

   [Privát IP-cím] [Fiókvégpont].documents.azure.com

   **Példa:**

   10.1.255.13 mycosmosaccount.documents.azure.com

   10.1.255.14 mycosmosaccount-eastus.documents.azure.com


1. A *myVM*távoli asztalán telepítse a [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows)alkalmazást.

1. Válassza **ki a Cosmos DB-fiókok (Előzetes verzió)** a jobb gombbal.

1. Válassza **a Csatlakozás a Cosmos DB-hoz**lehetőséget.

1. Válassza ki az **API** lehetőséget.

1. Adja meg a kapcsolati karakterláncot a korábban másolt adatok beillesztésével.

1. Válassza a **Tovább lehetőséget.**

1. Kattintson a **Csatlakozás** gombra.

1. Böngésszen az Azure Cosmos-adatbázisok és -tárolók között a *mycosmosaccount*ból.

1. (Opcionálisan) új elemeket *amycosmosaccount*.

1. Zárja be a távoli asztali kapcsolatot a *myVM-mel.*

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a privát végpont, az Azure Cosmos-fiók és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 

1. Írja be a *myResourceGroup* kifejezést a portál tetején lévő **Keresőmezőbe,** és válassza a *myResourceGroup* elemet a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a *myResourceGroup értéket* **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy virtuális gépet egy virtuális hálózaton, egy Azure Cosmos-fiókot és egy privát végpontot. A virtuális géphez az internetről csatlakozott, és biztonságosan kommunikált az Azure Cosmos-fiókkal a Privát kapcsolat használatával.

* Ha többet szeretne megtudni a Privát végpontról, olvassa el [a Mi az Azure-beli privát végpont?](private-endpoint-overview.md).

* Ha többet szeretne megtudni a private endpoint korlátozásáról az Azure Cosmos DB használatával való használat során, olvassa el az [Azure Private Link with Azure Cosmos DB](../cosmos-db/how-to-configure-private-endpoints.md) cikkét.
