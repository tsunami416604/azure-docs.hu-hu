---
title: 'Tanúsítványok létrehozása és exportálása pont – hely számára: PowerShell: Azure |} A Microsoft Docs'
description: Hozzon létre egy önaláírt főtanúsítványt, exportálja a nyilvános kulcsot és PowerShell használatával a Windows 10-es vagy Windows Server 2016 ügyféltanúsítványokat.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: e635eddabe239268829d3a907df10392c7e1b6a3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850958"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Tanúsítványok létrehozása és exportálása pont – hely a PowerShell használatával

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozzon létre egy önaláírt főtanúsítványt, és a PowerShell használatával a Windows 10-es vagy Windows Server 2016 ügyféltanúsítványokat. Ha ugyanazt a tanúsítványt utasításokat keres, tekintse meg [tanúsítványok – Linux](vpn-gateway-certificates-point-to-site-linux.md) vagy [tanúsítványok – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Ez a cikk a Windows 10-es vagy Windows Server 2016 rendszert futtató számítógépen kell hajtsa végre a lépéseket. A PowerShell-parancsmagok használatával is létrehozhat tanúsítványokat az operációs rendszer részét képezik, és a Windows más verziói nem működnek. A Windows 10-es vagy Windows Server 2016 számítógép csak akkor van szükség a tanúsítványok előállításához. A tanúsítványok jönnek létre, miután feltölti őket, vagy telepítse őket a támogatott ügyfél operációs rendszereken. 

Ha nem rendelkezik hozzáféréssel Windows 10-es vagy Windows Server 2016 számítógéphez, akkor használhatja [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) is létrehozhat tanúsítványokat. A tanúsítványok, létrehozhat módszerek használatával is telepíthető bármelyik [támogatott](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) ügyfél operációs rendszerét.

## <a name="rootcert"></a>1. Hozzon létre egy önaláírt főtanúsítványt.

A New-SelfSignedCertificate-parancsmag segítségével hozzon létre egy önaláírt főtanúsítványt. További információkért lásd: [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Windows 10-es vagy Windows Server 2016 rendszert futtató számítógépről nyissa meg a Windows PowerShell-konzolt emelt szintű jogosultságokkal. Ezek a példák nem működik az Azure Cloud shellben "Próbálja ki". Ezekben a példákban helyileg kell futtatni.
2. A következő példa használatával hozza létre az önaláírt főtanúsítványt. Az alábbi példa létrehoz egy önaláírt főtanúsítványt a "P2SRootCert", amely automatikusan települ a "Tanúsítványok – aktuális felhasználó\személyes\tanúsítványok" nevű. A tanúsítvány megtekintéséhez nyissa meg *certmgr.msc*, vagy *felhasználói tanúsítványok kezelése*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

## <a name="clientcert"></a>2. Ügyféltanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a önaláírt főtanúsítványból és exportálni, és telepítheti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés meghiúsul. 

A következő lépések végigvezetik egy önaláírt főtanúsítványból ügyféltanúsítvány hozható létre. Előfordulhat, hogy több ügyféltanúsítványt generálhatnak ugyanazt a legfelső szintű tanúsítványt. Az alábbi lépéseket követve ügyféltanúsítványok generál, amikor az ügyfél-tanúsítványt automatikusan települ azon a számítógépen, a tanúsítvány létrehozásához használt. Ha telepíthet ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne, exportálhatja a tanúsítványt.

A példák a New-SelfSignedCertificate parancsmag segítségével hozzon létre egy ügyféltanúsítvány, amely egy év múlva lejár. További paraméter információk, például az ügyféltanúsítvány eltérő lejárati értékét: [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>1. példa

Ez a példa az előző szakaszban a deklarált "$cert" változó használja. Ha az önaláírt főtanúsítvány létrehozása után a PowerShell-konzol lezárva, vagy egy új PowerShell-konzol munkamenetet ügyféloldali tanúsítványokat készíti, szereplő lépések segítségével [2. példa](#ex2).

Módosíthatja, és futtasson le az ügyféltanúsítvány létrehozása. Ha az alábbi példa azt módosítása nélkül futtatja, ez "P2SChildCert" nevű ügyféltanúsítványt.  Ha meg szeretné nevezi a tanúsítványt, módosítsa a CN-érték. Ne módosítsa a TextExtension ebben a példában futtatásakor. Az ügyféltanúsítvány, azt automatikusan települ a "Tanúsítványok – aktuális felhasználó\személyes\tanúsítványok" a számítógépre.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>2. példa

Ha további ügyféltanúsítványok hoz létre, vagy nem használ, amelyet az önaláírt tanúsítvány létrehozásához használt egyetlen PowerShell-munkameneten, használja az alábbi lépéseket:

1. Azonosítsa az önaláírt főtanúsítványt, hogy telepítve van a számítógépen. Ez a parancsmag a számítógépre telepített tanúsítványok listáját adja vissza.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. A tulajdonos nevét, a visszaadott listában keresse meg, majd másolja az ujjlenyomatot, amely annak a mellette található. A következő példában két tanúsítványokat is. A CN-név az önaláírt főtanúsítványt, amelyből gyermek tanúsítványt létrehozni kívánt nevét. Ebben az esetben ez a "P2SRootCert".

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. Deklarálja a főtanúsítvány az előző lépésből származó ujjlenyomat használatával egy változót. UJJLENYOMAT cserélje le az ujjlenyomatot a legfelső szintű tanúsítvány, amelyből gyermek tanúsítványt létrehozni kívánja.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Ha például az ujjlenyomat P2SRootCert használja az előző lépésben, a változó a következőhöz hasonló:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Módosíthatja, és futtasson le az ügyféltanúsítvány létrehozása. Ha az alábbi példa azt módosítása nélkül futtatja, ez "P2SChildCert" nevű ügyféltanúsítványt. Ha meg szeretné nevezi a tanúsítványt, módosítsa a CN-érték. Ne módosítsa a TextExtension ebben a példában futtatásakor. Az ügyféltanúsítvány, azt automatikusan települ a "Tanúsítványok – aktuális felhasználó\személyes\tanúsítványok" a számítógépre.

  ```powershell
  New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="cer"></a>3. A legfelső szintű tanúsítvány nyilvános kulcsát (.cer) exportálása

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportálhatja az önaláírt főtanúsítványok és a titkos kulcsot tárolni (nem kötelező)

Érdemes önaláírt főtanúsítvány exportálása, és tárolja biztonságos biztonsági mentés. Ha kell, később is telepítheti egy másik számítógépen, és további ügyféltanúsítványok előállítása. Önaláírt főtanúsítvány exportálása a .pfx fájlként, válassza ki a legfelső szintű tanúsítványt, és ugyanazokat a lépéseket ismertetett módon kihasználhassák [ügyféltanúsítvány exportálásához](#clientexport).

## <a name="clientexport"></a>4. Az ügyféltanúsítvány exportálása

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Exportált ügyféltanúsítvány telepítése

Minden P2S-kapcsolaton keresztül a virtuális hálózathoz csatlakozó ügyfelet a rendszer helyben telepítendő ügyféltanúsítványt igényel.

Ügyféltanúsítvány telepítése: [telepítheti egy ügyféltanúsítványt, a pont – hely kapcsolatok](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Folytassa a P2S-konfigurációs lépések

A pont – hely konfiguráció folytatásához.

* A **Resource Manager** üzembe helyezési modell lépéseiért lásd: [P2S konfigurálása Azure natív tanúsítványalapú hitelesítésének használatával](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* A **klasszikus** üzembe helyezési modell lépéseiért lásd: [(klasszikus) virtuális hálózathoz pont – hely VPN-kapcsolat konfigurálása](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* P2S hibaelhárítási információkért lásd: [pont – hely kapcsolatok hibaelhárítása Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
