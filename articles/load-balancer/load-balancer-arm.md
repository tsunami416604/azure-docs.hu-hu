---
title: Az Azure Resource Manager támogatása a terheléselosztó számára |} A Microsoft Docs
description: Load Balancer az Azure Resource Managerben powershell használatával. Terheléselosztó-sablonok használatával
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 43db9db3842d05fa13c3be92df14b905d2ddfc17
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059457"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Az Azure Resource Manager-támogatás használata az Azure Load Balancer



Az Azure Resource Manager az előnyben részesített felügyeleti keretrendszer szolgáltatásokhoz az Azure-ban. Az Azure Load Balancer az Azure Resource Manager-alapú API-k és eszközök használatával kezelhetők.

## <a name="concepts"></a>Alapelvek

A Resource Manager Azure Load Balancer következő gyermek erőforrásokat tartalmazza:

* Előtérbeli IP-konfiguráció – a terheléselosztó egy vagy több előtérbeli IP-címek, más néven virtuális IP-címek (VIP) tartalmazhat. Ezek az IP-címek szolgálnak a forgalom bemeneti pontjaként.
* Háttér címkészletet – ezek az a virtuális gép hálózati kártya (NIC), amelyhez a terhelés eloszlik társított IP-a.
* Terheléselosztási szabályok – olyan szabálytulajdonság leképezi egy adott előtérbeli IP és port kombinációját háttérbeli IP-címek készletét és port kombinációját. Egyetlen terheléselosztó több terheléselosztási szabállyal is rendelkezhet. Minden egyes szabály egy előtérbeli IP és port és a háttérbeli IP és port virtuális gépekhez társított kombinációja.
* Mintavételezők – mintavételek lehetővé teszik a Virtuálisgép-példányok állapotát nyomon követéséhez. Ha az állapotfigyelő mintavételező nem sikerül, a Virtuálisgép-példány a rotációból automatikusan.
* Bejövő NAT-szabályok – NAT-szabályok definiálása a bejövő forgalom áramolnak át az előtérbeli IP-címet, és ossza el a háttérbeli IP-Címmel.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Gyorsindítási sablonok

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.

Sablonok virtuális gépek, virtuális hálózatok, a rendelkezésre állási csoportok, hálózati adapterek (NIC), Tárfiókok, Terheléselosztók, hálózati biztonsági csoportok és nyilvános IP-címek definícióit is tartalmaznak. A sablonok olyan hozhat létre minden olyan összetett alkalmazás szükséges. A sablonfájl be verziókövetés és együttműködési tartalomkezelő rendszer ellenőrizhető.

[További információ a sablonok](../azure-resource-manager/resource-manager-template-walkthrough.md)

[További információ a hálózati erőforrások](../networking/networking-overview.md)

Gyorsindítási sablonok az Azure Load Balancer használatával, lásd: a [GitHub-adattár](https://github.com/Azure/azure-quickstart-templates) helyadatbázist közösségi létrehozott sablonok készletét.

Példákat láthat sablonokra:

* [2 virtuális gép, egy terheléselosztó és terheléselosztási szabályok](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuális gép, egy belső Load Balancer és a Load Balancer-szabályok a virtuális hálózat](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuális gép, egy terheléselosztó és az LB NAT-szabályok konfigurálása](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Az Azure Load Balancer PowerShell vagy parancssori felület beállítása

Ismerkedés az Azure Resource Manager parancsmagok, a parancssori eszközöket és a REST API-k

* [Azure-hálózat parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.network#networking) hozzon létre egy terheléselosztó használható.
* [Az Azure Resource Manager terheléselosztó létrehozása](load-balancer-get-started-ilb-arm-ps.md)
* [Az Azure CLI használatával az Azure Resource Managerrel](../xplat-cli-azure-resource-manager.md)
* [Load Balancer – REST API-k](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>További lépések

Emellett [egy internetkapcsolattal rendelkező terheléselosztó létrehozásának első lépései](load-balancer-get-started-internet-arm-ps.md) és konfigurálása, hogy milyen típusú [elosztási módjának](load-balancer-distribution-mode.md) számára egy adott terheléselosztó hálózati forgalom viselkedését.

Ismerje meg, hogyan kezelheti a [TCP-időkorlátjának beállításairól a terheléselosztó üresjárati](load-balancer-tcp-idle-timeout.md). Ez akkor fontos, ha az alkalmazásnak kell tartania a kapcsolatot egy terheléselosztó mögött lévő kiszolgálókkal életben.
