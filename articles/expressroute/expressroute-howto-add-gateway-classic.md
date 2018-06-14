---
title: 'A virtuális hálózat átjárót konfigurálhatja a PowerShell használatával ExpressRoute: klasszikus: Azure |} Microsoft Docs'
description: Klasszikus üzembe helyezés hálózatok átjáró konfigurálásához modellhez tartozó virtuális hálózaton, ExpressRoute-konfigurációhoz a PowerShell használatával.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: 195a38fa45f1c514a93980e777fb0d8238aa3f3f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23850636"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>A virtuális hálózati átjáró konfigurálása az ExpressRoute (klasszikus) PowerShell használatával
> [!div class="op_single_selector"]
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasszikus - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Ez a cikk végigvezeti a lépések hozzáadásához átméretezése, és távolítsa el a virtuális hálózathoz (VNet) átjáró egy már meglévő vnet. Ehhez a konfigurációhoz lépésekre, kifejezetten a Vnetek használatával létrehozott a **klasszikus üzembe helyezési modellel** és fogja tárolni egy ExpressRoute-konfigurációja használható. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Mielőtt hozzálát
Győződjön meg arról, hogy telepítette-e az ehhez a konfigurációhoz szükség Azure PowerShell-parancsmagok (1.0.2-es vagy újabb). Ha még nem telepítette a parancsmagok, szüksége lesz erre a konfigurációs lépések megkezdése előtt. További információ az Azure PowerShell telepítése: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>További lépések
Miután létrehozta a virtuális hálózat átjáró, a virtuális hálózat hozzákapcsolhatja egy ExpressRoute-kapcsolatcsoportot. Lásd: [virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-classic.md).

