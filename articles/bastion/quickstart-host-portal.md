---
title: 'Gyors útmutató: Kapcsolódás virtuális géphez magánhálózati IP-cím használatával: Azure Bastion'
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet egy virtuális gépről, és hogyan csatlakozhat biztonságosan egy magánhálózati IP-cím használatával.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619253"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Gyors útmutató: Kapcsolódás virtuális géphez magánhálózati IP-cím és Azure-alapú megerősített kapcsolat használatával

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy virtuális géphez egy magánhálózati IP-cím használatával. Ha a-t a Bastion-n keresztül kapcsolódik, a virtuális gépeknek nincs szükségük nyilvános IP-címekre. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a-ben a virtuális gépen, a portálon keresztül üzembe helyezéshez. A szolgáltatás üzembe helyezését követően az RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető.

## <a name="prerequisites"></a><a name="prereq"></a>Előfeltételek

* Egy Azure-beli virtuális hálózat.
* Egy Azure-beli virtuális gép, amely a virtuális hálózaton található, a 3389-es porton keresztül.

### <a name="example-values"></a>Példaértékek

|**Név** | **Érték** |
| --- | --- |
| Name (Név) |  VNet1Bastion |
| Régió | eastus |
| Virtuális hálózat |  VNet1 |
| + Alhálózat neve | AzureBastionSubnet |
| AzureBastionSubnet-címek |  10.1.254.0/27 |
| Nyilvános IP-cím |  Új létrehozása |
| Nyilvános IP-cím | VNet1BastionPIP  |
| Nyilvános IP-cím SKU |  Standard  |
| Hozzárendelés  | Statikus |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion-gazdagép létrehozása

Amikor egy meglévő virtuális gép használatával hoz létre egy megerősített gazdagépet a portálon, a különböző beállítások automatikusan megegyeznek a virtuális géppel és/vagy a virtuális hálózattal.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Nyissa meg a virtuális gépet, majd kattintson a **Kapcsolódás**elemre.

   ![virtuális gép beállításai](./media/quickstart-host-portal/vm-settings.png)
1. A legördülő listából válassza a **Bastion**lehetőséget.
1. A kapcsolat lapon válassza a a **Bastion használata**lehetőséget.

   ![Megerősített](./media/quickstart-host-portal/select-bastion.png)

1. A megerősített lapon töltse ki a következő beállítások mezőket:

   * **Név**: a megerősített gazdagép neve
   * **Alhálózat**: a virtuális hálózaton belüli alhálózat, amelyre a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. A név lehetővé teszi, hogy az Azure tudja, melyik alhálózaton helyezi üzembe a megerősített erőforrást. Ez nem más, mint az átjáró alhálózata. Használjon legalább/27 vagy nagyobb (/27,/26,/25 stb.) alhálózatot.
   
      * Válassza az **alhálózat konfigurációjának kezelése**, majd a **+ alhálózat**lehetőséget.
      * Az alhálózat hozzáadása lapon írja be a következőt: **AzureBastionSubnet**.
      * A címtartomány meghatározása a CIDR-jelöléssel. Például: 10.1.254.0/27.
      * Az alhálózat létrehozásához kattintson **az OK gombra** . A lap tetején navigáljon vissza a kilépéshez a többi beállítás befejezéséhez.

         ![Navigáljon a megerősített beállítások menüponthoz](./media/quickstart-host-portal/navigate-bastion.png)
   * **Nyilvános IP-cím**: Ez annak a megerősített erőforrásnak a nyilvános IP-címe, AMELYEN az RDP/SSH elérhető lesz (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.
   * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
1. Az érvényesítési képernyőn kattintson a **Létrehozás**gombra. Várjon körülbelül 5 percet a megerősített erőforrás létrehozásához és üzembe helyezéséhez.

   ![megerősített gazdagép létrehozása](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Kapcsolódás

Miután a megerősített állapotot telepítette a virtuális hálózatra, a képernyő a kapcsolódás lapra változik.

1. Írja be a virtuális gép felhasználónevét és jelszavát. Ezután válassza a **kapcsolat**lehetőséget.

   ![csatlakozásra](./media/quickstart-host-portal/connect.png)
1. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   ![RDP-kapcsolat](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Adja meg a *TestRG1* a portál tetején található **keresőmezőbe** , és válassza a **TestRG1** lehetőséget a keresési eredmények közül.

2. Válassza az **Erőforráscsoport törlése** elemet.

3. Írja be a *TestRG1* **nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy megerősített gazdagépet hozott létre a virtuális hálózathoz, majd biztonságosan kapcsolódott egy virtuális géphez a megerősített gazdagépen keresztül.

* Ha többet szeretne megtudni az Azure Bastion-ról, olvassa el a [Bastion áttekintése](bastion-overview.md) és a [megerősített GYIK](bastion-faq.md)című témakört.
* Ha hálózati biztonsági csoportokat kíván használni az Azure megerősített alhálózattal, tekintse meg a következő témakört: a [NSG](bastion-nsg.md)használata.
* Az Azure Bastion-gazdagép beállításainak magyarázatait tartalmazó utasításokért tekintse meg az [oktatóanyagot](bastion-create-host-portal.md).
* A virtuálisgép-méretezési csoportokhoz való kapcsolódáshoz lásd: [Kapcsolódás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával](bastion-connect-vm-scale-set.md).