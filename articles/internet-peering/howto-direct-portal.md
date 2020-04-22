---
title: Közvetlen társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával
titleSuffix: Azure
description: Közvetlen társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681068"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Közvetlen társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával

Ez a cikk ismerteti, hogyan hozhat létre egy Microsoft Direct társviszony-létesítés az Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót az Azure PowerShell használatával is [elvégezheti.](howto-direct-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)
* Ha már rendelkezik közvetlen társviszony-létesítési kapcsolatokkal a Microsofttal, amelyek nincsenek azure-erőforrásokká konvertálva, olvassa el az [Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával című témakört.](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társviszony-létesítés létrehozása

A társviszony-létesítési erőforrás **Peering** használatával új társviszony-létesítési kérelmet hozhat létre.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás indítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társviszony-létesítés ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társviszony-létesítés módosítása
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társviszony-létesítés megszüntetése
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)
