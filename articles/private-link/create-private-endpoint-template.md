---
title: Azure Private Endpoint ARM-sablon
description: További információ az Azure Private linkről
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: af00119f1da3368b8592e020eee1ebb2a39a8501
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669953"
---
# <a name="quickstart-create-a-private-endpoint---resource-manager-template"></a>Rövid útmutató: privát végpont létrehozása – Resource Manager-sablon

Ebben a rövid útmutatóban egy Resource Manager-sablonnal hozhat létre privát végpontot.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](create-private-endpoint-portal.md), a [Azure PowerShell](create-private-endpoint-powershell.md)vagy az [Azure CLI](create-private-endpoint-cli.md)használatával is elvégezheti.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-private-endpoint"></a>Privát végpont létrehozása

Ez a sablon létrehoz egy magánhálózati végpontot az Azure SQL Serverhez.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-private-endpoint-sql/)származik.

:::code language="json" source="~/quickstart-templates/101-private-endpoint-sql/azuredeploy.json" range="001-295" highlight="131-156":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. SQL/Servers**](/azure/templates/microsoft.sql/servers) : az Azure SQL Server és a mintaadatbázis
- [**Microsoft. SQL/kiszolgálók/adatbázisok**](/azure/templates/microsoft.sql/servers/databases) : mintaadatbázis
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks) : Virtual Network, ahol a magánhálózati végpont üzembe lett helyezve
- [**Microsoft. Network/privateEndpoints**](/azure/templates/microsoft.network/privateendpoints) : magánhálózati végpont az Azure SQL Serverhez való hozzáféréshez
- [**Microsoft. Network/privateDnsZones**](/azure/templates/microsoft.network/privatednszones) : a magánhálózati végpont IP-címének feloldásához használatos
- [**Microsoft. Network/privateDnsZones/virtualNetworkLinks**](/azure/templates/microsoft.network/privatednszones/virtualnetworklinks)
- [**Microsoft. Network/privateEndpoints/privateDnsZoneGroups**](/azure/templates/microsoft.network/privateendpoints/privateDnsZoneGroups) : privát végpont hozzárendelése magánhálózati DNS-zónához
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicIpAddresses) : nyilvános IP-cím a virtuális gép eléréséhez
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : a virtuális gép hálózati adaptere
- [**Microsoft. számítás/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : virtuális gép, amely a privát végponttal rendelkező magánhálózati kapcsolatokat az Azure SQL Serverrel teszteli

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehozza a privát végpontot, az Azure SQL Servert, a hálózati infrastruktúrát és az érvényesíteni kívánt virtuális gépeket.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-sql%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot,
3. Írja be az SQL-rendszergazda felhasználónevét és jelszavát
4. Írja be a virtuális gép rendszergazdai felhasználónevét és jelszavát.
5. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget. Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

> [!NOTE]
> Az ARM-sablon egyedi nevet hoz létre a (z) {<b>UniqueID}</b> virtuális gép myVm és az Azure SQL Server SQLServer<b>{UniqueID}</b> erőforrásához, cserélje le a <b>{UniqueID}</b> értéket a generált értékre.

### <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Kapcsolódjon a _(z) {UniqueID}_ virtuális gép myVm az internetről a következőképpen:

1. A portál keresősáv mezőjébe írja be a _myVm {UniqueID}_ értéket.

2. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

3. Válassza az **RDP-fájl letöltése**lehetőséget. Az Azure létrehoz egy RDP protokoll (_. rdp_) fájlt, és letölti a számítógépre.

4. Nyissa meg a letöltött. rdp \* fájlt.

   a. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

   b. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

      > [!NOTE]
      > Előfordulhat, hogy a **More choices**  >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania**egy másik fiók használatával**.

5. Válassza az **OK** lehetőséget.

6. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

7. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

### <a name="access-sql-database-server-privately-from-the-vm"></a>SQL Database kiszolgáló magánhálózati elérése a virtuális gépről

Ebben a szakaszban a virtuális gépről a magánhálózati végpont használatával fog csatlakozni a SQL Database-kiszolgálóhoz.

1.  A _myVM ({UniqueID}_) távoli asztal nyissa meg a PowerShellt.
2.  Adja meg az nslookup SQLServer {UniqueID}. database. Windows. net értéket, amely a   következőhöz hasonló üzenetet küld:

    ```
      Server:  UnKnown
      Address:  168.63.129.16
      Non-authoritative answer:
      Name:    sqlserver.privatelink.database.windows.net
      Address:  10.0.0.5
      Aliases:  sqlserver.database.windows.net
    ```

3.  SQL Server Management Studio telepítése
4.  A Kapcsolódás a kiszolgálóhoz mezőben adja meg vagy válassza ki ezt az információt: kiszolgáló típusa: adatbázismotor kiválasztása.
    Kiszolgáló neve: válassza a SQLServer {UniqueID}. database. Windows. net Felhasználónév: adjon meg egy, a létrehozás során megadott felhasználónevet.
    Password (jelszó): adja meg a létrehozás során megadott jelszót.
    Jelszó megjegyzése: válassza az Igen lehetőséget.

5.  Kattintson a **Csatlakozás** gombra.
6.  A bal oldali menüben lévő **adatbázisok** tallózása.
7.  Opcionálisan Adatok létrehozása vagy lekérdezése a _Sample-db-_ ből
8.  A _(myVm) {UniqueID}_ távoli asztali kapcsolatának lezárása.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége a privát végponttal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a magánhálózati végpontot és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a következő `Remove-AzResourceGroup` parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Private linkről](private-link-overview.md)
