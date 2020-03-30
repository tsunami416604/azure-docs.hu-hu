---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e85dc8c079205484db9b7b7c43a0086f69feb3be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059924"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Önaláírt főtanúsítvány létrehozása

Az Új-SelfSignedCertificate parancsmag használatával önaláírt főtanúsítványt hozhat létre. További paraméterrel kapcsolatos információkért lásd: [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Windows 10 vagy Windows Server 2016 rendszert futtató számítógépről nyisson meg egy emelt szintű jogosultságokkal rendelkező Windows PowerShell-konzolt. Ezek a példák nem működnek az Azure Cloud Shell "Try It" szolgáltatásban. Ezeket a példákat helyileg kell futtatnia.
2. Az alábbi példával hozd létre az önaláírt főtanúsítványt. A következő példa létrehoz egy "P2SRootCert" nevű, önaláírt főtanúsítványt, amely automatikusan telepítve van a "Tanúsítványok-aktuális felhasználó\Személyes\Tanúsítványok" mappában. A tanúsítvány megtekintéséhez nyissa meg a *certmgr.msc*vagy *a Felhasználói tanúsítványok kezelése*lehetőséget.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. Hagyja nyitva a PowerShell-konzolt, ha közvetlenül a főtanúsítvány létrehozása után szeretne ügyféltanúsítványt létrehozni.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Ügyféltanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az önaláírt főtanúsítványból ügyféltanúsítványt hoz létre, majd exportálja és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés sikertelen lesz. 

A következő lépések végigvezetik az ügyféltanúsítvány önaláírt főtanúsítványból történő létrehozásán. Ugyanattól a főtanúsítványtól több ügyféltanúsítvány is létrehozható. Ha az alábbi lépésekkel állít elő ügyféltanúsítványokat, az ügyféltanúsítvány automatikusan települ azon a számítógépen, amelyen a tanúsítványt létrehozta. Ha egy másik ügyfélszámítógépre szeretne ügyféltanúsítványt telepíteni, exportálhatja a tanúsítványt.

A példák az Új-SelfSignedCertificate parancsmag használatával egy ügyféltanúsítványt hoz létre, amely egy év múlva lejár. További paraméterrel kapcsolatos információkért, például az ügyféltanúsítvány másik lejárati értékének beállításáért lásd: [Új-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>1. példa – A PowerShell konzolmunkamenete még mindig nyitva van

Ebben a példában, ha nem zárta be a PowerShell-konzol létrehozása után az önaláírt főtanúsítvány. Ez a példa az előző szakaszból folytatódik, és a deklarált "$cert" változót használja. Ha az önaláírt főtanúsítvány létrehozása után bezárta a PowerShell-konzolt, vagy további ügyféltanúsítványokat hoz létre egy új PowerShell-konzolmunkamenetben, kövesse a [2.](#ex2)

Módosítsa és futtassa a példát egy ügyféltanúsítvány létrehozásához. Ha a következő példát anélkül futtatja, hogy módosítaná, az eredmény egy "P2SChildCert" nevű ügyféltanúsítvány.  Ha a gyermektanúsítványt valami másnak szeretné elnevezni, módosítsa a KN-értéket. Ne módosítsa a TextExtension-t, amikor ezt a példát futtatja. A létrehozott ügyféltanúsítvány automatikusan települ a számítógépen a "Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok" mappába.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>2. példa – Új PowerShell konzolmunkamenet

Ha további ügyféltanúsítványokat hoz létre, vagy nem ugyanazt a PowerShell-munkamenetet használja, amelyet az önaláírt főtanúsítvány létrehozásához használt, kövesse az alábbi lépéseket:

1. Azonosítsa a számítógépre telepített önaláírt főtanúsítványt. Ez a parancsmag a számítógépre telepített tanúsítványok listáját adja vissza.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Keresse meg a tulajdonos nevét a visszaadott listából, majd másolja a mellette lévő ujjlenyomatot egy szövegfájlba. A következő példában két tanúsítvány található. A KN-név annak az önaláírt főtanúsítványnak a neve, amelyből gyermektanúsítványt szeretne létrehozni. Ebben az esetben a "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Deklarálegy változót a főtanúsítványhoz az előző lépés ből származó ujjlenyomat használatával. Cserélje le az UJJLENYOMATOT annak a főtanúsítványnak az ujjlenyomatára, amelyből gyermektanúsítványt szeretne létrehozni.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Ha például az előző lépésben a P2SRootCert ujjlenyomatát használja, a változó a következőképpen néz ki:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Módosítsa és futtassa a példát egy ügyféltanúsítvány létrehozásához. Ha a következő példát anélkül futtatja, hogy módosítaná, az eredmény egy "P2SChildCert" nevű ügyféltanúsítvány. Ha a gyermektanúsítványt valami másnak szeretné elnevezni, módosítsa a KN-értéket. Ne módosítsa a TextExtension-t, amikor ezt a példát futtatja. A létrehozott ügyféltanúsítvány automatikusan települ a számítógépen a "Tanúsítványok - Aktuális felhasználó\Személyes\Tanúsítványok" mappába.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>A legfelső szintű tanúsítvány nyilvános kulcsának exportálása (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Az önaláírt főtanúsítvány és a személyes kulcs exportálása a tároláshoz (nem kötelező)

Előfordulhat, hogy szeretné exportálni az önaláírt főtanúsítványt, és biztonságosan tárolni biztonsági másolatként. Szükség esetén később telepítheti egy másik számítógépre, és több ügyféltanúsítványt hozhat létre. Ha az önaláírt főtanúsítványt .pfx-ként szeretné exportálni, jelölje ki a főtanúsítványt, és kövesse az [ügyféltanúsítvány exportálása](#clientexport)című részben leírtaknak leírt lépéseket.

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Az ügyféltanúsítvány exportálása

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
