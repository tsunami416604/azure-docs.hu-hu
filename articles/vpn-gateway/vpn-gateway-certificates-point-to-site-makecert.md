---
title: "Készítése és a pont-pont tanúsítványok exportálása: MakeCert: Azure |} Microsoft Docs"
description: "Hozzon létre egy önaláírt legfelső szintű tanúsítványt, a nyilvános kulcs exportálásának és MakeCert használatával ügyféltanúsítványok előállításához."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: b2f31761e4560cf4b9b9a5b92f5de9982a663a75
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Létrehozása és exportálása a MakeCert használatával pont – hely kapcsolatok tanúsítványai

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozzon létre egy önaláírt legfelső szintű tanúsítványt, és a MakeCert használatával ügyféltanúsítványok előállításához. Ha a keresett pont-hely konfigurációs lépések, például a legfelső szintű tanúsítványok feltöltéséről válassza az "konfigurálása pont-pont" cikkekben az alábbi listából:

> [!div class="op_single_selector"]
> * [Hozzon létre önaláírt tanúsítványokat - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Hozzon létre önaláírt tanúsítványokat - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Pont – hely - Resource Manager - Azure-portál konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Pont – hely - erőforrás-kezelő - PowerShell konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Pont – hely - klasszikus - Azure-portál konfigurálása](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

Azt javasoljuk, amíg a [Windows 10 PowerShell lépéseket](vpn-gateway-certificates-point-to-site.md) a tanúsítványok létrehozásához, egy nem kötelező módszerként nyújtunk MakeCert útmutatóhoz. A tanúsítványok módszerek használatával generáló telepíthető [bármely támogatott ügyfél operációs rendszer](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). Azonban a MakeCert a következő korlátozás van:

* MakeCert elavult. Ez azt jelenti, hogy sikerült-e az eszköz eltávolítani bármely pontján. Már létrehozott MakeCert használatával tanúsítványokat nem érinti, ha a MakeCert már nem érhető el. MakeCert csak használja a tanúsítványok nem érvényesítéséhez mechanizmusaként készítéséhez.

## <a name="rootcert"></a>Hozzon létre egy önaláírt legfelső szintű tanúsítványt

A következő lépések bemutatják a hozzon létre egy önaláírt tanúsítványt a MakeCert használatával. Ezeket a lépéseket csak vonatkozó központi telepítési modellt. Akkor érvényesek az erőforrás-kezelő és a klasszikus.

1. Töltse le és telepítse [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. A telepítés után találhatja meg a makecert.exe segédprogramot az elérési úton: "C:\Program Files (x86) \Windows Kits\10\bin\<architektúrája >". Bár, lehetséges, hogy azt egy másik helyre telepítette. Nyisson meg egy parancssort rendszergazdaként, és keresse meg a helyet a MakeCert segédprogram. A következő példában a megfelelő hely beállítása használhatja:

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. Hozzon létre és telepítsen egy tanúsítványt a számítógép személyes tanúsítványtárolójába. Az alábbi példakód létrehozza a megfelelő *.cer* P2S konfigurálásakor az Azure-bA feltöltött fájl. "P2SRootCert" és "P2SRootCert.cer" cserélje le a tanúsítványt használni kívánt nevét. A tanúsítvány található a "tanúsítványok - aktuális User\Personal\Certificates".

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
  ```

## <a name="cer"></a>Exportálja a nyilvános kulcsot (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

A exported.cer fájlt fel kell tölteni, az Azure-bA. Útmutatásért lásd: [egy pont – hely kapcsolat beállítása](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Egy további megbízható legfelső szintű tanúsítvány hozzáadása, lásd: [ebben a szakaszban](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) a cikk.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Exportálhatja az önaláírt tanúsítvány és titkos kulcs tárolható (nem kötelező)

Érdemes lehet önaláírt legfelső szintű tanúsítványának exportálása és tárolja biztonságos helyen. Ha kell, később egy másik számítógépre telepítse, és további ügyféltanúsítványok előállításához vagy egy másik .cer-fájl exportálását. Az önaláírt legfelső szintű tanúsítvány exportálása a .pfx fájlhoz, válassza ki a legfelső szintű tanúsítványt, és használja ugyanazokat a lépéseket, a [ügyféltanúsítvány exportálása](#clientexport).

## <a name="create-and-install-client-certificates"></a>Hozzon létre és telepítsen ügyfél-tanúsítványok

Az önaláírt tanúsítvány nem telepíti közvetlenül az ügyfélszámítógépen. Ügyféltanúsítvány generálása az önaláírt tanúsítvány szükséges. Majd exportálja és az az ügyfélszámítógépre telepíti az ügyféltanúsítványt. Az alábbi lépéseket kell nem vonatkozó központi telepítési modellt. Akkor érvényesek az erőforrás-kezelő és a klasszikus.

### <a name="clientcert"></a>Ügyfél-tanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Hozzon létre egy ügyféltanúsítványt a önaláírt legfelső szintű tanúsítvány és exportálni, és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés meghiúsul. 

A következő lépések végigvezetik történő tanúsítványgenerálás során egy ügyfél egy önaláírt legfelső szintű tanúsítványt. Előfordulhat, hogy több ügyféltanúsítványt generálása ugyanazon főtanúsítványhoz. Az alábbi lépéseket követve ügyféltanúsítványok létrehozásakor az ügyféltanúsítvány automatikusan települ azon a számítógépen, amelyet a tanúsítvány létrehozásához használt. Ha egy ügyfél-tanúsítványt egy másik ügyfélszámítógépen telepíteni szeretné, exportálhatja a tanúsítványt.
 
1. Ugyanazon a számítógépen, amely az önaláírt tanúsítvány létrehozásához használt nyisson meg egy parancssort rendszergazdaként.
2. Módosíthatja, és hozza létre az ügyfél a minta futtatásához.
  * Változás *"P2SRootCert"* az ügyféltanúsítványt a generál önaláírt legfelső szintű nevét. Győződjön meg arról, hogy a legfelső szintű tanúsítvány, amely bármilyen nevet használ a "CN =' érték volt a önaláírt legfelső szintű létrehozásakor megadott.
  * Változás *P2SChildCert* kell ügyféltanúsítványt generálásához nevét.

  Ha futtatja a következő példa azt a módosítása nélkül, eredménye a személyes tanúsítványtárolóban lett létrehozva, a legfelső szintű tanúsítvány P2SRootCert P2SChildcert nevű ügyfél-tanúsítványt.

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
  ```

### <a name="clientexport"></a>Ügyfél-tanúsítvány exportálása

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>Az exportált ügyféltanúsítvány telepítése

Ügyfél-tanúsítvány telepítéséhez tekintse át [telepíthet ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>További lépések

A pont-hely konfigurációs folytatásához. 

* A **erőforrás-kezelő** telepítési modell lépéseket lásd: [konfigurálása P2S natív Azure-alapú hitelesítést használó](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* A **klasszikus** telepítési modell lépéseket lásd: [egy virtuális hálózat (klasszikus) pont – hely típusú VPN-kapcsolat konfigurálva](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

P2S hibaelhárítási információkért [Azure hibaelhárítási pont – hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).