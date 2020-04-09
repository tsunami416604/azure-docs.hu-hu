---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986714"
---
## <a name="windows-clients"></a><a name="windows"></a>Windows-ügyfelek

1. Töltse le és telepítse az OpenVPN klienst (2.4-es vagy újabb verzió) a hivatalos [OpenVPN weboldalról.](https://openvpn.net/index.php/open-source/downloads.html)
2. Töltse le az átjáró VPN-profilját. Ez az Azure Portal point-site konfigurációs lapjáról, illetve a PowerShell "New-AzVpnClientConfiguration" beállításával végezhető el.
3. Csomagolja ki a profilt. Ezután nyissa meg a *vpnconfig.ovpn* konfigurációs fájlt az OpenVPN mappából a Jegyzettömb használatával.
4. Exportálja a létrehozott és az átjáró P2S-konfigurációba feltöltött pont-hely ügyféltanúsítványt. Használja a következő cikk hivatkozásokat:

   * [VPN-átjáró](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) utasításai
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) utasítások
5. Húzza ki a személyes kulcsot és az base64 ujjlenyomatot a *.pfx modellből.* Ezt többféleképpen megteheti. Az OpenSSL használata a számítógépen egy irányú. A *profileinfo.txt* fájl tartalmazza a személyes kulcsot és a hitelesítésszolgáltató és az ügyféltanúsítvány ujjlenyomatát. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Nyissa meg *a profileinfo.txt fájlt* a Jegyzettömbben. Az ügyfél (gyermek) tanúsítvány ujjlenyomatának bemásolásához jelölje ki a gyermektanúsítvány szövegét (beleértve és között)"-----BEGIN CERTIFICATE-----" és "-----END CERTIFICATE-----" szöveget a gyermektanúsítványhoz, és másolja. A gyermektanúsítványt a tárgy=/ sor alapján azonosíthatja.
7. Váltson *a* Jegyzettömbben a 3. Keresse meg az alábbi szakaszt, és cserélje ki a "cert" és a "/cert" közötti mindent.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a *profileinfo.txt* fájlt a Jegyzettömbben. A személyes kulcs bemásolásához jelölje ki a "-----BEGIN PRIVATE KEY-----" és a "-----END PRIVATE KEY-----" szöveget, és másolja azt.
9. Lépjen vissza a notepad vpnconfig.ovpn fájljára, és keresse meg ezt a szakaszt. Illessze be a személyes kulcsot, amely mindent helyettesít a "kulcs" és a "/kulcs" között.

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

## <a name="mac-clients"></a><a name="mac"></a>Mac-ügyfelek

