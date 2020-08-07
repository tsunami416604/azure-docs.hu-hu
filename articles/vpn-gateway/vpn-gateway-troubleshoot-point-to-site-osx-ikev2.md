---
title: 'Azure VPN Gateway: pont – hely kapcsolatok hibakeresése: Mac OS X-ügyfelek'
description: 'A pont – hely kapcsolat hibakeresése Mac OS X-ről. a következő beállítások ellenőrzése: kiszolgáló címe, távoli azonosító, helyi azonosító, hitelesítési beállítások és operációs rendszer verziója.'
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: ae7ca9ad69bc5bea20fc959f86e41439bd01cd5c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920755"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Pont – hely típusú VPN-kapcsolatok hibakeresése Mac OS X VPN-ügyfelektől

Ez a cikk segítséget nyújt a Mac OS X pont – hely kapcsolati problémák hibaelhárításához a natív VPN-ügyfél és a IKEv2 használatával. A IKEv2 Mac rendszerű VPN-ügyfele nagyon alapszintű, és nem teszi lehetővé a sok testreszabást. Csak négy beállítást kell ellenőrizni:

* Kiszolgáló címe
* Távoli azonosító
* Helyi azonosító
* Hitelesítési beállítások
* Operációs rendszer verziója (10,11 vagy újabb)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Tanúsítványalapú hitelesítés – problémamegoldás
1. Keresse meg a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítást** a Command + Shift billentyűkombináció lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából válassza ki a vizsgálni kívánt VPN-bejegyzést.

   ![IKEv2 tanúsítvány alapú hitelesítés](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ellenőrizze, hogy a **kiszolgáló címe** a teljes FQDN, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgáló címeként (az ÁTJÁRÓ teljes tartományneve).
4. A **helyi azonosítónak** meg kell egyeznie az ügyféltanúsítvány **tárgyával** .
5. Kattintson a **hitelesítési beállítások** elemre a hitelesítési beállítások lap megnyitásához.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ellenőrizze, hogy a **tanúsítvány** ki van-e választva a legördülő listából.
7. Kattintson a **kiválasztás** gombra, és ellenőrizze, hogy a megfelelő tanúsítvány van-e kiválasztva. A módosítások mentéséhez kattintson **az OK** gombra.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Felhasználónév és jelszó hitelesítésének hibakeresése

1. Keresse meg a VPN-ügyfél beállításait. Nyissa meg a **hálózati beállítást** a Command + Shift billentyűkombináció lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából válassza ki a vizsgálni kívánt VPN-bejegyzést.

   ![IKEv2 Felhasználónév jelszava](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ellenőrizze, hogy a **kiszolgáló címe** a teljes FQDN, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgáló címeként (az ÁTJÁRÓ teljes tartományneve).
4. A **helyi azonosító** üres is lehet.
5. Kattintson a **hitelesítési beállítások** gombra, és ellenőrizze, hogy a "username" lehetőség ki van-e választva a legördülő listából.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Ellenőrizze, hogy a helyes hitelesítő adatok meg vannak-e adva.

## <a name="additional-steps"></a><a name="additional"></a>További lépések

Ha kipróbálja az előző lépéseket, és minden megfelelően van konfigurálva, töltse le a [Wireshark](https://www.wireshark.org/#download) , és hajtson végre egy csomagot.

1. Szűrje az *ISAKMP* -t, és tekintse meg a **IKE_SA** csomagokat. Tekintse meg az SA-javaslat részleteit a **hasznos adatok: biztonsági társítás**lehetőség alatt. 
2. Ellenőrizze, hogy az ügyfél és a kiszolgáló közös készlettel rendelkezik-e.

   ![csomag](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Ha nincs kiszolgálói válasz a hálózati nyomkövetéseken, ellenőrizze, hogy engedélyezve van-e a IKEv2 protokoll az Azure Gateway Configuration lapon a Azure Portal webhelyén.

## <a name="next-steps"></a>További lépések
További segítségért lásd: [Microsoft ügyfélszolgálata](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
