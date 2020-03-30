---
title: 'Azure VPN-átjáró: & exportálási tanúsítványok létrehozása a P2S-hez: MakeCert'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot, és hozzon létre ügyféltanúsítványokat a MakeCert használatával.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: ad2ab31e6771efc54238d5747863fa2a9bb2f356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75833972"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Tanúsítványok létrehozása és exportálása a point-to-site kapcsolatokhoz a MakeCert használatával

A helyek közötti kapcsolatok tanúsítványokat használnak a hitelesítéshez. Ez a cikk bemutatja, hogyan hozhat létre önaláírt főtanúsítványt, és hogyan hozhat létre ügyféltanúsítványokat a MakeCert használatával. Ha különböző tanúsítványutasításokat keres, olvassa el a [Tanúsítványok - PowerShell](vpn-gateway-certificates-point-to-site.md) vagy [tanúsítványok - Linux](vpn-gateway-certificates-point-to-site-linux.md).

Bár azt javasoljuk, hogy a [Windows 10 PowerShell lépéseket](vpn-gateway-certificates-point-to-site.md) a tanúsítványok létrehozásához, ezeket a MakeCert utasításokat opcionális módszerként biztosítjuk. A bármelyik módszerrel létrehozott tanúsítványok [bármely támogatott ügyféloperációs rendszerre](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)telepíthetők. A MakeCert azonban a következő korlátozásokkal rendelkezik:

* A MakeCert elavult. Ez azt jelenti, hogy ez az eszköz bármikor eltávolítható. A MakeCert használatával már létrehozott tanúsítványokat ez nem érinti, ha a MakeCert már nem érhető el. A MakeCert csak a tanúsítványok létrehozására szolgál, érvényesítési mechanizmusként nem.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Önaláírt főtanúsítvány létrehozása

A következő lépések bemutatják, hogyan hozhat létre önaláírt tanúsítványt a MakeCert használatával. Ezek a lépések nem központi telepítési modell-specifikus. Az Erőforrás-kezelőre és a klasszikusra egyaránt érvényesek.

1. Töltse le és telepítse [makecert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx).
2. A telepítés után a makecert.exe segédprogram általában a következő elérési út alatt található: "C:\Program Files\<(x86)\Windows Kits\10\bin arch>". Bár lehetséges, hogy egy másik helyre telepítették. Nyisson meg egy parancssort rendszergazdaként, és keresse meg a MakeCert segédprogram helyét. A következő példát használhatja a megfelelő helyhez igazítva:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Tanúsítvány létrehozása és telepítése a számítógép Személyes tanúsítványtárolójában. A következő példa létrehoz egy megfelelő *.cer* fájlt, amelyet a P2S konfigurálásakor tölt fel az Azure-ba. Cserélje le a "P2SRootCert" és a "P2SRootCert.cer" nevet a tanúsítványhoz használni kívánt névre. A tanúsítvány a "Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok" mappában található.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>A nyilvános kulcs exportálása (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Az exported.cer fájlt fel kell tölteni az Azure-ba. További információt a [Pont-hely kapcsolat konfigurálása című témakörben talál.](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile) További megbízható főtanúsítvány hozzáadásáról a cikk [ezen szakaszában](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) olvashat.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Az önaláírt tanúsítvány és a személyes kulcs exportálása a tároláshoz (nem kötelező)

Előfordulhat, hogy exportálni szeretné az önaláírt főtanúsítványt, és biztonságosan tárolni. Szükség esetén később telepítheti egy másik számítógépre, és több ügyféltanúsítványt hozhat létre, vagy exportálhat egy másik .cer fájlt. Ha az önaláírt főtanúsítványt .pfx-ként szeretné exportálni, jelölje ki a főtanúsítványt, és kövesse az [ügyféltanúsítvány exportálása](#clientexport)című részben leírtaknak leírt lépéseket.

## <a name="create-and-install-client-certificates"></a>Ügyféltanúsítványok létrehozása és telepítése

Az önaláírt tanúsítványt nem telepíti közvetlenül az ügyfélszámítógépre. Létre kell hoznia egy ügyféltanúsítványt az önaláírt tanúsítványból. Ezután exportálja és telepíti az ügyféltanúsítványt az ügyfélszámítógépre. A következő lépések nem központi telepítési modell-specifikus. Az Erőforrás-kezelőre és a klasszikusra egyaránt érvényesek.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Ügyféltanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az önaláírt főtanúsítványból ügyféltanúsítványt hoz létre, majd exportálja és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés sikertelen lesz. 

A következő lépések végigvezetik az ügyféltanúsítvány önaláírt főtanúsítványból történő létrehozásán. Ugyanattól a főtanúsítványtól több ügyféltanúsítvány is létrehozható. Ha az alábbi lépésekkel állít elő ügyféltanúsítványokat, az ügyféltanúsítvány automatikusan települ azon a számítógépen, amelyen a tanúsítványt létrehozta. Ha egy másik ügyfélszámítógépre szeretne ügyféltanúsítványt telepíteni, exportálhatja a tanúsítványt.
 
1. Ugyanazon a számítógépen, amelyen az önaláírt tanúsítvány t, nyisson meg egy parancssort rendszergazdaként.
2. Módosítsa és futtassa a mintát az ügyféltanúsítvány létrehozásához.
   * Módosítsa a *"P2SRootCert"* szót annak az önaláírt gyökérnek a nevére, amelyből az ügyféltanúsítványt létrehoz. Győződjön meg arról, hogy a főtanúsítvány nevét használja, amely a "CN=" értéktől függetlenül az önaláírt gyökér létrehozásakor megadott érték.
   * Módosítsa a *P2SChildCert nevet* arra a névre, amelynek ügyféltanúsítványt szeretne létrehozni.

   Ha a következő példát anélkül futtatja, hogy módosítaná, az eredmény egy P2SChildcert nevű ügyféltanúsítvány a Személyes tanúsítványtárolóban, amely a P2SRootCert főtanúsítványból lett létrehozva.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Ügyféltanúsítvány exportálása

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Exportált ügyféltanúsítvány telepítése

Ügyféltanúsítvány telepítéséhez olvassa el az Ügyféltanúsítvány telepítése című [témakört.](point-to-site-how-to-vpn-client-install-azure-cert.md)

## <a name="next-steps"></a>További lépések

Folytassa a pont-hely konfigurációval. 

* Az **Erőforrás-kezelő** telepítési modelljének [lépéseiről a P2S konfigurálása natív Azure-tanúsítványhitelesítéssel](vpn-gateway-howto-point-to-site-resource-manager-portal.md)című témakörben talál.
* A **klasszikus** telepítési modell lépéseit a [Point-to-Site VPN-kapcsolat konfigurálása virtuális hálózattal (klasszikus) című](vpn-gateway-howto-point-to-site-classic-azure-portal.md)témakörben talál.

A pont–hely hibaelhárítási információiért tekintse át az [Azure pont–hely kapcsolatok hibaelhárításával](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) foglalkozó cikket.