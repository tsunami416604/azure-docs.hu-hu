---
title: Mac OS X-ügyfelek az Azure pont – hely VPN-kapcsolatok hibaelhárítása |} A Microsoft Docs
description: P2S Mac OS X VPN kapcsolatok hibaelhárítása
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
ms.openlocfilehash: 0c058cb6547d67469d3138dc331b6181c07e6e65
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087524"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>A Mac OS X VPN-ügyfelek a pont – hely VPN-kapcsolatok hibaelhárítása

Ez a cikk segít a Mac OS X használata a natív VPN-ügyfél és az IKEv2 pont – hely kapcsolati problémák elhárításához. A VPN-ügyfél a Mac rendszerben az IKEv2 protokollhoz nagyon egyszerű, és nem teszi lehetővé a sok testreszabáshoz. Nincsenek ellenőrizendő csak négy beállítások:

* Kiszolgáló címe
* Távoli azonosítója
* Helyi azonosítója
* Hitelesítési beállítások
* Operációsrendszer-verzió (10.11 vagy újabb)


## <a name="VPNClient"></a> Hibaelhárítás – Tanúsítványalapú hitelesítés
1. Ellenőrizze a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítás** paranccsal + Shift, és a ellenőrizze a VPN-ügyfél beállításait, majd írja be a "VPN" billentyű lenyomásával. A listából kattintson a VPN-bejegyzés, meg kell vizsgálni.

   ![Az IKEv2-alapú hitelesítés](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ellenőrizze, hogy a **kiszolgálócím** teljes Tartománynevét, és a cloudapp.net tartalmaz.
3. A **távoli azonosítója** ugyanaz, mint a kiszolgáló címe (átjáró FQDN) kell lennie.
4. A **helyi azonosítója** legyen ugyanaz, mint a **tulajdonos** az ügyféltanúsítvány.
5. Kattintson a **hitelesítési beállítások** nyissa meg a hitelesítési beállításokat.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ellenőrizze, hogy **tanúsítvány** kiválasztott elemet a legördülő listából.
7. Kattintson a **kiválasztása** gombra, és győződjön meg arról, hogy a megfelelő tanúsítvány van kiválasztva. Kattintson a **OK** menteni a módosításokat.

## <a name="ikev2"></a>Felhasználónév- és jelszóalapú hitelesítés hibaelhárítása

1. Ellenőrizze a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítás** paranccsal + Shift, és a ellenőrizze a VPN-ügyfél beállításait, majd írja be a "VPN" billentyű lenyomásával. A listából kattintson a VPN-bejegyzés, meg kell vizsgálni.

   ![Az IKEv2 felhasználónév-jelszó](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ellenőrizze, hogy a **kiszolgálócím** teljes Tartománynevét, és a cloudapp.net tartalmaz.
3. A **távoli azonosítója** ugyanaz, mint a kiszolgáló címe (átjáró FQDN) kell lennie.
4. A **helyi azonosítója** maradhat üresen.
5. Kattintson a **hitelesítési beállítást** gombra, és győződjön meg arról, hogy a "Felhasználónév" van kiválasztva a legördülő listából.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.jpg)
6. Győződjön meg arról, hogy a helyes hitelesítő adatok vannak megadva.

## <a name="additional"></a>További lépések

Ha, próbálja ki az előző lépést, és mindent megfelelően van konfigurálva, töltse le a [Wireshark](https://www.wireshark.org/#download) és végezzen csomagrögzítés.

1. Szűrés *isakmp* tekintse meg a **IKE_SA** csomagokat. Tekintse meg az SA-javaslat részletei alapján elvileg a **adattartalom: biztonsági társítás**. 
2. Ellenőrizze, hogy az ügyfél és a kiszolgáló rendelkezik-e a közös.

   ![csomag](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Ha nem érkezik válasz server, az a hálózati nyomkövetés, ellenőrizze a engedélyezte az IKEv2 protokollt, az Azure Portal webhelyen az Azure-átjáró konfigurációs lapján.

## <a name="next-steps"></a>További lépések
További segítségért lásd: [Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
