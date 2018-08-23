---
title: Az Azure Resource Manager-sablonok használata az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Resource Manager-sablonok az Azure Stackben erőforrások kiépítése.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 456f27b97ee644aef34f9bb9e2c0525bd61c1c84
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060836"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Az Azure Resource Manager-sablonok használata az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Resource Manager-sablonok segítségével telepítése és jogosultságok kiosztása összes erőforrását egyetlen, koordinált műveletben az alkalmazáshoz. Telepítse újra a sablonok az erőforrásokat egy erőforráscsoportba tartozó módosíthatja is.

Ezeket a sablonokat is üzembe helyezhetők a Microsoft Azure Stack portal, PowerShell, a parancssor és a Visual Studio.

A következő gyorsindítási sablonok érhetők el a [GitHub](http://aka.ms/azurestackgithub).

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>A SharePoint Server (nem – magas rendelkezésre állású üzembe helyezés) üzembe helyezése

A PowerShell DSC bővítmény segítségével hozzon létre egy SharePoint Server 2013-farm, amely tartalmazza az alábbi forrásanyagokat:

* Virtuális hálózat
* Három tárfiókot tartalmaz
* Két a külső terheléselosztók
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva
* Egy virtuális Gépet egy önálló SQL Server 2014-kiszolgáló
* Egy virtuális Gépet egy egy számítógépen a SharePoint Server 2013-farm

## <a name="deploy-ad-non-high-availability-deployment"></a>(Nem nagy – rendelkezésre állás – telepítési) AD üzembe helyezése

A PowerShell DSC bővítmény segítségével hozzon létre egy AD tartományvezérlő kiszolgálóra, amely tartalmazza az alábbi forrásanyagokat:

* Virtuális hálózat
* Egy storage-fiók
* Egy külső terheléselosztó
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva

## <a name="deploy-adsql-non-high-availability-deployment"></a>Az AD/SQL (nem nagy – rendelkezésre állás – telepítési) üzembe helyezése

A PowerShell DSC bővítmény segítségével hozzon létre egy SQL Server 2014 önálló kiszolgáló, amely a következő forrásokat tartalmazza:

* Virtuális hálózat
* Két tárfiókot
* Egy külső terheléselosztó
* Egy virtuális gép (VM) egy új erdő egyetlen tartományt a tartományvezérlővel konfigurálva
* Egy virtuális Gépet egy önálló SQL Server 2014-kiszolgáló

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

A PowerShell DSC bővítmény segítségével egy meglévő virtuális gép helyi Configuration Manager (LCM) konfigurálása és a egy Azure Automation fiókhoz DSC lekéréses kiszolgálón való bejegyzéséhez.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Virtuális gép létrehozása felhasználói rendszerképből

Virtuális gép létrehozása egyéni felhasználói rendszerképből. Ez a sablon is üzembe helyez egy virtuális hálózatot (a DNS-), a nyilvános IP-cím és a egy hálózati adapter.

## <a name="basic-virtual-machine"></a>Alapszintű virtuális gép

Windows virtuális gép üzembe helyezése egy virtuális hálózatot (a DNS-), a nyilvános IP-cím és a egy hálózati adaptert tartalmaz.

## <a name="cancel-a-running-template-deployment"></a>Mégse sablon üzemelő példányban

Mégse sablon üzemelő példányban, használja a `Stop-AzureRmResourceGroupDeployment` PowerShell-parancsmagot.

## <a name="next-steps"></a>További lépések

* [Sablonok üzembe helyezése a portállal](azure-stack-deploy-template-portal.md)
* [Az Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md)
