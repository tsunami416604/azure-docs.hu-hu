---
title: "VPN-átjáró konfigurációk és kapcsolatok hibáinak elhárítása |} Microsoft Docs"
description: "Ez a cikk segít kapcsolódik, a VPN-átjáró konfigurációját, a kapcsolat, hibaelhárításához és átviteli érvényesítéséhez."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2018
ms.author: cherylmc
ms.openlocfilehash: bb1074e361610cf3a8810aaeeb83717f791eb8bf
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="troubleshoot-vpn-gateway"></a>VPN-átjáró hibaelhárítása

Virtuális Magánhálózati átjárókapcsolatokhoz számos okból futhat hibára. Ez a cikk első hibaelhárítási mutató hivatkozásokat tartalmaz. Teljes listájáért lásd: a tartalomjegyzék alatt lévő cikkek **kapcsolatos problémák elhárítása**, ezen az oldalon balra.

## <a name="troubleshooting-scenarios-and-solutions"></a>Hibaelhárítási forgatókönyvek és megoldások

* [VPN teljesítményének érvényesítése VNeten](vpn-gateway-validate-throughput-to-vnet.md)<br>VPN gateway-kapcsolattal lehetővé teszi a biztonságos létrehozásához, létesítmények közötti kapcsolat között a virtuális hálózat az Azure és a helyszíni informatikai infrastruktúrát. Ez a cikk bemutatja, hogyan ellenőrizze a hálózati átviteli a helyszíni erőforrások az Azure virtuális gép (VM). Azt is nyújt hibaelhárítási útmutatót.

* [VPN- és a tűzfalon eszközbeállítások](vpn-gateway-third-party-settings.md)<br>Ez a cikk számos javasolt megoldást nyújt külső VPN vagy VPN-átjáróval használt tűzfal eszközök. Külső VPN- vagy tűzfal eszközök a technikai támogatási szolgálathoz biztosítja az eszköz gyártója.

* [Pont – hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>A cikk ismerteti, amelyekkel Ön is szembesülhet pont-pont csatlakozási probléma. Lehetséges okait és megoldásait ezeket a problémákat is ismerteti.

* [Pont-pont kapcsolatok](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>Miután konfigurált egy a helyszíni hálózat és az Azure virtuális hálózat közötti pont-pont VPN kapcsolatot, a VPN-kapcsolat hirtelen leáll, és nem kell csatlakoztatni. Ez a cikk nyújt hibaelhárítási segítséget nyújtanak a probléma megoldásához.

## <a name="next-steps"></a>További lépések

Használhatja az alábbi lépéseket [VNet érvényesítése és a VPN-kapcsolatok](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections).