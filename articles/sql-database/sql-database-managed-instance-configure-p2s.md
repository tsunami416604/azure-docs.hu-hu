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
ms.date: 01/17/2019
ms.openlocfilehash: 9133f7f4dde080700b2b11a4c09df6d0610869f6
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388038"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>Gyors útmutató: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni

Ez a rövid útmutató azt ismerteti, hogyan csatlakozhat egy Azure SQL Database felügyelt példánya a [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) egy pont – hely kapcsolaton keresztül egy helyszíni ügyfélszámítógépről. További információk a pont – hely kapcsolatokról: [tudnivalók pont – hely VPN](../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- A létrehozott erőforrásokat használja [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) kiindulási pontként.
- Szükséges a PowerShell 5.1-es és az Azure PowerShell 5.4.2 vagy újabb a helyi ügyfélszámítógépen. Ha szükséges, tekintse meg a következő útmutatót: [az Azure PowerShell-modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0#install-the-azure-powershell-module).
- A legújabb verziója szükséges [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) a helyi ügyfélszámítógépen.

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>A felügyelt példány virtuális hálózat VPN-átjáró csatlakoztatása

1. A helyszíni ügyfél-számítógépen nyissa meg a Powershellt.
2. Másolja ezt a PowerShell-parancsfájlt. Ez a szkript csatolja a felügyelt példány virtuális hálózatra, amelyhez a létrehozott VPN-átjáró a [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) rövid. A parancsfájl a következő műveleteket hajtja végre:

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

3. A PowerShell-ablakban illessze be a parancsfájlt, és adja meg a szükséges paramétereket. A tartozó értékeket `<subscriptionId>`, `<resourceGroup>`, és `<virtualNetworkName>` során használt azokat meg kell egyeznie a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid. Az érték `<certificateNamePrefix>` egy tetszés szerinti karakterlánc lehet.

4. Hajtsa végre a PowerShell-parancsfájlt.

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>A felügyelt példány VPN-kapcsolat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Nyissa meg az erőforráscsoport, amelyben létrehozta a virtuális hálózati átjárót, és nyissa meg a virtuális hálózati átjáró erőforrás.
3. Válassza ki **pont – hely konfiguráció** majd **VPN-ügyfél letöltése**.

    ![VPN-ügyfél letöltése](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. A helyi ügyfélszámítógépen csomagolja ki a fájlokat a zip-fájlt, és nyissa meg a kibontott mappát.
5. Nyissa meg a WindowsAmd64 mappát, és nyissa meg a **VpnClientSetupAmd64.exe** fájlt.
6. Ha megjelenik egy **Windows a számítógép védett** üzenet, válassza **további információ** majd **Futtatás mindenképpen**.

    ![VPN-ügyfél telepítése](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. Válassza ki **Igen** folytatja a felhasználói fiókok felügyelete párbeszédpanelen.
8. Jelölje ki a virtuális hálózat hivatkozó párbeszédpanel **Igen** a VPN-ügyfél telepítéséhez.

## <a name="connect-to-the-vpn-connection"></a>Csatlakozás a VPN-kapcsolat

1. Ugrás a VPN-kapcsolatok a helyszíni ügyfél-számítógépen, és válassza ki a felügyelt példány, a virtuális hálózatok közötti kapcsolatot létesíteni a virtuális hálózat. Az alábbi ábrán a virtuális hálózat neve **MyNewVNet**.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. Kattintson a **Csatlakozás** gombra.
3. A párbeszédpanelen válassza ki a **Connect**.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. Amikor a rendszer kéri, hogy a Csatlakozáskezelő az útvonaltábla frissítése, válassza a megemelt jogosultsági szint szükséges **Folytatás**.
5. Válassza ki **Igen** folytatja a felhasználói fiókok felügyelete párbeszédpanelen.

   A felügyelt példány virtuális hálózathoz létrehozott egy VPN-kapcsolat.

    ![VPN-kapcsolat](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  


## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Csatlakozhat a felügyelt példányhoz az SSMS használatával

1. A helyi ügyfélszámítógépen nyissa meg az SQL Server Management Studio (SSMS).
2. Az a **kapcsolódás a kiszolgálóhoz** párbeszédpanelen adja meg a teljes **állomásnév** a felügyelt példány a **kiszolgálónév** mezőbe. 
1. Válassza ki **SQL Server-hitelesítés**, és adja meg a felhasználónevét és jelszavát, majd válassza ki **Connect**.

    ![ssms connect](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

A csatlakozás után megtekintheti a rendszer- és felhasználói adatbázisokat az adatbázis-csomópont. Megtekintheti a különféle objektumokat a Security, Server Objects, replikációs, felügyeleti, SQL Server Agent és XEvent Profiler csomópontokon.

## <a name="next-steps"></a>További lépések

- A rövid útmutató, amely egy Azure virtuális gépen a kapcsolódás, lásd: [pont – hely kapcsolat konfigurálása](sql-database-managed-instance-configure-p2s.md).
- Az alkalmazások csatlakozási lehetőségeinek áttekintéséért lásd: [Alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Meglévő SQL Server-adatbázis visszaállítása helyi egy felügyelt példányra, használhatja a [Azure Database Migration Service (DMS) az áttelepítéshez](../dms/tutorial-sql-server-to-managed-instance.md) vagy a [T-SQL RESTORE parancsot](sql-database-managed-instance-get-started-restore.md) visszaállítása egy adatbázis biztonsági másolatát.
