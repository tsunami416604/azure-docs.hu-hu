---
title: Privát IP-címek konfigurálása a virtuális gépekhez (klasszikus) - Azure PowerShell | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja a privát IP-címeket a virtuális gépek (Classic) PowerShell használatával.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9255ca3a2ed7e446c60a269deef61372955c25f4
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458505"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Privát IP-címek konfigurálása egy virtuális géphez (Klasszikus) a PowerShell használatával

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Az [Erőforrás-kezelő telepítési modelljében statikus magáncím is kezelhető.](virtual-networks-static-private-ip-arm-ps.md)

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Az alábbi PowerShell-parancsok minta egy már létrehozott egyszerű környezetet várnak. Ha a parancsokat úgy szeretné futtatni, ahogy azok ebben a dokumentumban megjelennek, először hozza létre a [Virtuális hálózat létrehozása](virtual-networks-create-vnet-classic-netcfg-ps.md)című dokumentumban leírt tesztkörnyezetet.

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Annak ellenőrzése, hogy van-e elérhető egy adott IP-cím?
Annak ellenőrzéséhez, hogy a *192.168.1.101* IP-cím elérhető-e egy *TestVNet*nevű virtuális hálózatban, futtassa a következő PowerShell-parancsot, és ellenőrizze az *IsAvailable*értékét:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Várt kimenet:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus privát IP-cím megadása virtuális gép létrehozásakor
Az alábbi PowerShell-parancsfájl létrehoz egy új felhőalapú szolgáltatást *TestService*néven, majd lekéri a lemezképet az Azure-ból, létrehoz egy *DNS01* nevű virtuális gép az új felhőszolgáltatásban a beolvasott lemezkép használatával, beállítja a virtuális gép *egy FrontEnd*nevű alhálózatban, és a *192.168.1.7-et* a virtuális gép statikus privát IP-címeként állítja be:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Várt kimenet:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus privát IP-címadatok beolvasása virtuális géphez
A fenti parancsfájllal létrehozott virtuális gép statikus privát IP-címadatainak megtekintéséhez futtassa a következő PowerShell-parancsot, és tartsa be az *IpAddress értékeit:*

    Get-AzureVM -Name DNS01 -ServiceName TestService

Várt kimenet:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Statikus privát IP-cím eltávolítása a virtuális gépről
A fenti parancsfájlban a virtuális géphez hozzáadott statikus privát IP-cím eltávolításához futtassa a következő PowerShell-parancsot:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Várt kimenet:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus privát IP-cím hozzáadása meglévő virtuális géphez
Ha statikus privát IP-címet szeretne hozzáadni a fenti parancsfájl lal létrehozott virtuális géphez, futtassa a következő parancsot:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Várt kimenet:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

Javasoljuk, hogy ne rendelje hozzá statikusan az Azure virtuális géphez rendelt privát IP-címet egy virtuális gép operációs rendszerén belül, kivéve, ha szükséges. Ha manuálisan állítja be a privát IP-címet az operációs rendszeren belül, győződjön meg arról, hogy az megegyezik az Azure virtuális géphez rendelt privát IP-címmel, vagy elveszítheti a virtuális géphez való kapcsolódást. Soha ne rendelje hozzá manuálisan az Azure virtuális géphez rendelt nyilvános IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>További lépések
* További információ a [fenntartott nyilvános IP-címekről.](virtual-networks-reserved-public-ip.md)
* További információ [a példányszintű nyilvános IP-címekről.](virtual-networks-instance-level-public-ip.md)
* Tekintse meg a [fenntartott IP-REST API-kat.](https://msdn.microsoft.com/library/azure/dn722420.aspx)