1. OpenVPN-ügyfél, például [TunnelBlick letöltése](https://tunnelblick.net/downloads.html)és telepítése. 
2. Töltse le az átjáró VPN-profilját. Ez az Azure Portal pont-hely konfigurációs lapjáról, vagy a PowerShell "New-AzVpnClientConfiguration" használatával végezhető el.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig.ovpn konfigurációs fájlt az OpenVPN mappából egy szövegszerkesztőben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A kódolt nyilvános kulcs bekapásához a tanúsítványok exportálásáról az alábbi cikkhivatkozásokat használhatja:

   * [VPN-átjáró](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) utasításai 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) utasítások
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A [személyes kulcs exportálása](https://openvpn.net/community-resources/how-to/#pki) az OpenVPN webhelyen című témakörben talál információt a rról, hogyan bonthat ki egy személyes kulcsot.
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. Kattintson duplán a profilfájlra a profil tunnelblick-ben való létrehozásához.
8. Indítsa el a Tunnelblick alkalmazást az alkalmazások mappából.
9. Kattintson a Tunnelblick ikonra a tálcán, és válassza a connect lehetőséget.

> [!IMPORTANT]
>Az OpenVPN protokoll csak az iOS 11.0 és újabb, valamint a MacOS 10.13 és újabb rendszer támogatott.
>
## <a name="ios-clients"></a><a name="iOS"></a>iOS-ügyfelek

1. Telepítse az OpenVPN-ügyfelet (2.4-es vagy újabb verzió) az App Store-ból.
2. Töltse le az átjáró VPN-profilját. Ez az Azure Portal pont-hely konfigurációs lapjáról, vagy a PowerShell "New-AzVpnClientConfiguration" használatával végezhető el.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig.ovpn konfigurációs fájlt az OpenVPN mappából egy szövegszerkesztőben.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. A kódolt nyilvános kulcs bekapásához a tanúsítványok exportálásáról az alábbi cikkhivatkozásokat használhatja:

   * [VPN-átjáró](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) utasításai 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#cer) utasítások
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. A [személyes kulcs kinyeréséről](https://openvpn.net/community-resources/how-to/#pki) a személyes kulcs kinyeréséről a személyes kulcs exportálása az OpenVPN webhelyen című témakörben talál információt.
6. Ne módosítson semmilyen egyéb mezőt.
7. Küldje el e-mailben a profilfájlt (.ovpn) az iPhone levelezőalkalmazásában konfigurált e-mail fiókjába. 
8. Nyissa meg az e-mailt az iPhone levelezőalkalmazásában, és koppintson a csatolt fájlra

    ![E-mail megnyitása](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Érintse meg az **Egyebek** lehetőséget, ha nem látja **a Másolás openVPN-re** lehetőséget

    ![Továbbiak](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Koppintson a **Másolás openVPN-re** 

    ![Másolás openVPN-be](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Koppintson a **HOZZÁADÁS** elemre a **Profil importálása** lapon

    ![Hozzáadás](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Koppintson a **HOZZÁADÁS** elemre az **Importált profil** lapon

    ![Koppintson a Hozzáadás elemre](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Indítsa el az OpenVPN alkalmazást, és csúsztassa a kapcsolót a **Profil** lapon jobbra a csatlakozáshoz

    ![Kapcsolódás](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linux-ügyfelek

1. Nyisson meg egy új terminálmunkamenetet. Új munkamenetet a "Ctrl + Alt + t" billentyűkombinációval egyidejűleg megnyithat.
2. Adja meg a következő parancsot a szükséges összetevők telepítéséhez:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Töltse le az átjáró VPN-profilját. Ez az Azure Portal point-site konfigurációs lapjáról végezhető el.
4. Exportálja a létrehozott és az átjáróN lévő P2S-konfigurációba feltöltött P2S-ügyféltanúsítványt. Használja a következő cikk hivatkozásokat:

   * [VPN-átjáró](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) utasításai 
   
   * [Virtuális WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport) utasítások
5. Bontsa ki a személyes kulcsot és az base64 ujjlenyomatot a .pfx-ből. Ezt többféleképpen megteheti. Az OpenSSL használata a számítógépen az egyik módja.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   A *profileinfo.txt* fájl tartalmazza a személyes kulcsot és a hitelesítésszolgáltató ujjlenyomatát, valamint az ügyféltanúsítványt. Ügyeljen arra, hogy az ügyféltanúsítvány ujjlenyomatát használja.

6. Nyissa *meg a profileinfo.txt fájlt* egy szövegszerkesztőben. Az ügyfél (gyermek) tanúsítvány ujjlenyomatának bemásolásához jelölje ki a gyermektanúsítvány "-----BEGIN CERTIFICATE-----" és "-----END CERTIFICATE-----" szövegét, és másolja azt. A gyermektanúsítványt a tárgy=/ sor alapján azonosíthatja.

7. Nyissa meg a *vpnconfig.ovpn* fájlt, és keresse meg az alábbi szakaszt. Cseréljen ki mindent a "cert" és a "/cert" között.

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a profileinfo.txt fájlt egy szövegszerkesztőben. A személyes kulcs bemásolásához jelölje ki a "-----BEGIN PRIVATE KEY-----" és a "-----END PRIVATE KEY-----" szöveget tartalmazó szöveget, és másolja azt.

9. Nyissa meg a vpnconfig.ovpn fájlt egy szövegszerkesztőben, és keresse meg ezt a szakaszt. Illessze be a személyes kulcsot, amely mindent helyettesít a "kulcs" és a "/kulcs" között.

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. Ha a parancsssal szeretne csatlakozni, írja be a következő parancsot:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Ha a grafikus felhasználói felületen keresztül szeretne csatlakozni, nyissa meg a rendszerbeállításokat.
13. Ide **+** kattintva új VPN-kapcsolatot adhat hozzá.
14. A **VPN hozzáadása**csoportban válassza **az Importálás fájlból választót...**
15. Tallózással keresse meg a profilfájlt, és kattintson duplán a **Megnyitás gombra,** vagy válassza a Megnyitás gombot.
16. Kattintson a **Vpn hozzáadása** ablak **Hozzáadás** gombjára.
  
    ![Importálás fájlból](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. A **vpn** bekapcsolásával a **Hálózati beállítások** lapon vagy a rendszertálca hálózati ikonja alatt csatlakozhat.
