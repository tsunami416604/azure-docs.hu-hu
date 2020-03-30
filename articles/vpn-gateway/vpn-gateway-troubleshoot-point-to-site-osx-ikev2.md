---
title: 'Azure VPN-átjáró: A helyek közötti kapcsolatok hibaelhárítása: Mac OS X-ügyfelek'
description: A P2S Mac OS X VPN-ügyfélkapcsolatok hibáinak elhárításának lépései
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 03/27/2018
ms.author: alzam
ms.openlocfilehash: f88053c93884e10e46a0f7d70106bda67b057562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425721"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Mac OS X VPN-ügyfelek pont-hely VPN-kapcsolatainak hibaelhárítása

Ez a cikk segítséget nyújt a Mac OS X rendszerből származó, a helyek közötti kapcsolódási problémák elhárításához a natív VPN-ügyfél és az IKEv2 használatával. A VPN-kliens a Mac for IKEv2-ben nagyon egyszerű, és nem tesz lehetővé sok testreszabást. Csak négy beállítást kell ellenőrizni:

* Kiszolgáló címe
* Távoli azonosító
* Helyi azonosító
* Hitelesítési beállítások
* Operációs rendszer verziója (10.11 vagy újabb)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a>Tanúsítványalapú hitelesítés – problémamegoldás
1. Ellenőrizze a VPN-ügyfél beállításait. Lépjen a **Hálózati beállításhoz** a Command + Shift billentyű lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából kattintson a kivizsgálandó VPN-bejegyzésre.

   ![IKEv2 tanúsítványalapú hitelesítés](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Ellenőrizze, hogy a **kiszolgálócíme** a teljes teljes teljes tartománynn, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgálócímével (átjáró teljes tartományna).
4. A **helyi azonosítónak** meg kell egyeznie az ügyféltanúsítvány **tulajdonosával.**
5. Kattintson a **Hitelesítési beállítások gombra** a Hitelesítési beállítások lap megnyitásához.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Ellenőrizze, hogy a **Tanúsítvány** ki van-e jelölve a legördülő menüből.
7. Kattintson a **Kijelölés** gombra, és ellenőrizze, hogy a megfelelő tanúsítvány van-e kiválasztva. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Felhasználónév- és jelszóhitelesítés – problémamegoldás

1. Ellenőrizze a VPN-ügyfél beállításait. Lépjen a **Hálózati beállításhoz** a Command + Shift billentyű lenyomásával, majd írja be a "VPN" parancsot a VPN-ügyfél beállításainak ellenőrzéséhez. A listából kattintson a kivizsgálandó VPN-bejegyzésre.

   ![IKEv2 felhasználónév jelszava](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Ellenőrizze, hogy a **kiszolgálócíme** a teljes teljes teljes tartománynn, és tartalmazza-e a cloudapp.net.
3. A **távoli azonosítónak** meg kell egyeznie a kiszolgálócímével (átjáró teljes tartományna).
4. A **helyi azonosító** üres lehet.
5. Kattintson a **Hitelesítési beállítás** gombra, és ellenőrizze, hogy a "Felhasználónév" lehetőség van-e kiválasztva a legördülő menüből.

   ![Hitelesítési beállítások](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Ellenőrizze, hogy a megfelelő hitelesítő adatok at adják-e meg.

## <a name="additional-steps"></a><a name="additional"></a>További lépések

Ha megpróbálja az előző lépéseket, és minden megfelelően van konfigurálva, töltse le [a Wireshark-ot,](https://www.wireshark.org/#download) és hajtson végre egy csomagrögzítést.

1. Szűrd az *isakmp-ra,* és nézd meg a **IKE_SA** csomagokat. Önnek képesnek kell lennie arra, hogy nézd meg a SA javaslat részleteit a **Hasznos teher: Security Association**. 
2. Ellenőrizze, hogy az ügyfél és a kiszolgáló rendelkezik-e közös készletlel.

   ![Csomag](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Ha nincs kiszolgálóválasz a hálózati nyomkövetéseken, ellenőrizze, hogy engedélyezte-e az IKEv2 protokollt az Azure Gateway konfigurációs lapján az Azure Portal webhelyén.

## <a name="next-steps"></a>További lépések
További segítségért olvassa el [a Microsoft támogatási szolgálatát.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
