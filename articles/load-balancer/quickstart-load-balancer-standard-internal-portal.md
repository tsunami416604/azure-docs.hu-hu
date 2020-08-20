---
title: 'Rövid útmutató: belső terheléselosztó létrehozása – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ez a rövid útmutató bemutatja, hogyan hozható létre belső terheléselosztó a Azure Portal használatával.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a internal load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: e8d11c2122a21b67620987ad9ef74efc99eeb98b
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654497"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Rövid útmutató: belső terheléselosztó létrehozása a virtuális gépek terheléselosztásához a Azure Portal használatával

A Azure Load Balancer használatának első lépései a Azure Portal a belső terheléselosztó és két virtuális gép létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

---

# <a name="standard-sku"></a>[**Standard termékváltozat**](#tab/option-1-create-internal-load-balancer-standard)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott.  További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Létrehozhat egy nyilvános terheléselosztó vagy egy belső terheléselosztó is. 

Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. 

A virtuális hálózatban a magánhálózati IP-címek úgy vannak konfigurálva, mint a terheléselosztó (alapértelmezés szerint **LoadBalancerFrontend** ). 

A frontend IP-címe **statikus** vagy **dinamikus**lehet.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > virtuális hálózat** lehetőséget, vagy keressen rá a **virtuális hálózatra** a keresőmezőbe.

2. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |                                                                 |
    | Név             | **MyVNet** megadása                                    |
    | Region           | **Nyugat-Európa** kiválasztása |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címterület | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

6. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MyBackendSubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **Biztonság** fület.

9. A **BastionHost**területen válassza az **Engedélyezés**lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név**mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki az előző lépésben létrehozott **myresourcegrouplb erőforráscsoportban** .|
    | Név                   | **MyLoadBalancer** megadása                                   |
    | Region         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **belső**lehetőséget.                                        |
    | Termékváltozat           | **Standard** kiválasztása |
    | Virtuális hálózat | Válassza ki az előző lépésben létrehozott **myVNet** . |
    | Alhálózat  | Válassza ki az előző lépésben létrehozott **myBackendSubnet** . |
    | IP-cím hozzárendelése | Válassza a **dinamikus**lehetőséget. |
    | A rendelkezésre állási zóna | **Zóna kiválasztása – redundáns** |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-standard-internal-load-balancer.png" alt-text="Standard belső terheléselosztó létrehozása" border="true":::
 
## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Egy terheléselosztó-szabály.

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

3. Hagyja meg a többi alapértelmezett értéket, majd kattintson az **OK gombra**.

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
    | Implicit kimenő szabályok létrehozása | Válassza a **Nem** lehetőséget.

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

