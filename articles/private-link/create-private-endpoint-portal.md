---
title: Manage Private Endpoints in Azure
description: Learn how to create a Private Endpoint using the Azure portal
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 50e358684ebef82e96c4c21e6139434f8581595a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224787"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Create a Private Endpoint using Azure portal

A Private Endpoint is the fundamental building block for private link in Azure. It enables Azure resources, like Virtual Machines (VMs), to communicate privately with private link resources. In this Quickstart, you will learn how to create a VM on an Azure Virtual Network, a  SQL Database Server with an Azure private endpoint using the Azure Portal. Then, you can securely access the SQL Database Server from the VM.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


> [!NOTE]
> Private endpoint(s) are not permitted in conjunction with service endpoints in the same subnet!

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
In this section, you will create virtual network and the subnet to host the VM that is used to access your Private Link resource (a SQL server in Azure in this example).

### <a name="create-the-virtual-network"></a>Create the virtual network


In this section, you will create a Virtual Network and the subnet to host the VM that is used to access your Private Link resource.

1. On the upper-left side of the screen, select **Create a resource** > **Networking** > **Virtual network**.
1. In **Create virtual network**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Név | Enter *MyVirtualNetwork*. |
    | Címtér | Enter *10.1.0.0/16*. |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Select **Create new**, enter *myResourceGroup*, then select **OK**. |
    | Földrajzi egység | Select **WestCentralUS**.|
    | Subnet - Name | Enter *mySubnet*. |
    | Alhálózat – címtartomány | Enter *10.1.0.0/24*. |
    |||
1. Leave the rest as default and select **Create**.


### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual Machine**.

1. In **Create a virtual machine - Basics**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Select **myResourceGroup**. You created this in the previous section.  |
    | **INSTANCE DETAILS** |  |
    | Virtuális gép neve | Enter *myVm*. |
    | Region (Régió) | Select **WestCentralUS**. |
    | Availability options | Leave the default **No infrastructure redundancy required**. |
    | Lemezkép | Select **Windows Server 2019 Datacenter**. |
    | Méret | Leave the default **Standard DS1 v2**. |
    | **ADMINISTRATOR ACCOUNT** |  |
    | Felhasználónév | Enter a username of your choosing. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reenter password. |
    | **INBOUND PORT RULES** |  |
    | Public inbound ports | Leave the default **None**. |
    | **SAVE MONEY** |  |
    | Already have a Windows license? | Leave the default **No**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. In **Create a virtual machine - Networking**, select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Virtuális hálózat | Leave the default **MyVirtualNetwork**.  |
    | Címtér | Leave the default **10.1.0.0/24**.|
    | Alhálózat | Leave the default **mySubnet (10.1.0.0/24)** .|
    | Nyilvános IP-cím | Leave the default **(new) myVm-ip**. |
    | Public inbound ports | Select **Allow selected ports**. |
    | Select inbound ports | Select **HTTP** and **RDP**.|
    |||


1. Válassza az **Áttekintés + létrehozás** lehetőséget. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## <a name="create-a-sql-database-server"></a>Create a SQL database server
In this section, you will create a SQL database server in Azure. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL database**.

1. In **Create SQL database - Basics**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **Database details** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Adatbázis neve  | Enter *mydatabase*. If this name is taken, create a unique name. |
    |||
5. In **Server**, select **Create new**. 
6. In **New server**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    |Kiszolgálónév  | Enter *myserver*. If this name is taken, create a unique name.|
    | Kiszolgáló-rendszergazdai bejelentkezés| Enter an administrator name of your choosing. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. The password must be at least 8 characters long and meet the defined requirements. |
    | Földrajzi egység | Select an Azure region where you want to want your SQL Server to reside. |
    
7. Kattintson az **OK** gombra. 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. You're taken to the **Review + create** page where Azure validates your configuration. 
9. When you see the Validation passed message, select **Create**. 
10. When you see the Validation passed message, select Create. 

## <a name="create-a-private-endpoint"></a>Create a private endpoint

In this section, you will create a SQL server and add a private endpoint to it. 

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Networking** > **Private Link Center (Preview)** .
2. In **Private Link Center - Overview**, on the option to **Build a private connection to a service**, select **Start**.
1. In **Create a private endpoint (Preview) - Basics**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | **Project details** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Select **myResourceGroup**. You created this in the previous section.|
    | **INSTANCE DETAILS** |  |
    | Név | Enter * myPrivateEndpoint*. If this name is taken, create a unique name. |
    |Region (Régió)|Select **WestCentralUS**.|
    |||
5. Select **Next: Resource**.
6. In **Create a private endpoint - Resource**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    |Kapcsolati módszer  | Select connect to an Azure resource in my directory.|
    | Előfizetés| Válassza ki előfizetését. |
    | Erőforrás típusa | Select **Microsoft.Sql/servers**. |
    | Erőforrás |Select *myServer*|
    |Target sub-resource |Select *sqlServer*|
    |||
7. Select **Next: Configuration**.
8. In **Create a private endpoint (Preview) - Configuration**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    |**NETWORKING**| |
    | Virtuális hálózat| Select *MyVirtualNetwork*. |
    | Alhálózat | Select *mySubnet*. |
    |**PRIVATE DNS INTEGRATION**||
    |Integrate with private DNS zone |Válassza az **Igen** lehetőséget. |
    |Private DNS Zone |Select *(New)privatelink.database.windows.net* |
    |||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. You're taken to the **Review + create** page where Azure validates your configuration. 
2. When you see the **Validation passed** message, select **Create**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Connect to a VM using Remote Desktop (RDP)


After you've created **myVm*, connect to it from the internet as follows: 

1. In the portal's search bar, enter *myVm*.

1. Kattintson a **Csatlakozás** gombra. After selecting the **Connect** button, **Connect to virtual machine** opens.

1. Select **Download RDP File**. Azure creates a Remote Desktop Protocol ( *.rdp*) file and downloads it to your computer.

1. Open the downloaded.rdp* file.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Enter the username and password you specified when creating the VM.

        > [!NOTE]
        > You may need to select **More choices** > **Use a different account**, to specify the credentials you entered when you created the VM.

1. Kattintson az **OK** gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. If you receive a certificate warning, select **Yes** or **Continue**.

1. Once the VM desktop appears, minimize it to go back to your local desktop.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Access the SQL database server privately from the VM

1. In the Remote Desktop of *myVM*, open PowerShell.

2. Enter `nslookup myserver.database.windows.net`. 

    You'll receive a message similar to this:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Kiszolgáló típusa| Válassza a **Database Engine** (Adatbázismotor) lehetőséget.|
    | Kiszolgálónév| Select *myserver.database.windows.net* |
    | Felhasználónév | Enter username as username@servername which is provided during the SQL server creation. |
    |Jelszó |Enter a password provided during the SQL server creation. |
    |Remember password|Válassza az **Igen** lehetőséget.|
    |||
1. Kattintson a **Csatlakozás** gombra.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## <a name="next-steps"></a>Következő lépések

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

