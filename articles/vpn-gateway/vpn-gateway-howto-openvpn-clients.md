---
title: Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása | Microsoft Docs
description: Az OpenVPN-ügyfelek Azure-VPN Gateway konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: cherylmc
ms.openlocfilehash: 3366f3470e01e455acacf8748830f2b15c826f49
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997156"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Az Azure VPN Gateway OpenVPN-ügyfeleinek konfigurálása

Ez a cikk segítséget nyújt az **OpenVPN® Protocol** -ügyfelek konfigurálásához.

## <a name="before-you-begin"></a>Előzetes teendők

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ellenőrizze, hogy elvégezte-e a VPN-átjáróhoz tartozó OpenVPN konfigurálásához szükséges lépéseket. Részletekért lásd: [az OpenVPN konfigurálása az Azure VPN Gatewayhoz](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows-ügyfelek

1. Töltse le és telepítse az OpenVPN-ügyfelet (2,4-es vagy újabb verzió) a hivatalos [OpenVPN webhelyről](https://openvpn.net/index.php/open-source/downloads.html).
2. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal pont – hely konfiguráció lapján teheti meg, vagy a PowerShellben a "New-AzVpnClientConfiguration".
3. Csomagolja ki a profilt. Ezután nyissa meg a *vpnconfig. ovpn* konfigurációs fájlt az OpenVPN mappából a Jegyzettömb használatával.
4. [Exportálja](vpn-gateway-certificates-point-to-site.md#clientexport) a létrehozott és a P2S-konfigurációba az átjárón feltöltött P2S-ügyféltanúsítványt.
5. Bontsa ki a titkos kulcsot és a Base64 ujjlenyomatot a *. pfx*fájlból. Ezt többféleképpen megteheti. Az OpenSSL használata a gépen egy módszer. A *profileinfo. txt* fájl tartalmazza a titkos kulcsot, valamint a hitelesítésszolgáltató és az ügyféltanúsítvány ujjlenyomatát. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Nyissa meg a *profileinfo. txt fájlt* a Jegyzettömbben. Az ügyfél (gyermek) tanúsítványának ujjlenyomatának lekéréséhez jelölje ki a "-----BEGIN CERTIFICATe-----" és a "-----END CERTIFICATe-----" szöveget a gyermek tanúsítványhoz, és másolja azt. A gyermek tanúsítvány azonosításához tekintse meg a subject =/sort.
7. Váltson arra a *vpnconfig. ovpn* fájlra, amelyet a 3. lépésben megnyitott a Jegyzettömbben. Keresse meg az alább látható szakaszt, és cserélje le a "CERT" és a "/CERT" közötti mindent.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a *profileinfo. txt fájlt* a Jegyzettömbben. A titkos kulcs lekéréséhez válassza ki a "-----BEGIN PRIVATE KEY-----" és a "-----END titkos kulcs-----" szöveget, és másolja azt.
9. Térjen vissza a vpnconfig. ovpn fájlhoz a Jegyzettömbben, és keresse meg ezt a szakaszt. Illessze be a titkos kulcsot, és cserélje le a "Key" és a "/Key" közötti mindent.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. Másolja a vpnconfig.ovpn fájlt a C:\Program Files\OpenVPN\config mappába.
12. Kattintson a jobb gombbal az OpenVPN ikonjára a rendszertálcán, majd kattintson a Csatlakozás parancsra.

## <a name="mac"></a>Mac-ügyfelek

1. Töltse le és telepítsen egy OpenVPN-ügyfelet, például [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Töltse le az átjáró VPN-profilját. Ez a Azure Portal pont – hely konfiguráció lapján, vagy a PowerShellben a "New-AzVpnClientConfiguration" paranccsal végezhető el.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig. ovpn konfigurációs fájlt az OpenVPN mappából a Jegyzettömbben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A kódolt nyilvános kulcs lekéréséhez a tanúsítvány exportálásával kapcsolatos információkért lásd: [a nyilvános kulcs exportálása](vpn-gateway-certificates-point-to-site.md#cer) .
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A titkos kulcs kibontásával kapcsolatos információkért lásd: [a titkos kulcs exportálása](https://openvpn.net/community-resources/how-to/#pki) .
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. A profil Tunnelblick való létrehozásához kattintson duplán a profil fájlra.
8. Indítsa el a Tunnelblick az alkalmazások mappából.
9. Kattintson a Tunnelblick ikonra a tálcán, és válassza a kapcsolat menüpontot.

> [!IMPORTANT]
>Az OpenVPN protokoll csak az iOS 11,0 és újabb, valamint a MacOS 10,13 és újabb verziókat támogatja.
>

## <a name="linux"></a>Linux-ügyfelek

1. Nyisson meg egy új terminál-munkamenetet. Egy új munkamenet megnyitásához nyomja le a "CTRL + ALT + t" billentyűkombinációt egy időben.
2. A szükséges összetevők telepítéséhez írja be a következő parancsot:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Töltse le az átjáró VPN-profilját. Ezt a Azure Portal pont – hely konfiguráció lapján teheti meg.
4. [Exportálja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) a létrehozott és a P2S-konfigurációba az átjárón feltöltött P2S-ügyféltanúsítványt. 
5. Bontsa ki a titkos kulcsot és a Base64 ujjlenyomatot a. pfx fájlból. Ezt többféleképpen megteheti. Az OpenSSL használata a számítógépen egy módszer.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   A *profileinfo. txt* fájl tartalmazni fogja a titkos kulcsot és a hitelesítésszolgáltató ujjlenyomatát, valamint az ügyféltanúsítványt. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

6. Nyissa meg a *profileinfo. txt fájlt* egy szövegszerkesztőben. Az ügyfél (gyermek) tanúsítványának ujjlenyomatának lekéréséhez válassza a "-----BEGIN CERTIFICATe-----" és a "-----END CERTIFICATe-----" szöveget, és másolja azt. A gyermek tanúsítvány azonosításához tekintse meg a subject =/sort.

7. Nyissa meg az *vpnconfig. ovpn* fájlt, és keresse meg az alább látható szakaszt. Cserélje le a és a "CERT" és a "/CERT" közötti mindent.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a profileinfo. txt fájlt egy szövegszerkesztőben. A titkos kulcs beszerzéséhez válassza ki a "-----BEGIN PRIVATE KEY-----" és a "-----END titkos kulcs-----" szöveget, és másolja azt.

9. Nyissa meg a vpnconfig. ovpn fájlt egy szövegszerkesztőben, és keresse meg ezt a szakaszt. Illessze be a titkos kulcsot, és cserélje le a "Key" és a "/Key" közötti mindent.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. A parancssor használatával történő kapcsolódáshoz írja be a következő parancsot:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>&
    ```
12. A grafikus felhasználói felülettel való kapcsolódáshoz lépjen a rendszerbeállítások elemre.
13. Új VPN-kapcsolat hozzáadásához kattintson a **+** elemre.
14. A **VPN hozzáadása**területen válassza **az importálás fájlból lehetőséget..** .
15. Tallózással keresse meg a profil fájlt, és kattintson rá duplán, vagy válassza a **Megnyitás**lehetőséget.
16. A **VPN hozzáadása** ablakban kattintson a **Hozzáadás** gombra.
  
    ![Importálás fájlból](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. A csatlakozáshoz **kapcsolja be a VPN-** t a **hálózati beállítások** lapon, vagy a rendszertálcán a hálózat ikon alatt.

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, hogy a VPN-ügyfelek hozzáférhessenek egy másik VNet található erőforrásokhoz, kövesse a [VNet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk utasításait egy VNet-VNet kapcsolat beállításához. Ügyeljen arra, hogy az átjárók és a kapcsolatok esetében engedélyezze a BGP-t, ellenkező esetben a forgalom nem fog folytatódni.

**Az "OpenVPN" az OpenVPN Inc védjegye.**
