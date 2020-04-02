---
title: Hozzon létre egy Azure Data Explorer-fürtöt & db-t a virtuális hálózatban
description: Ebben a cikkben megtudhatja, hogyan hozhat létre egy Azure Data Explorer-fürta virtuális hálózatban.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548903"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Hozzon létre egy Azure Data Explorer-fürtöt a virtuális hálózatban

Az Azure Data Explorer támogatja a fürt üzembe helyezését egy alhálózatba a virtuális hálózatban (VNet). Ez a funkció lehetővé teszi, hogy a fürt privát elérése az Azure virtuális hálózatvagy a helyszínen, erőforrás eléréséhez, például az Event Hub és a storage a virtuális hálózaton belül, és korlátozza a bejövő és kimenő forgalmat.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-network-security-group-nsg"></a>Hálózati biztonsági csoport létrehozása (NSG)

[A hálózati biztonsági csoportok (NSG)](/azure/virtual-network/security-overview) lehetővé teszik a hálózati hozzáférés vezérlését a virtuális hálózaton belül. Az Azure Data Explorer két végpont HTTP (443) és TDS (1433) használatával érhető el. A következő NSG-szabályokat úgy kell konfigurálni, hogy lehetővé tegyék a hozzáférést ezekhez a végpontokhoz a fürt kezeléséhez, figyeléséhez és megfelelő működéséhez.

A hálózati biztonsági csoport létrehozása:

