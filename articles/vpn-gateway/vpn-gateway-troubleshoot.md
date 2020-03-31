---
title: 'Azure VPN-átjáró: Konfigurációk és kapcsolatok hibaelhárítása'
description: Ez a cikk a VPN-átjáró konfigurációjának, kapcsolatának és az átviteli rendszer érvényesítéséhez szükséges cikkekre mutató hivatkozásokat tartalmaz.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2018
ms.author: cherylmc
ms.openlocfilehash: d73bc8e9c39c14a254e8f7bae1d7b1a96a6c3225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780164"
---
# <a name="troubleshoot-vpn-gateway"></a>VPN-átjáró – problémamegoldás

A VPN-átjárókapcsolatok számos okból sikertelenek lehetnek. Ez a cikk hivatkozásokat tartalmaz a hibaelhárítás megkezdéséhez. A teljes listát a lap bal oldalán, a Hibaelhárítás csoportban található, **tartalomelhárítás**i.

## <a name="troubleshooting-scenarios-and-solutions"></a>Forgatókönyvek és megoldások hibaelhárítása

* [VPN teljesítményének érvényesítése VNeten](vpn-gateway-validate-throughput-to-vnet.md)<br>A VPN-átjárókapcsolat lehetővé teszi, hogy biztonságos, több helyiségen át vezető kapcsolatot hozzon létre az Azure-on belüli virtuális hálózat és a helyszíni informatikai infrastruktúra között. Ez a cikk bemutatja, hogyan érvényesítheti a hálózati átviteli a helyszíni erőforrások egy Azure virtuális gép (VM). Hibaelhárítási útmutatást is nyújt.

* [Vpn- és tűzfaleszköz-beállítások](vpn-gateway-third-party-settings.md)<br>Ez a cikk számos javasolt megoldást kínál a VPN-átjáróval használt külső VPN- vagy tűzfaleszközökhöz. A külső VPN- vagy tűzfaleszközök technikai támogatását az eszköz gyártója biztosítja.

* [Pont–hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>Ez a cikk a helyek közötti gyakori kapcsolódási problémákat sorolja fel. A bizottság ismerteti e problémák lehetséges okait és megoldásait is.

* [Helyek közötti kapcsolatok](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>Miután konfigurálta a helyszíni hálózat és az Azure virtuális hálózat közötti hely-hely VPN-kapcsolatot, a VPN-kapcsolat hirtelen leáll, és nem csatlakoztatható újra. Ez a cikk hibaelhárítási lépéseket tartalmaz a probléma megoldásához.

## <a name="next-steps"></a>További lépések

Ezekkel a lépésekkel [is ellenőrizheti a virtuális hálózat és a VPN-kapcsolatokat.](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)