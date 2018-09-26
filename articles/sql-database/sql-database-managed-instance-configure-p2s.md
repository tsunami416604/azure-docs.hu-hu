---
title: P2S - konfigurálása az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Csatlakozás egy Azure SQL Database felügyelt példányába történő SQL Server Management studióval egy a helyszíni ügyfélszámítógépről pont – hely kapcsolat használatával.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova, jovanpop
manager: craigg
ms.date: 09/06/2018
ms.openlocfilehash: fc51d7191deb8242075b28fbb42adc2ff9ae2739
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163001"
---
# <a name="configure-a-point-to-site-connection-to-connect-to-an-azure-sql-database-managed-instance-from-on-premises-computer"></a>Csatlakozás egy Azure SQL Database felügyelt példánya a helyi számítógép pont – hely kapcsolat konfigurálása

Ez a rövid útmutató azt ismerteti, hogyan csatlakozhat egy Azure SQL Database felügyelt példánya a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) egy pont – hely kapcsolaton keresztül egy helyszíni ügyfélszámítógépről. További információk a pont – hely kapcsolatokról: [tudnivalók pont – hely VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:
- Használja a kiindulási pont az ebben a rövid útmutatóban létrehozott erőforrásokat: [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md).
- Szükséges a PowerShell 5.1-es és az Azure PowerShell 5.4.2 vagy újabb a helyi ügyfélszámítógépen.
- A legújabb verziója szükséges [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) a helyi ügyfélszámítógépen

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>A felügyelt példány virtuális hálózat VPN-átjáró csatlakoztatása

1. A helyszíni ügyfél-számítógépen nyissa meg a Powershellt.
2. Másolja és illessze be a PowerShell-szkript. Ez a szkript csatolja a felügyelt példány virtuális hálózatra, amelyhez a létrehozott VPN-átjáró a [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) rövid. Ez a szkript a következő három lépés hajtja végre:
  - Létrehozza és tanúsítványok telepítése ügyfélszámítógépre
  - Kiszámítja a jövőbeli VPN-átjáró alhálózati IP-címtartomány
  - Az átjáró-alhálózat létrehozása
  - Üzembe helyez az Azure Resource Manager-sablon, amely a VPN-átjáró VPN-alhálózathoz csatolja

   ```powershell
   $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

   $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

   Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase 
   ```

3. Adja meg a kért paramétereket a PowerShell-parancsfájlt. A tartozó értékeket `<subscriptionId>`, `<resourceGroup>` és `<virtualNetworkName>` egyeznie kell a használt azokat [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid. Az érték `<certificateNamePrefix>` egy tetszés szerinti karakterlánc lehet.

4. Hajtsa végre a PowerShell-parancsfájlt.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>A felügyelt példány VPN-kapcsolat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az erőforráscsoport, amelyben létrehozta a virtuális hálózati átjárót, és nyissa meg a virtuális hálózati átjáró erőforrás.

    ![virtuális hálózati átjáró erőforrás](./media/sql-database-managed-instance-configure-p2s/vnet-gateway.png)  

3. Kattintson a **pont – hely konfiguráció** majd **VPN-ügyfél letöltése**.

    ![VPN-ügyfél letöltése](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. Csomagolja ki a fájlokat a zip-fájlt, és nyissa meg a kibontott mappát. 
5. Havigate WindowsAmd64 mappába és nyissa meg a **VpnClientSetupAmd64.exe** fájlt.
6. Ha megjelenik egy **Windows védeni a számítógépen** üzenet, kattintson **további információ** majd **Futtatás mindenképpen**.

    ![VPN-ügyfél telepítése](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Kattintson a **Igen** folytatja a felhasználói fiókok felügyelete párbeszédpanelen.
8. A MyNewVNet párbeszédpanelen kattintson a **Igen** MyNewVNet egy Vpn-ügyfél telepítéséhez.

## <a name="connect-to-the-vpn-connection"></a>Csatlakozás a VPN-kapcsolatot.

1. Ugrás a VPN-kapcsolatok az ügyfélszámítógépen, és kattintson a **MyNewVNet** egy a virtuális hálózatok közötti kapcsolatot létesíteni.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Kattintson a **Connect** (Csatlakozás) gombra.
3. A MyNewVNet párbeszédpanelen kattintson a **Connect**.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Amikor a rendszer kéri, hogy a Csatlakozáskezelő igények megemelt jogosultsági szint az útvonaltábla frissítése, kattintson a **Folytatás**.
5. Kattintson a **Igen** folytatja a felhasználói fiókok felügyelete párbeszédpanelen.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

   A felügyelt példány virtuális hálózathoz egy VPN-kapcsolatot hozott létre.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Csatlakozhat a felügyelt példányhoz az SSMS használatával

1. A helyi ügyfélszámítógépen nyissa meg az SQL Server Management Studio (SSMS).
 
2. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a teljes **állomásnév** a felügyelt példány az **kiszolgálónév** jelölje ki **SQL Server Hitelesítési**, adja meg a felhasználónevét és jelszavát, és kattintson **Connect**.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat a Databases (Adatbázisok) csomóponton, valamint különféle objektumokat a Security (Biztonság), Server Objects (Kiszolgálóobjektumok), Replication (Replikáció), Management (Felügyelet), SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

- A rövid útmutató, amely egy Azure virtuális gépen a kapcsolódás, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md)
- Az alkalmazások csatlakozási lehetőségek áttekintését lásd: [összekötheti saját alkalmazásait a felügyelt példány](sql-database-managed-instance-connect-app.md).
- Meglévő SQL Server-adatbázis visszaállítása helyi egy felügyelt példányra, használhatja a [Azure Database Migration Service (DMS) az áttelepítéshez](../dms/tutorial-sql-server-to-managed-instance.md) vissza egy adatbázis biztonságimásolat-fájlt vagy a [T-SQL RESTORE a parancs](sql-database-managed-instance-get-started-restore.md) vissza egy adatbázis biztonságimásolat-fájlt.
