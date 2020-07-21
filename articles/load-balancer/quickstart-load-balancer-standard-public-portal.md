---
title: 'Rövid útmutató: nyilvános Load Balancer létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre terheléselosztó-t a Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: eb23f1e703c2e447c484ccb366914cb4b23c5bf7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536546"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Gyors útmutató: terheléselosztó létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával

A Azure Load Balancer használatának első lépései a Azure Portal használatával a nyilvános Load Balancer és a három virtuális gép létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

---

# <a name="option-1-default-create-a-load-balancer-standard-sku"></a>[1. lehetőség (alapértelmezett): Load Balancer létrehozása (standard SKU)](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott.  További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Létrehozhat egy nyilvános terheléselosztó vagy egy belső terheléselosztó is. 

Nyilvános terheléselosztó létrehozásakor létre kell hoznia egy új nyilvános IP-címet, amely a terheléselosztó számára (alapértelmezés szerint **LoadBalancerFrontend** ) van konfigurálva.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** elemet, és írja be a **myresourcegrouplb erőforráscsoportban** szöveget a szövegmezőbe.|
    | Name                   | **MyLoadBalancer** megadása                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | **Standard** kiválasztása |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha meglévő nyilvános IP-címmel szeretne használni, válassza a **meglévő használata**lehetőséget. |
    | Nyilvános IP-cím | Írja be a **myPublicIP** szöveget a szövegmezőbe.|
    | A rendelkezésre állási zóna | Válassza ki a **felesleges zónát** a rugalmas Load Balancer létrehozásához. A Zona Load Balancer létrehozásához jelöljön ki egy 1, 2 vagy 3 típusú zónát |
    | A nyilvános IPv6-cím hozzáadása | Válassza a **Nem** lehetőséget. </br> További információ az IPv6-címekről és a Load balancerről: [Mi az az IPv6 for Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Standard terheléselosztó létrehozása" border="true":::
 
## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Terheléselosztó-szabály és automatikus Kimenő szabály.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttér-címkészlet tartalmazza a terheléselosztó számára csatlakoztatott virtuális (NIC) IP-címeit. 

Hozza létre a háttér-címkészlet **myBackendPool** , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myBackendPool**nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

A terheléselosztó az alkalmazás állapotát állapottal figyeli. 

Az állapot mintavétele a terheléselosztó által az állapot-ellenőrzésekre adott válasz alapján hozzáadja vagy eltávolítja a virtuális gépeket. 

Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza az **állapot**-mintavételek, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **http**lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    | | |

3. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A bejövő forgalom és a háttérbeli IP-készlet előtérbeli IP-konfigurációját a forgalom fogadásához adja meg. A forrás-és a célport a szabályban van definiálva. 

Ebben a szakaszban egy terheléselosztó-szabályt fog létrehozni:

* Elnevezett **: myhttprule**.
* A **LoadBalancerFrontEnd**nevű előtérben.
* Figyelés a **80-es porton**.
* Az elosztott terhelésű forgalmat a 80-es **porton**található **myBackendPool** nevű háttérre irányítja.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a terheléselosztási **szabályok**lehetőséget, majd kattintson a **Hozzáadás**gombra.

3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **: myhttprule**. |
    | IP-verzió | **IPv4** kiválasztása |
    | Előtérbeli IP-cím | **LoadBalancerFrontEnd** kiválasztása |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Háttérport | Adja meg a **80**értéket. |
    | A háttérkészlet | Válassza a **myBackendPool**lehetőséget.|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe**lehetőséget. |
    | Implicit kimenő szabályok létrehozása | Válassza az **Igen** lehetőséget. </br> További információ és speciális kimenő szabályok konfigurálása: </br> [Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md) </br> [Terheléselosztás és kimenő szabályok konfigurálása standard Load Balancerban a Azure Portal használatával](configure-load-balancer-outbound-portal.md)

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban:

* Hozzon létre egy virtuális hálózatot.
* Hozzon létre három virtuális gépet a terheléselosztó backend-készletéhez.
* Telepítse az IIS-t a virtuális gépeken a terheléselosztó teszteléséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő információkra cseréli le a paramétereket:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | Myresourcegrouplb erőforráscsoportban |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A nyilvános IP-SKU-nak és a terheléselosztó SKU-nak egyeznie kell. A standard Load Balancer esetében használjon standard IP-címmel rendelkező virtuális gépeket a háttér-készletben. 

Ebben a szakaszban három virtuális gépet hoz létre (**myVM1**, **myVM2** és **myVM3**) egy szabványos nyilvános IP-címmel három különböző zónában (**1. zóna**, **2. zóna**és **3. zóna**). 

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget. 
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Resource Group (Erőforráscsoport) | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Régió | **Nyugat-Európa** kiválasztása |
    | Rendelkezésre állási beállítások | **Rendelkezésre állási zónák** kiválasztása |
    | A rendelkezésre állási zóna | **1** . választás |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **myBackendSubnet** |
    | Nyilvános IP-cím | Fogadja el az alapértelmezett **myVM-IP-címet**. </br> Az IP-cím automatikusan a standard SKU IP-címe lesz 1. zóna. |
    | NIC hálózati biztonsági csoport | Válassza a **speciális** lehetőséget|
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása**területen adja meg **myNSG** a myNSG **nevet**. </br> A **Bejövő szabályok**területen válassza **a + Bejövő szabály hozzáadása**elemet. </br> A **célport tartománya**területen adja meg a **80**értéket. </br> A **Priority (prioritás**) területen adja meg a **100**értéket. </br> A **név**mezőben adja meg a **: myhttprule** </br> **Hozzáadás** kiválasztása </br> Kattintson **az OK gombra** |
    | **Terheléselosztás**  |
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Válassza az **Igen** lehetőséget |
    | **Terheléselosztási beállítások** |
    | Terheléselosztási beállítások | **Azure-terheléselosztás** kiválasztása |
    | Terheléselosztó kiválasztása | **MyLoadBalancer** kiválasztása  |
    | Háttérbeli készlet kiválasztása | **MyBackendPool** kiválasztása |

5. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget.

6. A **kezelés** lapon válassza ki vagy írja be a következőket:
    
    | Beállítás | Érték |
    |-|-|
    | **Figyelés** |  |
    | Rendszerindítási diagnosztika | Kijelölés **kikapcsolva** |
   
7. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

9. Az 1 – 8. lépés végrehajtásával hozzon létre két további virtuális gépet a következő értékekkel és az összes többi beállítással, amely ugyanaz, mint a **myVM1**:

    | Beállítás | VM 2| 3. VIRTUÁLIS GÉP|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | A rendelkezésre állási zóna | **2** |**3**|
    | Hálózati biztonsági csoport | Meglévő **myNSG** kiválasztása| Meglévő **myNSG** kiválasztása|


# <a name="option-2-create-a-load-balancer-basic-sku"></a>[2. lehetőség: terheléselosztó létrehozása (alapszintű SKU)](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott.  További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Létrehozhat egy nyilvános terheléselosztó vagy egy belső terheléselosztó is. 

Nyilvános terheléselosztó létrehozásakor létre kell hoznia egy új nyilvános IP-címet, amely a terheléselosztó számára (alapértelmezés szerint **LoadBalancerFrontend** ) van konfigurálva.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a **myresourcegrouplb erőforráscsoportban** szöveget a szövegmezőbe.|
    | Name                   | **MyLoadBalancer** megadása                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | **Alapszintű** kiválasztása |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha meglévő nyilvános IP-címmel szeretne használni, válassza a **meglévő használata**lehetőséget. |
    | Nyilvános IP-cím | Írja be a **myPublicIP** szöveget a szövegmezőbe.|
    | Hozzárendelés | **Dinamikus** kiválasztása |
    | A nyilvános IPv6-cím hozzáadása | Válassza a **Nem** lehetőséget. </br> További információ az IPv6-címekről és a Load balancerről: [Mi az az IPv6 for Azure Virtual Network?](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)  |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Alapszintű Load Balancer létrehozása" border="true":::

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Hozzon létre egy virtuális hálózatot.
* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Egy terheléselosztó-szabály.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő információkra cseréli le a paramétereket:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | Myresourcegrouplb erőforráscsoportban |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | West Europe      |
| **\<IPv4-address-space>**   | 10.1.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttér-címkészlet tartalmazza a terheléselosztó számára csatlakoztatott virtuális (NIC) IP-címeit. 

Hozza létre a háttér-címkészlet **myBackendPool** , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.

3. A **háttérrendszer-készlet hozzáadása** lapon adja meg vagy válassza ki a következőket:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myBackendPool**. |
    | Virtuális hálózat | Válassza a **myVNet**lehetőséget. |
    | Társítva ezzel: | **Virtuális gépek** kiválasztása |

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

A terheléselosztó az alkalmazás állapotát állapottal figyeli. 

Az állapot mintavétele a terheléselosztó által az állapot-ellenőrzésekre adott válasz alapján hozzáadja vagy eltávolítja a virtuális gépeket. 

Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza az **állapot**-mintavételek, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **http**lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Elérési út | Be**/** |
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|

3. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A bejövő forgalom és a háttérbeli IP-készlet előtérbeli IP-konfigurációját a forgalom fogadásához adja meg. A forrás-és a célport a szabályban van definiálva. 

Ebben a szakaszban egy terheléselosztó-szabályt fog létrehozni:

* Elnevezett **: myhttprule**.
* A **LoadBalancerFrontEnd**nevű előtérben.
* Figyelés a **80-es porton**.
* Az elosztott terhelésű forgalmat a 80-es **porton**található **myBackendPool** nevű háttérre irányítja.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a terheléselosztási **szabályok**lehetőséget, majd kattintson a **Hozzáadás**gombra.

3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **: myhttprule**. |
    | IP-verzió | **IPv4** kiválasztása |
    | Előtérbeli IP-cím | **LoadBalancerFrontEnd** kiválasztása |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Háttérport | Adja meg a **80**értéket. |
    | A háttérkészlet | Válassza a **myBackendPool**lehetőséget.|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe**lehetőséget. |
 
4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban:

* Hozzon létre három virtuális gépet a terheléselosztó backend-készletéhez.
* Hozzon létre egy rendelkezésre állási készletet a virtuális gépek számára.
* Telepítse az IIS-t a virtuális gépeken a terheléselosztó teszteléséhez.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A nyilvános IP-SKU-nak és a terheléselosztó SKU-nak egyeznie kell. Alapszintű Load Balancer esetén használjon alapszintű IP-címmel rendelkező virtuális gépeket a háttér-készletben. 

Ebben a szakaszban három virtuális gépet hoz létre (**myVM1**, **myVM2**és **myVM3**) egy alapszintű nyilvános IP-címmel.  

A három virtuális gép egy **myAvailabilitySet**nevű rendelkezésre állási csoportba kerül.

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget. 
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Resource Group (Erőforráscsoport) | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Régió | **Nyugat-Európa** kiválasztása |
    | Rendelkezésre állási beállítások | **Rendelkezésre állási csoport** kiválasztása |
    | Rendelkezésre állási csoport | Válassza az **Új létrehozása** lehetőséget. </br> Adja meg a **MyAvailabilitySet** **nevet**. </br> Kattintson **az OK gombra** |
    | Kép | **Windows Server 2019 Datacenter** |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **MyVNet** kiválasztása |
    | Alhálózat | **MyBackendSubnet** kiválasztása |
    | Nyilvános IP-cím | Válassza az **új létrehozása** lehetőséget. </br> Adja meg a **myVM-IP** nevet a névben. </br> Kattintson **az OK gombra** |
    | NIC hálózati biztonsági csoport | Válassza a **speciális** lehetőséget|
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása**területen adja meg **myNSG** a myNSG **nevet**. </br> A **Bejövő szabályok**területen válassza **a + Bejövő szabály hozzáadása**elemet. </br> A **célport tartománya**területen adja meg a **80**értéket. </br> A **Priority (prioritás**) területen adja meg a **100**értéket. </br> A **név**mezőben adja meg a **: myhttprule** </br> **Hozzáadás** kiválasztása </br> Kattintson **az OK gombra** |
    | **Terheléselosztás**  |
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Válassza a **nem** lehetőséget |
 
5. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget.

6. A **kezelés** lapon válassza ki vagy írja be a következőket:
    | Beállítás | Érték |
    |-|-|
    | **Figyelés** | |
    | Rendszerindítási diagnosztika | Kijelölés **kikapcsolva** |

7. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

9. Az 1 – 8. lépés végrehajtásával hozzon létre két további virtuális gépet a következő értékekkel és az összes többi beállítással, amely ugyanaz, mint a **myVM1**:

    | Beállítás | VM 2| 3. VIRTUÁLIS GÉP|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Rendelkezésre állási csoport| **MyAvailabilitySet** kiválasztása | **MyAvailabilitySet** kiválasztása|
    | Hálózati biztonsági csoport | Meglévő **myNSG** kiválasztása| Meglévő **myNSG** kiválasztása|
---

### <a name="install-iis"></a>Az IIS telepítése

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz található.

2. Az **Áttekintés** lapon válassza a **Kapcsolódás** lehetőséget a virtuális gép RDP-fájljának letöltéséhez.

3. Nyissa meg az RDP-fájlt.

4. Jelentkezzen be a virtuális gépre az adott virtuális gép létrehozásakor megadott hitelesítő adatokkal.

5. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök** > **Windows PowerShell**elemre.

6. A PowerShell ablakban futtassa a következő parancsokat:

    * Az IIS-kiszolgáló telepítése
    * Az alapértelmezett iisstart.htm fájl eltávolítása
    * Adjon hozzá egy új iisstart.htm fájlt, amely megjeleníti a virtuális gép nevét:

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
7. Az RDP-munkamenet lezárása a **myVM1**.

8. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a **myVM2** és **myVM3** gépeken.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. A terheléselosztó nyilvános IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd válassza a **myPublicIP**lehetőséget.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha meg szeretné tekinteni, hogy a terheléselosztó mindhárom virtuális gépen osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztó és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforrásokat tartalmazó erőforráscsoportot, majd válassza a **Törlés**lehetőséget a **myresourcegrouplb erőforráscsoportban** .

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy Azure standard vagy alapszintű Load Balancer
* 3 virtuális gép csatlakoztatva a terheléselosztó számára.
* Konfigurálta a terheléselosztó forgalmi szabályát, az állapot-mintavételt, majd tesztelte a terheléselosztó. 

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [mi Azure Load Balancer?](load-balancer-overview.md) és [Load Balancer a gyakori kérdések](load-balancer-faqs.md)című témakört.

További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).
