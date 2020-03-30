---
title: P2S – Felügyelt példány konfigurálása
description: Csatlakozzon egy Azure SQL Database felügyelt példányaz SQL Server Management Studio segítségével egy pont-hely kapcsolat egy helyszíni ügyfélszámítógép.
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
ms.openlocfilehash: 30b2ba92174996ea2bae34e7553a3258d8ebee27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268885"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Rövid útmutató: Pont-hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszíni környezetből

Ez a rövid útmutató bemutatja, hogyan csatlakozhat egy Azure SQL Database Felügyelt példány hoz egy [helyszíni](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) ügyfélszámítógépről egy helyszíni ügyfélszámítógépről egy pont-hely kapcsolaton keresztül. A helyek közötti kapcsolatokról a [Pont–hely VPN – című témakörben](../vpn-gateway/point-to-site-about.md) talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- Kiindulópontként a [Felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) létrehozott erőforrásokat használja kiindulási pontként.
- A helyszíni ügyfélszámítógépen PowerShell 5.1-es és AZ PowerShell 1.4.0-s vagy újabb verziószükséges. Ha szükséges, tekintse meg [az Azure PowerShell-modul telepítésével](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)kapcsolatos utasításokat.
- Az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) legújabb verzióját igényli a helyszíni ügyfélszámítógépen.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>VPN-átjáró csatolása a felügyelt példány virtuális hálózatához

1. Nyissa meg a PowerShellt a helyszíni ügyfélszámítógépen.

2. Másolja a PowerShell-parancsfájlt. Ez a parancsfájl vpn-átjárót csatol a [Felügyelt](sql-database-managed-instance-get-started.md) példány létrehozása rövid útmutatóban létrehozott felügyelt példány virtuális hálózatához. Ez a parancsfájl az Azure PowerShell Az modult használja, és a következőket fogja tenni Windows- vagy Linux-alapú gazdagépekesetén:

   - Tanúsítványok létrehozása és telepítése ügyfélszámítógépen
   - Kiszámítja a vpn-átjáró jövőbeli alhálózati IP-tartományát
   - Létrehozza a GatewaySubnet
   - Telepíti az Azure Resource Manager sablont, amely a VPN-átjárót csatolja a VPN-alhálózathoz

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

3. Illessze be a parancsfájlt a PowerShell-ablakba, és adja meg a szükséges paramétereket. A , `<subscriptionId>` `<resourceGroup>`és `<virtualNetworkName>` meg kell egyeznie a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatóhoz használt értékekkel. Az érték `<certificateNamePrefix>` lehet egy karakterlánc, amelyet ön választott.

4. Hajtsa végre a PowerShell-parancsfájlt.

> [!IMPORTANT]
> Ne folytassa, amíg a PowerShell-parancsfájl be nem fejeződik.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>VPN-kapcsolat létrehozása a felügyelt példányhoz

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Nyissa meg azt az erőforráscsoportot, amelyben létrehozta a virtuális hálózati átjárót, majd nyissa meg a virtuális hálózati átjáró erőforrást.
3. Válassza **a Pont-hely konfiguráció,** majd **a VPN-ügyfél letöltése**lehetőséget.

    ![VPN-ügyfél letöltése](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. A helyszíni ügyfélszámítógépen bontsa ki a fájlokat a zip fájlból, majd nyissa meg a kibontott fájlokat tartalmazó mappát.
5. Nyissa meg a '**WindowsAmd64** mappát és nyissa meg a **VpnClientSetupAmd64.exe** fájlt.
6. Ha a **Számítógép által védett Windows-üzenet** jelenik meg, kattintson a **További információ,** majd a **Futtatás gombra.**

    ![VPN-ügyfél telepítése](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. A Felhasználói fiókok felügyelete párbeszédpanelen kattintson az **Igen** gombra a folytatáshoz.
8. A virtuális hálózatra hivatkozó párbeszédpanelen válassza az **Igen** lehetőséget a virtuális hálózat VPN-ügyfélalkalmazásának telepítéséhez.

## <a name="connect-to-the-vpn-connection"></a>Csatlakozás a VPN-kapcsolathoz

1. Nyissa meg a **VPN-t** a **hálózati & internet en** a helyszíni ügyfélszámítógépen, és válassza ki a felügyelt példány virtuális hálózatát a virtuális hálózathoz való kapcsolat létrehozásához. Az alábbi képen a virtuális hálózat neve **MyNewVNet**.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Kattintson a **Csatlakozás** gombra.
3. A párbeszédpanelen válassza a **Csatlakozás**lehetőséget.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Amikor a rendszer azt kéri, hogy a Csatlakozáskezelőnek emelt szintű jogosultságra van szüksége az útvonaltábla frissítéséhez, válassza a **Folytatás gombot.**
5. A folytatáshoz válassza az **Igen** lehetőséget a Felhasználói fiókok felügyelete párbeszédpanelen.

   Vpn-kapcsolatot létesített a felügyelt példány virtuális hálózatával.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>A felügyelt példányhoz való csatlakozás hoz az SSMS használatával

1. A helyszíni ügyfélszámítógépen nyissa meg az SQL Server Management Studio (SSMS) alkalmazást.
2. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen írja be a felügyelt példány teljesen minősített **állomásnevét** a **Kiszolgáló neve** mezőbe.
3. Válassza **az SQL Server Hitelesítés**lehetőséget, adja meg felhasználónevét és jelszavát, majd válassza a **Csatlakozás**lehetőséget.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat az Adatbázisok csomópontban. A biztonság, a kiszolgálóobjektumok, a replikáció, a felügyelet, az SQL Server Agent és az XEvent Profiler csomópontok különböző objektumait is megtekintheti.

## <a name="next-steps"></a>További lépések

- Az Azure virtuális gépekről való csatlakozásról a [Pont-hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md)című rövid útmutatót című témakörben található.
- Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása egy felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Meglévő SQL Server-adatbázis visszaállítása a helyszíni felügyelt példányból, az [Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) segítségével az áttelepítéshez vagy a [T-SQL RESTORE paranccsal](sql-database-managed-instance-get-started-restore.md) visszaállíthatja az adatbázis biztonsági másolatfájlját.