>[!NOTE]
>A háttér-készletben lévő virtuális gépek nem rendelkeznek kimenő internetkapcsolattal ezzel a konfigurációval. </br> A kimenő kapcsolatok nyújtásával kapcsolatos további információkért lásd: </br> **[Kimenő kapcsolatok az Azure-ban](load-balancer-outbound-connections.md)**</br> Kapcsolatok biztosításának lehetőségei: </br> **[Csak kifelé irányuló terheléselosztó konfigurációja](egress-only.md)** </br> **[Mi az Virtual Network NAT?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

## <a name="create-backend-servers"></a>Háttérkiszolgálók létrehozása

Ebben a szakaszban:

* Hozzon létre két virtuális gépet a terheléselosztó backend-készletéhez.
* Telepítse az IIS-t a virtuális gépeken a terheléselosztó teszteléséhez.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a szakaszban két virtuális gépet hoz létre (**myVM1** és **myVM2**) egy standard nyilvános IP-címmel két zónában (**1. zóna** és **2. zóna**). 

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget. 
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Region | **Nyugat-Európa** kiválasztása |
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
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása**területen adja meg **myNSG** a myNSG **nevet**. </br> Kattintson **az OK gombra** |
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
   
7. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

9. Az 1 – 8. lépés végrehajtásával hozzon létre egy további virtuális gépet a következő értékekkel, valamint az összes többi beállítást, amely ugyanaz, mint a **myVM1**:

    | Beállítás | VM 2|
    | ------- | ----- |
    | Név |  **myVM2** |
    | A rendelkezésre állási zóna | **2** |
    | Hálózati biztonsági csoport | Meglévő **myNSG** kiválasztása|


# <a name="basic-sku"></a>[**Alapszintű termékváltozat**](#tab/option-1-create-internal-load-balancer-basic)

>[!NOTE]
>A standard SKU Load Balancer használata éles számítási feladatokhoz ajánlott.  További információ az SKU-ról: **[Azure Load Balancer SKU](skus.md)**-ban.

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. 

Létrehozhat egy nyilvános terheléselosztó vagy egy belső terheléselosztó is. 

Belső terheléselosztó létrehozásakor a rendszer a terheléselosztó hálózatként konfigurálja a virtuális hálózatot. 

A virtuális hálózatban a magánhálózati IP-címek úgy vannak konfigurálva, mint a terheléselosztó (alapértelmezés szerint **LoadBalancerFrontend** ). 

A frontend IP-címe **statikus** vagy **dinamikus**lehet.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > virtuális hálózat** lehetőséget, vagy keressen rá a **virtuális hálózatra** a keresőmezőbe.

2. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |                                                                 |
    | Név             | **MyVNet** megadása                                    |
    | Region           | **Nyugat-Európa** kiválasztása |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címterület | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve**alatt válassza ki az **alapértelmezett**szót.

6. Az **alhálózat szerkesztése**területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MyBackendSubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **Biztonság** fület.

9. A **BastionHost**területen válassza az **Engedélyezés**lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név**mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Kattintson a **Létrehozás** gombra.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer**elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki az előző lépésben létrehozott **myresourcegrouplb erőforráscsoportban** .|
    | Név                   | **MyLoadBalancer** megadása                                   |
    | Region         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **belső**lehetőséget.                                        |
    | Termékváltozat           | **Alapszintű** kiválasztása |
    | Virtuális hálózat | Válassza ki az előző lépésben létrehozott **myVNet** . |
    | Alhálózat  | Válassza ki az előző lépésben létrehozott **myBackendSubnet** . |
    | IP-cím hozzárendelése | Válassza a **dinamikus**lehetőséget. |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/create-basic-internal-load-balancer.png" alt-text="Standard belső terheléselosztó létrehozása" border="true":::

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Egy terheléselosztó-szabály.

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

4. Válassza a **Hozzáadás** elemet.

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
    | Elérési út | Be **/** |
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|

3. Válassza az **OK** lehetőséget.

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

* Hozzon létre két virtuális gépet a terheléselosztó backend-készletéhez.
* Hozzon létre egy rendelkezésre állási készletet a virtuális gépek számára.
* Telepítse az IIS-t a virtuális gépeken a terheléselosztó teszteléséhez.

### <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

A nyilvános IP-SKU-nak és a terheléselosztó SKU-nak egyeznie kell. Alapszintű Load Balancer esetén használjon alapszintű IP-címmel rendelkező virtuális gépeket a háttér-készletben. 

Ebben a szakaszban két virtuális gépet hoz létre (**myVM1**és **myVM2**) egy alapszintű nyilvános IP-címmel.  

A két virtuális gép egy **myAvailabilitySet**nevű rendelkezésre állási csoportba kerül.

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget. 
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Region | **Nyugat-Európa** kiválasztása |
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
    | Nyilvános IP-cím | Válassza a **nincs** lehetőséget |
    | NIC hálózati biztonsági csoport | Válassza a **speciális** lehetőséget|
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása**területen adja meg **myNSG** a myNSG **nevet**. </br> Kattintson **az OK gombra** |
    | **Terheléselosztás**  |
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Válassza a **nem** lehetőséget |
 
5. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget.

6. A **kezelés** lapon válassza ki vagy írja be a következőket:
    
    | Beállítás | Érték |
    |-|-|
    | **Figyelés** |  |
    | Rendszerindítási diagnosztika | Kijelölés **kikapcsolva** |

7. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

9. Az 1 – 8. lépés végrehajtásával hozzon létre egy további virtuális gépet a következő értékekkel, valamint az összes többi beállítást, amely ugyanaz, mint a **myVM1**:

    | Beállítás | VM 2 |
    | ------- | ----- |
    | Név |  **myVM2** |
    | Rendelkezésre állási csoport| **MyAvailabilitySet** kiválasztása |
    | Hálózati biztonsági csoport | Meglévő **myNSG** kiválasztása|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Virtuális gépek hozzáadása a háttérbeli készlethez

Az előző lépésekben létrehozott virtuális gépeket hozzá kell adni a **myLoadBalancer**háttér-készletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **myBackendPool**lehetőséget.

3. Válassza ki a **hozzárendelt** **virtuális gépeket** a következőhöz:.

4. A **Virtual Machines (virtuális gépek** ) szakaszban válassza a **+ Hozzáadás**lehetőséget.

5. Jelölje be a **myVM1** és a **myVM2**melletti jelölőnégyzetet.

6. Válassza a **Hozzáadás** elemet.

7. Kattintson a **Mentés** gombra.
---

## <a name="create-test-virtual-machine"></a>Teszt virtuális gép létrehozása

Ebben a szakaszban létrehoz egy **myTestVM**nevű virtuális gépet.  Ezt a virtuális gépet a terheléselosztó konfigurációjának tesztelésére fogjuk használni.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget. 
   
2. A **virtuális gép létrehozása**területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány részletei** |  |
    | Virtuális gép neve | **MyTestVM** megadása |
    | Region | **Nyugat-Európa** kiválasztása |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
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
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | Válassza a **speciális** lehetőséget|
    | Hálózati biztonsági csoport konfigurálása | Válassza ki az előző lépésben létrehozott **MyNSG** .|
    
5. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés**lehetőséget.

6. A **kezelés** lapon válassza ki vagy írja be a következőket:
    
    | Beállítás | Érték |
    |-|-|
    | **Figyelés** |  |
    | Rendszerindítási diagnosztika | Kijelölés **kikapcsolva** |
   
7. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

## <a name="install-iis"></a>Az IIS telepítése

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz található.

2. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion**lehetőséget.

4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

5. Válassza a **Kapcsolódás** lehetőséget.

6. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök**  >  **Windows PowerShell**elemre.

7. A PowerShell ablakban futtassa a következő parancsokat:

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
8. A megerősített munkamenet lezárása a **myVM1**.

9. Az 1–6. lépés megismétlésével telepítse az IIS-t és a frissített iisstart.htm fájlt a **myVM2** gépen.


## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. A terheléselosztó magánhálózati IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd válassza a **myLoadBalancer**lehetőséget.

2. Jegyezze fel, vagy másolja a **magánhálózati IP-cím** melletti címet a **myLoadBalancer** **áttekintésében** .

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza ki a **myTestVM** , amely a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion**lehetőséget.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myTestVM**.

8. Az előző lépésben adja meg az IP-címet a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Standard belső terheléselosztó létrehozása" border="true":::
   
Ha meg szeretné tekinteni, hogy a terheléselosztó mindhárom virtuális gépen osztja el a forgalmat, testreszabhatja az egyes virtuális gépek IIS-webkiszolgálójának alapértelmezett oldalát, majd kényszerítheti a webböngésző frissítését az ügyfélgépről.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztó és az összes kapcsolódó erőforrást. Ehhez válassza ki az erőforrásokat tartalmazó erőforráscsoportot, majd válassza a **Törlés**lehetőséget a **myresourcegrouplb erőforráscsoportban** .

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy Azure standard vagy alapszintű belső Load Balancer
* 2 virtuális gép csatlakoztatva a terheléselosztó számára.
* Konfigurálta a terheléselosztó forgalmi szabályát, az állapot-mintavételt, majd tesztelte a terheléselosztó. 

Ha többet szeretne megtudni a Azure Load Balancerről, folytassa a [mi Azure Load Balancer?](load-balancer-overview.md) és [Load Balancer a gyakori kérdések](load-balancer-faqs.md)című témakört.

* További információ a [Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).
* További információ az [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)-ről.
