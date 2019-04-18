---
title: OpenVPN ügyfelek konfigurálása Azure VPN Gateway |} A Microsoft Docs
description: Az Azure VPN Gateway OpenVPN ügyfelek konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 1/15/2019
ms.author: cherylmc
ms.openlocfilehash: d1e57e623e3e95f3d71e895c49c928f00aa0ad46
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59274672"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Azure VPN Gateway (előzetes verzió) OpenVPN ügyfelek konfigurálása

Ez a cikk segít konfigurálása **OpenVPN® protokoll** ügyfelek.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Győződjön meg arról, hogy végrehajtotta a OpenVPN konfigurálása a VPN-átjárót. További információkért lásd: [OpenVPN konfigurálása Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows ügyfelek

1. Töltse le és telepítse a hivatalos a OpenVPN ügyfél [OpenVPN webhely](https://openvpn.net/index.php/open-source/downloads.html).
2. Töltse le az átjáró VPN-profilját. Ezt megteheti a pont – hely konfiguráció lapon az Azure Portalon vagy a "New-AzVpnClientConfiguration" parancsot a PowerShellben.
3. Csomagolja ki a profilt. Ezután nyissa meg a *vpnconfig.ovpn* konfigurációs fájlt a Jegyzettömbben OpenVPN mappából.
4. [Exportálás](vpn-gateway-certificates-point-to-site.md#clientexport) a P2S-ügyféltanúsítvány során létrehozott és feltöltött a P2S konfiguráció az átjárón.
5. Bontsa ki a titkos kulcs és a base64 ujjlenyomatot a *.pfx*. Ezt többféleképpen megteheti. OpenSSL használatával a gépén az egyik módja. A *profileinfo.txt* fájl tartalmazza a hitelesítésszolgáltató és az ügyféltanúsítványt a titkos kulcs és az ujjlenyomat. Ügyeljen arra, használja az ügyféltanúsítvány ujjlenyomatát.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Nyissa meg *profileinfo.txt* a Jegyzettömbben. Az ügyfél (gyermek) tanúsítvány ujjlenyomatának beszerzéséhez válassza ki a szöveget (köztük a és közötti) "----BEGIN CERTIFICATE---" és "----END tanúsítvány---" a gyermek tanúsítvány, és másolja azt. A gyermek tanúsítvány azonosíthatja a subject = megnézzük / sor.
7. Váltson a *vpnconfig.ovpn* 3. lépésében a Jegyzettömbben megnyitott fájl. Az alább látható szakaszt keresés és csere mindent között "cert" és "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Nyissa meg a *profileinfo.txt* a Jegyzettömbben. A titkos kulcs lekéréséhez válassza ki a szöveget (köztük a és közötti) "----BEGIN PRIVATE KEY----" és "----END PRIVATE KEY----", és másolja azt.
9. Lépjen vissza a vpnconfig.ovpn fájlt a Jegyzettömbben, és ebben a szakaszban található. Illessze be a titkos kulcsot minden között cseréje és a "kulcs" és "/ kulcs".

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

## <a name="mac"></a>Mac-ügyfél

1. Ügyfél letöltése és telepítése egy OpenVPN, mint például [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Töltse le az átjáró VPN-profilját. Ezt megteheti a pont – hely konfiguráció lapon, az Azure Portalon, vagy a "New-AzVpnClientConfiguration" a PowerShell használatával.
3. Csomagolja ki a profilt. Nyissa meg a vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben OpenVPN mappából.
4. Töltse ki a pont–hely ügyféltanúsítványra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású nyilvános kulcsával. PEM formátumú tanúsítvány esetén egyszerűen nyissa meg a .cer-fájlt, és másolja a Base64-kódolású kulcsot a tanúsítványfejlécek között. Lásd: [nyilvános kulcs exportálása](vpn-gateway-certificates-point-to-site.md#cer) a tanúsítványt a kódolt nyilvános kulcs lekérésével kapcsolatos információk.
5. Töltse ki a titkos kulcsra vonatkozó részt a pont–hely ügyféltanúsítvány Base-64-kódolású titkos kulcsával. Lásd: [a titkos kulcs exportálását választom](https://openvpn.net/community-resources/how-to/#pki) hogyan lehet kigyűjteni a titkos kulccsal kapcsolatos információkat.
6. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
7. Kattintson duplán a profil fájlt tunnelblik a profil létrehozásához.
8. Indítsa el az alkalmazások mappából Tunnelblik.
9. Kattintson a tálcán Tunnelblik ikonra, és válasszon csatlakozzon.

> [!IMPORTANT]
>Csak iOS 11.0-s vagy újabb verzió és a MacOS 10.13 és újabb OpenVPN protokoll használata támogatott.
>

## <a name="linux"></a>Linux-ügyfelek

1. Nyisson meg egy új terminál-munkamenetben. Új munkamenet "Ctrl + Alt + t" lenyomásával megnyithatja egy időben.
2. Adja meg a szükséges összetevők telepítése a következő parancsot:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Töltse le az átjáró VPN-profilját. Ez a pont – hely konfiguráció lapon az Azure Portalon teheti meg.
4. [Exportálás](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) a P2S-ügyféltanúsítvány során létrehozott és feltöltött a P2S konfiguráció az átjárón. 
5. Bontsa ki a .pfx a titkos kulcs és a base64-ujjlenyomata. Ezt többféleképpen megteheti. OpenSSL használatával a számítógép egyik módja a.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   A *profileinfo.txt* fájl tartalmazza a titkos kulcs és az ujjlenyomatot a hitelesítésszolgáltató és az ügyféltanúsítványt. Ügyeljen arra, használja az ügyféltanúsítvány ujjlenyomatát.

6. Nyissa meg *profileinfo.txt* egy szövegszerkesztőben. Az ügyfél (gyermek) tanúsítvány ujjlenyomatának beszerzéséhez válassza ki a szöveget, többek között, és a "----BEGIN tanúsítvány---" között "----END tanúsítvány---" a gyermek és tanúsítvány, és másolja azt. A gyermek tanúsítvány azonosíthatja a subject = megnézzük / sor.

7. Nyissa meg a *vpnconfig.ovpn* fájlt és keresse meg az alább látható szakaszt. Cserélje le minden között a és a "cert" és "/ cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Egy szövegszerkesztőben nyissa meg a profileinfo.txt. A titkos kulcs lekéréséhez válassza ki a szöveget, például és azok között "----BEGIN titkos kulcs---" és "----END PRIVATE KEY----", és másolja azt.

9. Nyissa meg a vpnconfig.ovpn fájlt egy szövegszerkesztőben, és ebben a szakaszban található. Illessze be a titkos kulcsot minden között cseréje és a "kulcs" és "/ kulcs".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ne módosítson semmilyen egyéb mezőt. Az ügyfélbemenet kitöltött konfigurációjával csatlakozhat a VPN-hez.
11. Csatlakozhat a parancssorból, írja be a következő parancsot:
  
    ```
    sudo openvpn –-config <name and path of your VPN profile file>
    ```
12. Csatlakozhat a grafikus felhasználói felülettel, nyissa meg a rendszer beállításait.
13. Kattintson a **+** hozzáadása egy új VPN-kapcsolat.
14. A **hozzáadása VPN**, válasszon **Importálás fájlból...**
15. Keresse meg a profilfájlt, és kattintson duplán vagy kivételezést **nyílt**.
16. Kattintson a **Hozzáadás** a a **hozzáadása VPN** ablak.
  
    ![Importálás fájlból](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. A Kapcsolódás a VPN engedélyezésével **ON** a a **hálózati beállítások** lapon vagy a tálcán található hálózat ikonra.

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy a VPN-ügyfél egy másik virtuális hálózatban (éles) található erőforrások hozzáférhet, majd kövesse a [VNet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk egy vnet – vnet kapcsolat beállításához. Ügyeljen arra, hogy a BGP engedélyezéséhez az átjárók és a kapcsolatokat, ellenkező esetben nem forgalom.

**"OpenVPN" a OpenVPN Inc. védjegye.**
