---
title: "Használja az Azure Resource Manager-sablonok Azure verem |} Microsoft Docs"
description: "Megtudhatja, hogyan használható az Azure-verem Azure Resource Manager-sablonok erőforrások kiépítése."
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure Resource Manager-sablonok használata az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure Resource Manager-sablonok telepítése, és jogosultságok kiosztása egyetlen, koordinált műveletben az alkalmazás összes erőforrást. Telepítse újra a módosításokat az erőforrások az erőforráscsoportban sablonok is.

Ezeket a sablonokat is telepíthető a Microsoft Azure verem portal, PowerShell, a parancssor és a Visual Studio.

A következő gyors üzembe helyezési sablonokat [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>SharePoint (nem magas rendelkezésre állás) telepítése
A PowerShell DSC-bővítmény segítségével hozzon létre a SharePoint 2013-farmhoz, amely a következőket tartalmazza:

* Virtuális hálózat
* Három storage-fiókok
* Két, a külső terheléselosztók
* Egy virtuális gép egyetlen tartományt az új erdő tartományvezérlőjeként konfigurálva
* Önálló SQL Server 2014-kiszolgálóként konfigurált egy virtuális gép
* Egy üzemelő Azure virtuális Gépet egy számítógépen a SharePoint 2013 farmhoz

## <a name="deploy-ad-non-high-availability"></a>AD (a nem magas rendelkezésre állás) telepítése
A PowerShell DSC-bővítmény segítségével hozzon létre egy AD tartományvezérlő kiszolgálót, amely a következőket tartalmazza:

* Virtuális hálózat
* Egy tárfiók
* Egy külső terheléselosztó
* Egy virtuális gép egyetlen tartományt az új erdő tartományvezérlőjeként konfigurálva

## <a name="deploy-adsql-non-high-availability"></a>Active/SQL (nem magas rendelkezésre állás) telepítése
A PowerShell DSC-bővítmény segítségével hozzon létre egy SQL Server 2014 önálló kiszolgáló, amely a következőket tartalmazza:

* Virtuális hálózat
* Két storage-fiókok
* Egy külső terheléselosztó
* Egy virtuális gép egyetlen tartományt az új erdő tartományvezérlőjeként konfigurálva
* Önálló SQL Server 2014-kiszolgálóként konfigurált egy virtuális gép

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Használja a PowerShell DSC-bővítményt egy meglévő virtuális gép helyi Configuration Manager (LCM) konfigurálása és regisztrálása az Azure Automation szolgáltatásbeli fiók DSC lekéréses kiszolgáló.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Virtuális gép létrehozása egy felhasználó lemezképből
Hozzon létre egy virtuális gépet egy egyéni felhasználói lemezkép. Ez a sablon is telepíti, a virtuális hálózaton (DNS), a nyilvános IP-cím és a hálózati illesztő.

## <a name="simple-vm"></a>Egyszerű VM
A Windows virtuális gép, amely tartalmazza a virtuális hálózaton (DNS), a nyilvános IP-cím és a hálózati illesztő telepíthető.

## <a name="cancel-a-running-template-deployment"></a>Folyamatban lévő futó sablon központi telepítés
Egy futó sablon-üzembehelyezés törléséhez használja a `Stop-AzureRmResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>Következő lépések
[Sablonok üzembe helyezése a portállal](azure-stack-deploy-template-portal.md)

[Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)

