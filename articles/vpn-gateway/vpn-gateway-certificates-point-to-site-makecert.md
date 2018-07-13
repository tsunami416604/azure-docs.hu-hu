---
title: 'Tanúsítványok létrehozása és exportálása pont – hely számára: MakeCert: Azure |} A Microsoft Docs'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és létrehoz ügyféltanúsítványokat a MakeCert használatával.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: b2f31761e4560cf4b9b9a5b92f5de9982a663a75
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38651787"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>A MakeCert használatával, pont – hely kapcsolatokhoz a tanúsítványok létrehozása és exportálása

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozzon létre egy önaláírt tanúsítványt, és a MakeCert használatával ügyféltanúsítványokat. Ha pont – hely konfigurációs lépések, például a legfelső szintű tanúsítványok feltöltése keres az alábbi listából válassza ki a "Konfigurálás pont – hely" cikkekben:

> [!div class="op_single_selector"]
> * [Önaláírt tanúsítványok létrehozása – PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Önaláírt tanúsítványok létrehozása – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Pont – hely – Resource Manager – Azure portal konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Pont – hely – Resource Manager – a PowerShell konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Pont – hely – klasszikus – Azure portal konfigurálása](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Bár javasoljuk a [Windows 10-es PowerShell-lépések](vpn-gateway-certificates-point-to-site.md) a tanúsítványok létrehozásához, egy nem kötelező módszerként kínálunk Makecertre vonatkozó utasítások. A tanúsítványok, létrehozhat módszerek használatával is telepíthető [bármely támogatott ügyfél operációs rendszer](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Azonban a MakeCert van a következő korlátozást:

* MakeCert elavult. Ez azt jelenti, hogy ezt az eszközt bármikor eltávolítása sikerült. Ha már nem érhető el a MakeCert már létrehozott MakeCert használatával tanúsítványokat nem befolyásolja. Létrehozásához a tanúsítványok nem érvényesítésekor mechanizmusként csak használatos a MakeCert.

## <a name="rootcert"></a>Hozzon létre egy önaláírt főtanúsítványt.

A következő lépések bemutatják, hogyan hozhat létre egy önaláírt tanúsítványt a MakeCert használatával. Ezeket a lépéseket nem üzemi specifikus el. Akkor érvényesek az erőforrás-kezelő és a klasszikus modellt.

1. Töltse le és telepítse [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. A telepítés után általában megtalálható a makecert.exe segédprogramot a elérési úton: "C:\Program Files (x86) \Windows Kits\10\bin\<arch >". Bár, lehetséges, hogy egy másik helyre telepítve lett. Nyisson meg egy parancssort rendszergazdaként, és keresse meg a helyet, a MakeCert segédprogram. Használhatja a következő példában a megfelelő hely módosítása:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Hozzon létre és telepítsen egy tanúsítványt a számítógép személyes tanúsítványtárolójába. Az alábbi példa létrehoz egy megfelelő *.cer* P2S konfigurálásakor az Azure-bA feltöltött fájl. "P2SRootCert" és "p2srootcert.cer nevet" cserélje le a tanúsítványhoz használni kívánt nevét. A tanúsítvány található a "tanúsítványok – aktuális felhasználó\személyes\tanúsítványok".

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Exportálja a nyilvános kulcsot (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Az Azure-bA a exported.cer fájlt kell feltölteni. Útmutatásért lásd: [pont – hely kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). További megbízható főtanúsítvány hozzáadásával, lásd: [ebben a szakaszban](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) a cikk.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportálhatja az önaláírt tanúsítvány és titkos kulcsot tárolni (nem kötelező)

Előfordulhat, hogy szeretné önaláírt főtanúsítvány exportálása és tárolja biztonságos helyen. Ha szükséges, később is telepítheti egy másik számítógépen és további ügyféltanúsítványokat, vagy egy másik .cer-fájl exportálása. Önaláírt főtanúsítvány exportálása a .pfx fájlként, válassza ki a legfelső szintű tanúsítványt, és ugyanazokat a lépéseket ismertetett módon kihasználhassák [ügyféltanúsítvány exportálásához](#clientexport).

## <a name="create-and-install-client-certificates"></a>Hozzon létre, és az ügyféltanúsítványok telepítése

Az önaláírt tanúsítvány nem telepít közvetlenül az ügyfélszámítógépen. Létre kell hoznia egy ügyfél-tanúsítványt az önaláírt tanúsítvány. Majd exportálja és az az ügyféltanúsítvány telepítése az ügyfélszámítógépre. A következő lépéseket nem üzemi specifikus el. Akkor érvényesek az erőforrás-kezelő és a klasszikus modellt.

### <a name="clientcert"></a>Ügyfél-tanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a önaláírt főtanúsítványból és exportálni, és telepítheti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés meghiúsul. 

A következő lépések végigvezetik egy önaláírt főtanúsítványból ügyféltanúsítvány hozható létre. Előfordulhat, hogy több ügyféltanúsítványt generálhatnak ugyanazt a legfelső szintű tanúsítványt. Az alábbi lépéseket követve ügyféltanúsítványok generál, amikor az ügyfél-tanúsítványt automatikusan települ azon a számítógépen, a tanúsítvány létrehozásához használt. Ha telepíthet ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne, exportálhatja a tanúsítványt.
 
1. Ugyanazon a számítógépen, amelyet az önaláírt tanúsítvány létrehozásához használt nyisson meg egy parancssort rendszergazdaként.
2. Módosíthatja, és futtassa a mintát az ügyféltanúsítványokat.
  * Változás *"P2SRootCert"* az önaláírt főtanúsítványok meg az ügyféltanúsítványt a létrehozó nevére. Ellenőrizze, hogy a legfelső szintű tanúsítvány, amely bármilyen nevét használja a "CN =" érték volt, amikor létrehozta az önaláírt főtanúsítványok megadott.
  * Változás *P2SChildCert* kell ügyféltanúsítvány létrehozásához használni kívánt nevet.

  Ha az alábbi példa azt módosítása nélkül futtatja, ez nevű P2SChildcert a személyes tanúsítványtárolójában P2SRootCert főtanúsítványból létrehozott ügyféltanúsítvány.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Ügyféltanúsítvány exportálásához

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Az exportált ügyféltanúsítvány telepítése

Ügyféltanúsítvány telepítése: [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>További lépések

A pont – hely konfiguráció folytatásához. 

* A **Resource Manager** üzembe helyezési modell lépéseiért lásd: [P2S konfigurálása Azure natív tanúsítványalapú hitelesítésének használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* A **klasszikus** üzembe helyezési modell lépéseiért lásd: [(klasszikus) virtuális hálózathoz pont – hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.