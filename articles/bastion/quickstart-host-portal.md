---
title: 'Gyors útmutató: megerősített gazdagép létrehozása virtuális gépről és magánhálózati IP-cím használatával történő összekapcsolás'
titleSuffix: Azure Bastion
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Bastion-gazdagépet egy virtuális gépről, és hogyan csatlakozhat biztonságosan egy magánhálózati IP-cím használatával.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019052"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Gyors útmutató: Kapcsolódás virtuális géphez magánhálózati IP-cím és Azure-alapú megerősített kapcsolat használatával

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy virtuális géphez a böngészőben az Azure Bastion és a Azure Portal használatával. A Azure Portal az Azure-beli virtuális gépről üzembe helyezheti a virtuális hálózatát. A megerősített telepítés után a magánhálózati IP-címmel csatlakozhat a virtuális géphez a Azure Portal használatával. A virtuális gépe nem igényel nyilvános IP-címet vagy speciális szoftvert. Az egyik előnye, hogy a VNet közvetlenül a virtuális gépről hozzon létre egy megerősített gazdagépet, hogy a beállítások közül sok előre ki van töltve.

A szolgáltatás üzembe helyezését követően az RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. További információ az Azure Bastion-ről: [Mi az az Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a><a name="prereq"></a>Előfeltételek

* Egy virtuális hálózat.
* Egy Windows rendszerű virtuális gép a virtuális hálózaton.
* A következő szükséges szerepkörök:
  * Olvasó szerepkör a virtuális gépen.
  * Olvasó szerepkör a hálózati adapteren a virtuális gép magánhálózati IP-címével.

* Portok: a virtuális géphez való csatlakozáshoz a következő portokat kell megnyitni a virtuális gépen:
  * Bejövő portok: RDP (3389)

### <a name="example-values"></a>Példaértékek

|**Név** | **Érték** |
| --- | --- |
| Név |  TestVNet1-Bastion |
| Virtuális hálózat |  TestVNet1 (a virtuális gép alapján) |
| + Alhálózat neve | AzureBastionSubnet |
| AzureBastionSubnet-címek |  10.1.254.0/27 |
| Nyilvános IP-cím |  Új létrehozása |
| Nyilvános IP-cím | VNet1BastionPIP  |
| Nyilvános IP-cím SKU |  Standard  |
| Hozzárendelés  | Statikus |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Megerősített gazdagép létrehozása

Amikor egy meglévő virtuális gép használatával hoz létre egy megerősített gazdagépet a Azure Portalban, a különböző beállítások automatikusan a virtuális géphez és/vagy a virtuális hálózathoz fognak igazodni.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Nyissa meg a virtuális gépet, majd válassza a **Kapcsolódás**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="virtuális gép beállításai" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. A legördülő listából válassza a **Bastion**lehetőséget.
1. A **TestVM | A kapcsolat lapon**válassza a **megerősített használata**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="virtuális gép beállításai" border="false":::

1. A **megerősített** lapon töltse ki a következő beállítások mezőket:

   * **Név**: nevezze el a megerősített gazdagépet.
   * **Alhálózat**: a virtuális hálózaton belüli alhálózat, amelyre a megerősített erőforrást telepíteni fogja. Az alhálózatot a **AzureBastionSubnet**névvel kell létrehozni. A név lehetővé teszi, hogy az Azure tudja, melyik alhálózaton helyezi üzembe a megerősített erőforrást. Ez nem más, mint az átjáró alhálózata. Használjon legalább/27 vagy nagyobb (/27,/26,/25 stb.) alhálózatot.
   
      * Válassza az **alhálózat konfigurációjának kezelése**lehetőséget.
      * Válassza ki a **AzureBastionSubnet**.
      * Ha szükséges, módosítsa a címtartományt CIDR-jelöléssel. Például: 10.1.254.0/27.
      * Ne módosítsa a többi beállítást. Válassza az **OK** gombot az alhálózat változásainak elfogadásához és mentéséhez, vagy válassza az **x** lehetőséget az oldal tetején, ha nem kíván módosításokat végezni.
1. A böngészőben a Vissza gombra kattintva lépjen vissza a **megerősített** oldalra, és folytassa az értékek megadását.
   * **Nyilvános IP-cím neve**: a nyilvános IP-cím erőforrásának neve.
   * **Nyilvános IP-cím**: Ez annak a megerősített erőforrásnak a nyilvános IP-címe, AMELYEN az RDP/SSH elérhető lesz (az 443-as porton keresztül). Hozzon létre egy új nyilvános IP-címet.
1. Válassza a **Létrehozás** lehetőséget a megerősített gazdagép létrehozásához. Az Azure ellenőrzi a beállításokat, majd létrehozza a gazdagépet. A gazdagép és az erőforrásai 5 percet vesznek igénybe a létrehozás és a telepítés során.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="virtuális gép beállításai":::

## <a name="connect"></a><a name="connect"></a>Kapcsolódás

Miután a megerősített állapotot telepítette a virtuális hálózatra, a képernyő a kapcsolódás lapra változik.

1. Írja be a virtuális gép felhasználónevét és jelszavát. Ezután válassza a **kapcsolat**lehetőséget.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="virtuális gép beállításai":::
1. Az ehhez a virtuális géphez a Bastion-en keresztül létesített RDP-kapcsolat közvetlenül a Azure Portal (HTML5-n keresztül) lesz megnyitva a 443-es port és a megerősített szolgáltatás használatával.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="virtuális gép beállításai":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a virtuális hálózat és a virtuális gépek használatával, törölje az erőforráscsoportot és a benne található összes erőforrást:

1. Adja meg az erőforráscsoport nevét a portál tetején található **keresőmezőbe** , és válassza ki a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Adja meg az erőforráscsoport **nevét, írja be az erőforráscsoport nevét** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy megerősített gazdagépet hozott létre a virtuális hálózathoz, majd biztonságosan kapcsolódott egy virtuális géphez a megerősített gazdagépen keresztül. Ezután folytassa a következő lépéssel, ha egy virtuálisgép-méretezési csoporthoz szeretne csatlakozni.

> [!div class="nextstepaction"]
> [Kapcsolódás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával](bastion-connect-vm-scale-set.md)
