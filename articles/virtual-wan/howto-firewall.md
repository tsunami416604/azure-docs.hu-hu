---
title: Azure Firewall telepítése virtuális WAN-központban
titleSuffix: Azure Virtual WAN
description: A Azure Firewall konfigurálásának lépései egy virtuális WAN-központban
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/21/2020
ms.author: cherylmc
ms.openlocfilehash: f20ed76a72eecce59a7b8795a42b033230a2f7e0
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84753685"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Azure Firewall konfigurálása virtuális WAN-központban

A **biztonságos központ** egy Azure Firewall-vel rendelkező Azure-beli virtuális WAN-központ. Ebből a cikkből megtudhatja, hogyan alakíthatja át a virtuális WAN-központot egy biztonságos hubhoz úgy, hogy Azure Firewall közvetlenül az Azure Virtual WAN portál oldaláról telepíti.

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések azt feltételezik, hogy már telepített egy virtuális WAN-t egy vagy több hubhoz.

Új virtuális WAN és új központ létrehozásához kövesse az alábbi cikkekben ismertetett lépéseket:

* [Virtuális WAN létrehozása](virtual-wan-site-to-site-portal.md#openvwan)
* [Elosztó létrehozása](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Virtuális hubok megtekintése

A virtuális WAN **áttekintő** lapja a virtuális hubok és a biztonságos hubok listáját jeleníti meg. Az alábbi ábrán egy biztonságos hubok nélküli virtuális WAN látható.

[![Áttekintés](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Konvertálás biztonságos hubhoz

1. A virtuális WAN **Áttekintés** lapján válassza ki azt a hubot, amelyet biztonságos hubhoz kíván átalakítani. A virtuális központ lapon két lehetőséget láthat a Azure Firewall üzembe helyezésére ebben a központban. Válassza az egyik lehetőséget.

   [![Biztonság](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Az egyik lehetőség kiválasztását követően megjelenik az **átalakítás biztonságos hubhoz** lapra. Válasszon ki egy konvertálandó központot, majd kattintson a **Next (Azure Firewall tovább** ) gombra a lap alján.

   [![hub kiválasztása](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. A munkafolyamat befejezése után válassza a **Confirm (megerősítés**) lehetőséget.

   [![megerősítés](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Miután a hubot egy biztonságos hubhoz alakította át, megtekintheti a virtuális WAN **Áttekintés** lapján.

   [![biztonságos központ megtekintése](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Hub-erőforrások megtekintése

A virtuális WAN **áttekintése** lapon válassza ki a biztonságos hubot. A központ lapon megtekintheti az összes virtuális központ erőforrását, beleértve a Azure Firewall is.

[![hub-erőforrások megtekintése](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Ha Azure Firewall beállításokat szeretne megtekinteni a biztonságos hubhoz, a **Biztonság**területen válassza a **biztonságos virtuális központ beállításai**lehetőséget.
[![központ beállításainak megtekintése](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>További beállítások konfigurálása

A virtuális központ további Azure Firewall beállításainak konfigurálásához válassza a **Azure Firewall Managerre**mutató hivatkozást. További információ a tűzfal-házirendekről: [Azure Firewall Manager](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![További beállítások](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Ha vissza szeretne térni a hub **– Áttekintés** lapra, a következő ábrán látható nyílra kattintva visszatérhet a kívánt elérési útra.

[![Visszatérés az áttekintéshez](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>További lépések

A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
