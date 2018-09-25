---
title: OpenVPN ügyfelek konfigurálása Azure VPN Gateway |} A Microsoft Docs
description: Az Azure VPN Gateway OpenVPN ügyfelek konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977842"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Azure VPN Gateway (előzetes verzió) OpenVPN ügyfelek konfigurálása

Ez a cikk segít OpenVPN ügyfelek konfigurálása.

> [!IMPORTANT]
> A nyilvános előzetes verzióra nem vonatkozik szolgáltatói szerződés, és nem használható éles számítási feladatokra. Előfordulhat, hogy néhány funkció nem támogatott, korlátozott képességekkel rendelkezik, vagy nem érhető el minden Azure-helyen. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy végrehajtotta a OpenVPN konfigurálása a VPN-átjárót. További információkért lásd: [OpenVPN konfigurálása Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Windows ügyfelek

1. Töltse le és telepítse a hivatalos a OpenVPN ügyfél [OpenVPN webhely](https://openvpn.net/index.php/open-source/downloads.html).
2. Töltse le az átjáró VPN-profilt. Ezt megteheti az Azure Portalon, a pont – hely konfiguráció lapján vagy a "New-AzureRmVpnClientConfiguration" a PowerShellben.
3. Csomagolja ki a profilhoz. Ezután nyissa meg a vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben OpenVPN mappából.
4. Adja meg a P2S-ügyfél tanúsítvány szakaszában P2S ügyfél tanúsítvány nyilvános kulcsával a Base64 kódolás szerint. A PEM formátumú tanúsítvány egyszerűen megnyithatja a .cer fájlt, másolja át a base64-kulcs között a tanúsítvány fejlécek. Tekintse meg itt a tanúsítványt a kódolt nyilvános kulcs beszerzése.
5. Adja meg a titkos kulcs szakasz P2S ügyfél tanúsítvány titkos kulcsával a Base64 kódolás szerint. Bontsa ki a titkos kulcs kapcsolatos információkért lásd: [kulcs exportálása](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Ne változtassa meg a többi mezőt. Csatlakozhat a tematikus ügyfél bemeneti konfigurációban a VPN-t.
7. Másolja a vpnconfig.ovpn fájl C:\Program Files\OpenVPN\config mappába.
8. Kattintson a jobb gombbal az ikonra a tálcán, majd kattintson a csatlakozás OpenVPN.

## <a name="mac"></a>Mac-ügyfél

1. Ügyfél letöltése és telepítése egy OpenVPN, mint például [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Töltse le az átjáró VPN-profilt. Ezt megteheti a pont – hely konfiguráció lapon, az Azure Portalon, vagy a "New-AzureRmVpnClientConfiguration" a PowerShell használatával.
3. Csomagolja ki a profilhoz. Nyissa meg a vpnconfig.ovpn konfigurációs fájlt a Jegyzettömbben OpenVPN mappából.
4. Adja meg a P2S-ügyfél tanúsítvány szakaszában P2S ügyfél tanúsítvány nyilvános kulcsával a Base64 kódolás szerint. A PEM formátumú tanúsítvány egyszerűen megnyithatja a .cer fájlt, másolja át a base64-kulcs között a tanúsítvány fejlécek. Lásd: [nyilvános kulcs exportálása](vpn-gateway-certificates-point-to-site.md#cer) a tanúsítványt a kódolt nyilvános kulcs lekérésével kapcsolatos információk.
5. Adja meg a titkos kulcs szakasz P2S ügyfél tanúsítvány titkos kulcsával a Base64 kódolás szerint. Lásd: [a titkos kulcs exportálását választom](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) információ a titkos kulcs kicsomagolása.
6. Ne változtassa meg a többi mezőt. Csatlakozhat a tematikus ügyfél bemeneti konfigurációban a VPN-t.
7. Kattintson duplán a profil fájlt tunnelblik a profil létrehozásához.
8. Indítsa el az alkalmazások mappából Tunnelblik.
9. Kattintson a tálcán Tunneblik ikonra, és válasszon csatlakozzon.

## <a name="linux"></a>Linux-ügyfelek

1. Nyisson meg egy új terminál-munkamenetben. Megnyithatja a egy új munkamenetben nyomja le az "Ctrl + Alt + t" egyszerre
2. Adja meg a szükséges összetevők telepítése a következő parancsot:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Töltse le az átjáró VPN-profilt. Ezt megteheti a pont – hely konfiguráció lapon, az Azure Portalon, vagy a "New-AzureRmVpnClientConfiguration" a PowerShell használatával.
4. Adja meg a titkos kulcs szakasz P2S ügyfél tanúsítvány titkos kulcsával a Base64 kódolás szerint. Lásd: [a titkos kulcs exportálását választom](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) információ a titkos kulcs kicsomagolása.
5. Csatlakozás a parancssorból, írja be a következőt:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Csatlakozhat a grafikus felhasználói felülettel, nyissa meg a rendszer beállításait.
6. Kattintson a **+** hozzáadása egy új VPN-kapcsolat.
7. A **hozzáadása VPN**, válasszon **Importálás fájlból...**
8. Keresse meg a profilfájlt, és kattintson duplán vagy kivételezést **megnyitása**
9. Kattintson a **Hozzáadás** a a **hozzáadása VPN** ablak.
  
  ![Importálás fájlból](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. A Kapcsolódás a VPN engedélyezésével **ON** a a **hálózati beállítások** lapon vagy a tálcán található hálózat ikonra.

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy a VPN-ügyfél egy másik virtuális hálózatban (éles) található erőforrások hozzáférhet, majd kövesse a [Vnet – VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) cikk egy vnet – vnet kapcsolat beállításához. Ellenőrizze a BGP engedélyezéséhez az átjárók és a kapcsolatokat, ellenkező esetben nem forgalom.
