---
title: Használja az Azure Resource Manager-sablonok Azure verem |} Microsoft Docs
description: Megtudhatja, hogyan használható az Azure-verem Azure Resource Manager-sablonok erőforrások kiépítése.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 9c4d538f77ae056163fd17aa547162a4ad3eff63
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Azure Resource Manager-sablonok használata az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure Resource Manager-sablonok segítségével telepítése és jogosultságok kiosztása összes erőforrását egyetlen, koordinált műveletben az alkalmazáshoz. Módosítja az erőforrásokat az erőforráscsoport-sablonok is telepítheti.

Ezeket a sablonokat is telepíthető a Microsoft Azure verem portal, PowerShell, a parancssor és a Visual Studio.

A következő gyors üzembe helyezési sablonokat [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Telepítse a SharePoint Server (nem-magas rendelkezésre állású telepítés)

A PowerShell DSC-bővítmény segítségével létrehozása a SharePoint Server 2013, amely a következőket tartalmazza:

* Virtuális hálózat
* Három storage-fiókok
* Két, a külső terheléselosztók
* Egyetlen tartományt az új erdő tartományvezérlőjeként konfigurált egy virtuális gép (VM)
* Önálló SQL Server 2014-kiszolgálóként konfigurált egy virtuális gép
* Egy üzemelő Azure virtuális Gépet egy számítógépen a SharePoint Server 2013 farmhoz

## <a name="deploy-ad-non-high-availability-deployment"></a>AD (nem magas-rendelkezésre állási-telepítési) telepítése

A PowerShell DSC-bővítmény segítségével hozzon létre egy AD tartományvezérlő kiszolgálót, amely a következőket tartalmazza:

* Virtuális hálózat
* Egy tárfiók
* Egy külső terheléselosztó
* Egyetlen tartományt az új erdő tartományvezérlőjeként konfigurált egy virtuális gép (VM)

## <a name="deploy-adsql-non-high-availability-deployment"></a>Active/SQL (nem magas-rendelkezésre állási-telepítési) telepítése

A PowerShell DSC-bővítmény segítségével hozzon létre egy SQL Server 2014 önálló kiszolgáló, amely a következőket tartalmazza:

* Virtuális hálózat
* Két storage-fiókok
* Egy külső terheléselosztó
* Egyetlen tartományt az új erdő tartományvezérlőjeként konfigurált egy virtuális gép (VM)
* Önálló SQL Server 2014-kiszolgálóként konfigurált egy virtuális gép

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Használja a PowerShell DSC-bővítményt egy meglévő virtuális gép helyi Configuration Manager (LCM) konfigurálása és regisztrálása az Azure Automation szolgáltatásbeli fiók DSC lekéréses kiszolgáló.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Virtuális gép létrehozása egy felhasználó lemezképből

Hozzon létre egy virtuális gépet egy egyéni felhasználói lemezkép. Ez a sablon is telepíti, a virtuális hálózaton (DNS), a nyilvános IP-cím és a hálózati illesztő.

## <a name="basic-virtual-machine"></a>Alapszintű virtuális gép

A Windows virtuális gép, amely tartalmazza a virtuális hálózaton (DNS), a nyilvános IP-cím és a hálózati illesztő telepíthető.

## <a name="cancel-a-running-template-deployment"></a>Folyamatban lévő futó sablon központi telepítés

Egy futó sablon-üzembehelyezés törléséhez használja a `Stop-AzureRmResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a portállal](azure-stack-deploy-template-portal.md)
* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