1. Válassza a **+ Erőforrás létrehozása** gombot a portál bal felső sarkában.
1. Keresse meg a *Hálózati biztonsági csoportot*.
1. A **Hálózatbiztonsági csoport csoportban**a képernyő alján válassza a **Létrehozás lehetőséget.**
1. A **Hálózati biztonsági csoport létrehozása** ablakban töltse ki az alábbi információkat.

   ![NSG-űrlap létrehozása](media/vnet-create-cluster-portal/network-security-group.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki a fürthöz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | Az erőforráscsoport | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Név | AzureDataExplorerNsg | Válasszon egy nevet, amely azonosítja a hálózati biztonsági csoportot (NSG) az erőforráscsoportban.  |
    | Régió | *USA nyugati régiója* | Válassza ki azt a régiót, amely a legjobban megfelel az igényeinek.
    | | | |

1. Válassza **a Véleményezés + létrehozás** lehetőséget a fürt részleteinek áttekintéséhez, és a **Létrehozás lehetőséget** a fürt kiépítéséhez.

1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

    ![Erőforrás megnyitása](media/vnet-create-cluster-portal/notification-nsg.png)

1. A **Bejövő biztonsági szabályok** lapon válassza a **+Hozzáadás**lehetőséget.
1. A **Bejövő biztonsági szabály hozzáadása ablakban** töltse ki az alábbi információkat.

    ![NSG bejövő szabályűrlap létrehozása](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Beállítás** | **Ajánlott érték** 
    |---|---|
    | Forrás | Szolgáltatástag
    | Forrásszolgáltatás címkéje | Az AzureDataExplorerManagement
    | Forrásporttartományok | *
    | Cél | VirtualNetwork
    | Célporttartományok | *
    | Protocol (Protokoll) | TCP
    | Műveletek | Engedélyezés
    | Prioritás | 100
    | Név | AllowAzureDataExplorerManagement
    | | |
    
1. Ismételje meg az előző két lépést az összes bejövő és kimenő függőségnél [a virtuális hálózat központi telepítésének függőségei](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)szerint. Másik lehetőségként a kimenő szabályok egyetlen szabállyal helyettesíthetők, hogy a 443-as és a 80-as *portok számára engedélyezze* az internetet.
    
    A bejövő és kimenő függőségek NSG-szabályainak így kell kinézniük:

    ![NSG-szabályok](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Nyilvános IP-címek létrehozása

A lekérdezés (Engine) nyilvános IP-címeinek létrehozása:

1. Válassza a **+ Erőforrás létrehozása** gombot a portál bal felső sarkában.
1. Keresse meg a *Hálózati biztonsági csoportot*.
1. A **Nyilvános IP-cím**csoportban a képernyő alján válassza a **Létrehozás lehetőséget.**
1. A **Nyilvános IP-cím létrehozása** ablaktáblán töltse ki az alábbi adatokat.
   
   ![Nyilvános IP-űrlap létrehozása](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | IP-verzió | IPv4 | Válassza ki az IP-verziót. Csak az IPv4-et támogatjuk.|
    | SKU | Standard | A query (Engine URI-végpont **standard.** |
    | Név | motor-pip | Válasszon egy nevet, amely azonosítja a nyilvános IP-címet az erőforráscsoportban.
    | Előfizetés | Az Ön előfizetése | Válassza ki a nyilvános IP-címhez használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | Az erőforráscsoport | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Hely | *USA nyugati régiója* | Válassza ki azt a régiót, amely a legjobban megfelel az igényeinek.
    | | | |

1. A nyilvános IP-cím létrehozásához válassza a **Létrehozás** lehetőséget.

1. A betöltési (adatkezelési) nyilvános IP-cím létrehozásához kövesse ugyanazokat az utasításokat, és válassza a 
    * **Sku**: Alap
    * **IP-címhozzárendelés**: Statikus

## <a name="create-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A virtuális hálózat és az alhálózat létrehozása:

1. Válassza a **+ Erőforrás létrehozása** gombot a portál bal felső sarkában.
1. Virtuális *hálózat*keresése .
1. A **Virtuális hálózat**csoportban a képernyő alján válassza a **Létrehozás lehetőséget.**
1. A **Virtuális hálózat létrehozása** ablakban töltse ki a következő információkat.

   ![Virtuális hálózati űrlap létrehozása](media/vnet-create-cluster-portal/vnet-blade.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki a fürthöz használni kívánt Azure-előfizetést.|
    | Erőforráscsoport | Az erőforráscsoport | Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot. |
    | Név | AzureDataExplorerVnet | Válasszon egy nevet, amely azonosítja a virtuális hálózatot az erőforráscsoportban.
    | Régió | *USA nyugati régiója* | Válassza ki azt a régiót, amely a legjobban megfelel az igényeinek.
    | | | |

    > [!NOTE]
    > Éles számítási feladatok esetén tervezze meg az alhálózat méretét a [virtuális hálózat csomagalhálózatának mérete](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet) szerint

1. Válassza **a Véleményezés + létrehozás** lehetőséget a fürt részleteinek áttekintéséhez, és a **Létrehozás lehetőséget** a fürt kiépítéséhez.

1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.
1. Lépjen az **Alhálózatok** panelre, és válassza ki az **alapértelmezett** alhálózatot.
    
    ![Alhálózatok panel](media/vnet-create-cluster-portal/subnets.png)

1. Az **alapértelmezett** alhálózati ablakban:
    1. Válassza ki a **hálózati biztonsági csoportot** a legördülő menüből. 
    1. Válassza ki a hálózati biztonsági csoport nevét, ebben az esetben **az AzureDataExplorerNsg**. 
    1. **Mentés**

    ![Alhálózat konfigurálása](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Fürt létrehozása

Hozzon létre egy Azure Data Explorer-fürtöt egy Azure-erőforráscsoportban meghatározott számítási és tárolási erőforrásokkal a [fürt létrehozása](create-cluster-database-portal.md#create-a-cluster)című részében leírtak szerint.

1. A fürt létrehozásának véglegesítése előtt az **Azure Data Explorer létrehozása fürtablakban** válassza a **Hálózat** fület a virtuális hálózat részleteinek megadásához az előző lapokon létrehozott erőforrások használatával:

   ![Fürt virtuális hálózatának létrehozása](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Beállítás** | **Ajánlott érték** | **Mező leírása**
    |---|---|---|
    | Előfizetés | Az Ön előfizetése | Válassza ki a hálózati erőforrásokhoz használni kívánt Azure-előfizetést.|
    | Virtual Network | AzureDataExplorerVnet | Válassza ki az előző lépésekben létrehozott virtuális hálózatot.
    | Alhálózat | alapértelmezett | Válassza ki az előző lépésekben létrehozott alhálózatot.
    | Nyilvános IP lekérdezése | motor-pip | Válassza ki az előző lépésekben létrehozott nyilvános lekérdezési IP-címet.
    | Adatok betöltése nyilvános IP | dm-pip | Válassza ki az előző lépésekben létrehozott nyilvános IP-betöltési ip-címet.
    | | | |

1. A fürt létrehozásához válassza a **Véleményezés + létrehozás** lehetőséget.
1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

Az Azure Data Explorer-fürt virtuális hálózatra való üzembe helyezéséhez használja az [Azure Data Explorer-fürt központi telepítését a VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-sablonba.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Data Explorer telepítése a virtuális hálózatba](vnet-create-cluster-portal.md)