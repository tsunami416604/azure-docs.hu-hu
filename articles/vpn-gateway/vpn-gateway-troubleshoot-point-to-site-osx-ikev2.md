---
title: Mac OS X-ügyfelekről Azure pont – hely típusú VPN-kapcsolatok hibáinak elhárítása |} Microsoft Docs
description: P2S Mac OS X VPN ügyfélkapcsolatok hibaelhárítása
services: vpn-gateway
documentationcenter: na
author: anzaman
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: 1cf8195cbf65f27c71a4db18c0c61c8a25673acd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Pont – hely típusú VPN-kapcsolatok a Mac OS X VPN-ügyfelek hibaelhárítása

Ez a cikk segítséget nyújt a pont-pont csatlakozási problémák a Mac OS X natív VPN-ügyfél és az IKEv2 használatával. A VPN-ügyfél a Mac IKEv2 protokoll nagyon alapszintű, és nem teszi lehetővé a sok testreszabáshoz. Nincsenek csak négy beállítások, amelyeket fel kell venni:

* Kiszolgálócím
* Távoli azonosítója
* Helyi azonosítója
* Hitelesítési beállítások
* Operációs rendszer verziója (10.11 vagy újabb)


## <a name="VPNClient"></a> Tanúsítványalapú hitelesítés hibáinak elhárítása
1. Ellenőrizze a VPN-ügyfél beállításait. Lépjen a **hálózati beállítás** billentyűkombináció lenyomásával parancs + Shift és majd írja be a "VPN" Ellenőrizze a VPN-ügyfél beállításait. A listában kattintson a VPN-bejegyzés, amelyet akkor kell megvizsgálni.

  ![IKEv2-alapú hitelesítés](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ellenőrizze, hogy a **kiszolgálócímet** teljes teljes Tartománynevét, és magában foglalja a cloudapp.net.
3. A **távoli azonosítója** ugyanaz, mint a kiszolgáló címe (Gateway FQDN) kell lennie.
4. A **helyi azonosítója** meg kell egyeznie a **tulajdonos** az ügyféltanúsítvány.
5. Kattintson a **hitelesítési beállítások** a hitelesítési beállítások lap megnyitásához.

  ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ellenőrizze, hogy **tanúsítvány** a legördülő menüből kiválasztott.
7. Kattintson a **válasszon** gombra, és győződjön meg arról, hogy a megfelelő tanúsítvány van kiválasztva. Kattintson a **OK** menti a módosításokat.

## <a name="ikev2"></a>Felhasználónév- és jelszóalapú hitelesítés hibáinak elhárítása

1. Ellenőrizze a VPN-ügyfél beállításait. Lépjen a **hálózati beállítás** billentyűkombináció lenyomásával parancs + Shift és majd írja be a "VPN" Ellenőrizze a VPN-ügyfél beállításait. A listában kattintson a VPN-bejegyzés, amelyet akkor kell megvizsgálni.

  ![IKEv2 felhasználónév-jelszó](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ellenőrizze, hogy a **kiszolgálócímet** teljes teljes Tartománynevét, és magában foglalja a cloudapp.net.
3. A **távoli azonosítója** ugyanaz, mint a kiszolgáló címe (Gateway FQDN) kell lennie.
4. A **helyi azonosítója** maradhat üresen.
5. Kattintson a **hitelesítési beállítást** gombra, és győződjön meg arról, hogy a legördülő menüből kiválasztott-e a "Felhasználónév".

  ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Győződjön meg arról, hogy a megfelelő hitelesítő adatok vannak megadva.

## <a name="additional"></a>További lépések

Ha az előző lépést minden helyesen van-e konfigurálva, töltse le a [Wireshark](https://www.wireshark.org/#download) , és végezze el a csomagrögzítéssel.

1. A szűrés *iskmp* , és tekintse meg a **IKE_SA** csomagok. Akkor nézze meg a rendszergazdai (SA) javaslat részletei alapján képesnek kell lennie a **forgalma: biztonsági társítás**. 
2. Ellenőrizze, hogy az ügyfél és a kiszolgáló rendelkezik-e a közös.

  ![Csomag](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg)

## <a name="next-steps"></a>További lépések
További segítségért olvassa el [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
