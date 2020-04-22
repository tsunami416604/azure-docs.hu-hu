---
title: Exchange-társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával
titleSuffix: Azure
description: Exchange-társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680951"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Exchange-társviszony-létesítés létrehozása vagy módosítása az Azure Portal használatával

Ez a cikk ismerteti, hogyan hozhat létre egy Microsoft Exchange-társviszony-létesítés az Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-exchange-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)
* Ha már rendelkezik Exchange-társviszony-létesítéssel a Microsofttal, amely nem konvertálva Azure-erőforrásokká, olvassa el az [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával című témakört.](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társviszony-létesítés létrehozása

A társviszony-létesítési erőforrás **Peering** használatával új társviszony-létesítési kérelmet hozhat létre.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Az erőforrás elindítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange-társviszony-létesítés ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társviszony-létesítés módosítása
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange-társviszony-létesítés megszüntetése
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>További források

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)
