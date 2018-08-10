---
title: PowerShell beállítása a virtuális gép létrehozása a Marketplace-en |} A Microsoft Docs
description: Azure PowerShell telepítéséhez és használhatná egy nem kötelező folyamat áramlanak a központi telepítése Virtuálisgép-rendszerképek létrehozása, valamint a értékesítheti az Azure Marketplace-en
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: hascipio
ms.openlocfilehash: bbcce5093d2bbd5326523063db7d0e565fe4de6d
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39713635"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Azure PowerShell beállítása a hozzon létre egy ajánlatot az Azure Marketplace-en
Az Azure-ban a PowerShell beállítása a részletes információkért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview). Egy egyszerű módja, hogy a tanúsítvány metódussal, amely letölti és a hitelesítéshez szükséges tanúsítványt importál. A szükséges tanúsítvány beszerzéséhez használja a **Get-AzurePublishSettingsFile** parancsmagot. Mentse a fájlt, amikor a rendszer kéri. Importálja a tanúsítványt egy PowerShell-munkamenetbe, használja a **Import-AzurePublishSettingsFile** parancsmagot.

Konfigurálja, és tárolja a gyakori a Microsoft Azure-előfizetések beállításait a PowerShell-munkamenet, használja a **Set-AzureSubscription** és **Select-AzureSubscription** parancsmagok:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Az első parancs egy alapértelmezett tárfiókot társítja az előfizetés (néhány virtuális gép üzembe helyezési művelet szükséges).  A második lehetővé teszi az előfizetés (ismeri fel más parancsmagok) a jelenlegivel.

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)
* [Egy virtuálisgép-rendszerképet hoz létre a Marketplace-en](marketplace-publishing-vm-image-creation.md)

