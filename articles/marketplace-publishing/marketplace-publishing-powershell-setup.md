---
title: A virtuális gép létrehozása a piactér PowerShell beállítása |} Microsoft Docs
description: Beállítása az Azure PowerShell és a választható folytatásához használja a virtuális gép lemezképeket telepíteni áramló, és az Azure piactérről értékesítés
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e19d6cda-76df-4e42-be84-c9fe47a636db
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2016
ms.author: mbaldwin
ms.openlocfilehash: 72ee1ac612fc4c7191718009a78f55272f0a44cf
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
ms.locfileid: "29937399"
---
# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Állítsa be az Azure PowerShell ajánlatot létrehozása az Azure piactéren
Az Azure PowerShell beállításáról további információkért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview). Egy egyszerű módszert alkalmaz, hogy a tanúsítvány metódus, amely letölti és importálja a tanúsítvány szükséges a hitelesítéshez használja. A szükséges tanúsítvány beszerzéséhez használja a **Get-AzurePublishSettingsFile** parancsmag. Mentse a fájlt, amikor a rendszer kéri. Importálja a tanúsítványt egy PowerShell-munkamenetet, használja a **Import-AzurePublishSettingsFile** parancsmag.

Konfigurálja, és tárolja a közös Microsoft Azure-előfizetési beállítások a PowerShell-munkamenetet, használja a **Set-AzureSubscription** és **válasszon-AzureSubscription** parancsmagokat:

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

Az első parancs egy alapértelmezett tárfiókot társítja az előfizetés (néhány virtuális gép üzembe helyezési műveletekhez szükséges).  A második lehetővé teszi az előfizetés, a jelenlegivel (ismeri fel más parancsmagok).

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)
* [Hozzon létre egy virtuálisgép-lemezkép a piactér](marketplace-publishing-vm-image-creation.md)

