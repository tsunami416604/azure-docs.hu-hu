---
title: 'P2S konfigurálása – Azure SQL Database felügyelt példány '
description: Kapcsolódjon Azure SQL Database felügyelt példányhoz a SQL Server Management Studio használatával pont – hely kapcsolattal egy helyszíni ügyfélszámítógépről.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3b3a0ce28c4a936e185ac5f07ba3810c93f4c866
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689415"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Gyors útmutató: pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszíni környezetből

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure SQL Database felügyelt példányhoz [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával egy helyszíni ügyfélszámítógépről pont – hely kapcsolaton keresztül. További információ a pont – hely kapcsolatokról: [Tudnivalók a pont – hely típusú VPN-ről](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- A létrehozott erőforrásokat használja kiindulási pontként a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) .
- A PowerShell 5,1 és AZ AZ PowerShell 1.4.0 vagy újabb verzió szükséges a helyszíni ügyfélszámítógépen. Ha szükséges, tekintse meg a [Azure PowerShell modul telepítésére](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)vonatkozó utasításokat.
- A [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) legújabb verzióját igényli a helyszíni ügyfélszámítógépen.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>VPN-átjáró csatolása a felügyelt példány virtuális hálózathoz

1. Nyissa meg a PowerShellt a helyszíni ügyfélszámítógépen.

2. Másolja ezt a PowerShell-szkriptet. Ez a parancsfájl egy VPN Gatewayt csatol a felügyelt példány virtuális hálózathoz, amelyet a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatójában hozott létre. Ez a szkript a Azure PowerShell az modult használja, és Windows-vagy Linux-alapú gazdagépek esetén a következő műveleteket hajtja végre:

   - Tanúsítványok létrehozása és telepítése az ügyfélszámítógépen
   - Kiszámítja a jövőbeli VPN Gateway alhálózat IP-tartományát
   - Létrehozza a GatewaySubnet
   - Központilag telepíti a VPN Gateway VPN-alhálózathoz csatolt Azure Resource Manager sablont

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

3. Illessze be a szkriptet a PowerShell-ablakba, és adja meg a szükséges paramétereket. `<subscriptionId>`, `<resourceGroup>`és `<virtualNetworkName>` értékének meg kell egyeznie a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatójában használt értékekkel. A `<certificateNamePrefix>` értéke lehet tetszőleges sztring.

4. Futtassa a PowerShell-szkriptet.

> [!IMPORTANT]
> Ne folytassa, amíg a PowerShell-parancsfájl be nem fejeződik.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>VPN-kapcsolat létrehozása a felügyelt példányhoz

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Nyissa meg azt az erőforráscsoportot, amelyben létrehozta a virtuális hálózati átjárót, majd nyissa meg a virtuális hálózati átjáró erőforrását.
3. Válassza a **pont – hely konfiguráció** lehetőséget, majd válassza a **VPN-ügyfél letöltése**lehetőséget.

    ![VPN-ügyfél letöltése](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. A helyszíni ügyfélszámítógépen bontsa ki a fájlokat a zip-fájlból, majd nyissa meg a mappát a kibontott fájlokkal.
5. Nyissa meg a "**WindowsAmd64** " mappát, és nyissa meg a **VpnClientSetupAmd64. exe** fájlt.
6. Ha a Windows által **védett számítógép** -üzenet jelenik meg, kattintson a **További információ** , majd a **Futtatás amúgy**elemre.

    ![VPN-ügyfél telepítése](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. A felhasználói fiókok felügyelete párbeszédpanelen kattintson az **Igen** gombra a folytatáshoz.
8. A virtuális hálózatra hivatkozó párbeszédpanelen válassza az **Igen** lehetőséget a VPN-ügyfél telepítéséhez a virtuális hálózathoz.

## <a name="connect-to-the-vpn-connection"></a>Csatlakozás a VPN-kapcsolathoz

1. Nyissa meg a **VPN** -t a **hálózati & interneten** a helyi ügyfélszámítógépen, és válassza ki a felügyelt példány virtuális hálózatát, hogy kapcsolatot létesítsen ehhez a VNet. A következő képen a VNet neve **MyNewVNet**.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Kattintson a **Csatlakozás** gombra.
3. A párbeszédpanelen válassza a **kapcsolat**lehetőséget.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Amikor a rendszer arra kéri, hogy a Csatlakozáskezelő emelt szintű jogosultságot igényel az útválasztási táblázat frissítéséhez, válassza a **Folytatás**lehetőséget.
5. A folytatáshoz a felhasználói fiókok felügyelete párbeszédpanelen válassza az **Igen** lehetőséget.

   Létrehozott egy VPN-kapcsolatát a felügyelt példány VNet.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>A SSMS használata a felügyelt példányhoz való kapcsolódáshoz

1. A helyszíni ügyfélszámítógépen nyissa meg SQL Server Management Studio (SSMS).
2. A **Kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a felügyelt példány teljes **állomásnevét** a **kiszolgáló neve** mezőben.
3. Válassza ki **SQL Server hitelesítést**, adja meg felhasználónevét és jelszavát, majd válassza a **kapcsolat**lehetőséget.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozási lehetőség után megtekintheti a rendszer és a felhasználói adatbázisokat az adatbázisok csomópontban. A biztonság, a kiszolgálói objektumok, a replikálás, a felügyelet, a SQL Server Agent és a XEvent Profiler-csomópontok különböző objektumait is megtekintheti.

## <a name="next-steps"></a>További lépések

- Az Azure-beli virtuális gépekről történő kapcsolódást bemutató rövid útmutató: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Ha egy meglévő SQL Server adatbázist szeretne visszaállítani a helyszíni rendszerből egy felügyelt példányra, az [áttelepítéshez használhatja a Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) vagy a [T-SQL Restore parancsot](sql-database-managed-instance-get-started-restore.md) az adatbázis biztonságimásolat-fájljából való visszaállításhoz.
