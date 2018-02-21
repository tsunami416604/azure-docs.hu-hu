---
title: "Készítése és a pont-pont tanúsítványok exportálása: PowerShell: Azure |} Microsoft Docs"
description: "Hozzon létre egy önaláírt legfelső szintű tanúsítványt, exportálja a nyilvános kulcsot, és a PowerShell-lel Windows 10 vagy Windows Server 2016 ügyféltanúsítványok előállításához."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: 5e041de12105770a16e43b5ff4f918a1eba92b2a
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-powershell-on-windows-10-or-windows-server-2016"></a>Létrehozása és exportálása a tanúsítványok a PowerShell-lel Windows 10 vagy Windows Server 2016 pont – hely kapcsolatok

Pont – hely kapcsolatok tanúsítványok segítségével hitelesíti. Ez a cikk bemutatja, hogyan hozzon létre egy önaláírt legfelső szintű tanúsítványt, és a PowerShell-lel Windows 10 vagy Windows Server 2016 ügyféltanúsítványok előállításához. Ha a keresett pont-hely konfigurációs lépések, például a legfelső szintű tanúsítványok feltöltéséről válassza az "konfigurálása pont-pont" cikkekben az alábbi listából:

> [!div class="op_single_selector"]
> * [Hozzon létre önaláírt tanúsítványokat - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [Hozzon létre önaláírt tanúsítványokat - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [Pont – hely - Resource Manager - Azure-portál konfigurálása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Pont – hely - erőforrás-kezelő - PowerShell konfigurálása](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Pont – hely - klasszikus - Azure-portál konfigurálása](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 


Ez a cikk a Windows 10 vagy Windows Server 2016 rendszerű számítógépre kell hajtsa végre a lépéseket. A PowerShell-parancsmagok, amelyekkel lehet tanúsítványokat létrehozni az operációs rendszer része, és a Windows más verziói nem működnek. A Windows 10 vagy Windows Server 2016 számítógép csak akkor tanúsítványainak előállításához szükséges. Ha a tanúsítványok jönnek létre, feltöltheti ezeket, vagy telepítse őket a támogatott ügyfél operációs rendszereken. 

Ha nincs hozzáférése a Windows 10 vagy Windows Server 2016 számítógép, [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) tanúsítványainak létrehozásához. A tanúsítványok, létrehozhat módszerek használatával is telepíthető [támogatott](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) ügyfél operációs rendszerét.

## <a name="rootcert"></a>Hozzon létre egy önaláírt legfelső szintű tanúsítványt

A New-SelfSignedCertificate parancsmag segítségével hozzon létre egy önaláírt legfelső szintű tanúsítványt. További információkért lásd: [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Windows 10 vagy Windows Server 2016 rendszert futtató számítógépről nyissa meg a Windows PowerShell-konzolban emelt szintű jogosultságokkal.
2. Az alábbi példát követve létrehozni az önaláírt legfelső szintű tanúsítványt. Az alábbi példa létrehoz egy önaláírt legfelső szintű tanúsítványt, "P2SRootCert", amely automatikusan telepíti a "Tanúsítványok-aktuális User\Personal\Certificates" nevű. A tanúsítvány megtekintéséhez nyissa meg *certmgr.msc*, vagy *kezelheti a felhasználói tanúsítványok*.

  ```powershell
  $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
  ```

### <a name="cer"></a>Exportálja a nyilvános kulcsot (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

A exported.cer fájlt fel kell tölteni, az Azure-bA. Útmutatásért lásd: [egy pont – hely kapcsolat beállítása](vpn-gateway-howto-point-to-site-rm-ps.md#upload). Egy további megbízható legfelső szintű tanúsítvány hozzáadása [ebben a szakaszban](vpn-gateway-howto-point-to-site-rm-ps.md#addremovecert) a cikk.

### <a name="export-the-self-signed-root-certificate-and-public-key-to-store-it-optional"></a>Exportálhatja az önaláírt legfelső szintű tanúsítvány és nyilvános kulcs tárolja (nem kötelező)

Érdemes lehet önaláírt legfelső szintű tanúsítványának exportálása és tárolja biztonságos helyen. Ha kell, később egy másik számítógépre telepítse, és további ügyféltanúsítványok előállításához vagy egy másik .cer-fájl exportálását. Az önaláírt legfelső szintű tanúsítvány exportálása a .pfx fájlhoz, válassza ki a legfelső szintű tanúsítványt, és használja ugyanazokat a lépéseket, a [ügyféltanúsítvány exportálása](#clientexport).

## <a name="clientcert"></a>Ügyfél-tanúsítvány létrehozása

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Hozzon létre egy ügyféltanúsítványt a önaláírt legfelső szintű tanúsítvány és exportálni, és telepíti az ügyféltanúsítványt. Ha az ügyféltanúsítvány nincs telepítve, a hitelesítés meghiúsul. 

A következő lépések végigvezetik történő tanúsítványgenerálás során egy ügyfél egy önaláírt legfelső szintű tanúsítványt. Előfordulhat, hogy több ügyféltanúsítványt generálása ugyanazon főtanúsítványhoz. Az alábbi lépéseket követve ügyféltanúsítványok létrehozásakor az ügyféltanúsítvány automatikusan települ azon a számítógépen, amelyet a tanúsítvány létrehozásához használt. Ha egy ügyfél-tanúsítványt egy másik ügyfélszámítógépen telepíteni szeretné, exportálhatja a tanúsítványt.

A példák a New-SelfSignedCertificate parancsmaggal hozza létre az ügyfél, amely egy év lejár. További információkat, például a különböző lejárati érték megadásakor az ügyféltanúsítványt, lásd: [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>1. példa

A példa a deklarált "$cert" változót előző szakaszából. Ha a gyökér önaláírt tanúsítvány létrehozása után a PowerShell-konzolon zárva, vagy egy új PowerShell-konzol munkamenetet további ügyfél tanúsítványok készíti, lépésekkel [2. példa](#ex2).

Módosítsa, majd futtassa a példa egy ügyfél-tanúsítvány előállításához. Ha az alábbi példa azt a módosítása nélkül futtatja, eredménye "P2SChildCert" nevű ügyfél-tanúsítványt.  Ha szeretné a gyermek tanúsítvány elnevezése valami mást, módosítsa a CN-érték. Ne módosítsa a TextExtension ebben a példában futtatásakor. Az ügyfél tanúsítványát, létrehozhat "Tanúsítványok - aktuális User\Personal\Certificates" automatikusan telepíti a számítógépre.

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>2. példa

Ha további ügyféltanúsítványok létrehozni, vagy nem használja az ugyanazon a önaláírt legfelső szintű tanúsítvány létrehozásához használt PowerShell-munkamenetet, tegye a következőket:

1. Azonosítsa a önaláírt legfelső szintű tanúsítvány, amely telepítve van a számítógépen. Ez a parancsmag a számítógépen telepített tanúsítványok listáját adja vissza.

  ```powershell
  Get-ChildItem -Path “Cert:\CurrentUser\My”
  ```
2. Keresse meg a tulajdonos nevét, a visszaadott listából, majd másolja le az ujjlenyomatot, amely egy szövegfájlba mellette található. A következő példában két tanúsítványokat is. A CN-név esetén a önaláírt legfelső szintű tanúsítvány, amelyből gyermek tanúsítványt létrehozni kívánja. Ebben az esetben "P2SRootCert".

  ```
  Thumbprint                                Subject
  
  AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
  7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
  ```
3. A legfelső szintű tanúsítvány ujjlenyomata az előző lépésben segítségével változó deklarálható. Cserélje le, amelyből el kívánja létrehozni a tanúsítványt a főtanúsítvány ujjlenyomatának UJJLENYOMATA.

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
  ```

  Például az ujjlenyomat P2SRootCert használja az előző lépésben, a változó néz ki:

  ```powershell
  $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
  ```
4.  Módosítsa, majd futtassa a példa egy ügyfél-tanúsítvány előállításához. Ha az alábbi példa azt a módosítása nélkül futtatja, eredménye "P2SChildCert" nevű ügyfél-tanúsítványt. Ha szeretné a gyermek tanúsítvány elnevezése valami mást, módosítsa a CN-érték. Ne módosítsa a TextExtension ebben a példában futtatásakor. Az ügyfél tanúsítványát, létrehozhat "Tanúsítványok - aktuális User\Personal\Certificates" automatikusan telepíti a számítógépre.

  ```powershell
  New-SelfSignedCertificate -Type Custom -KeySpec Signature `
  -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
  -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My" `
  -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
  ```

## <a name="clientexport"></a>Ügyfél-tanúsítvány exportálása   

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

## <a name="install"></a>Az exportált ügyféltanúsítvány telepítése

Ügyfél-tanúsítvány telepítéséhez tekintse át [telepítheti egy pont – hely kapcsolatok ügyféltanúsítványt](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>További lépések

A pont-hely konfigurációs folytatásához.

* A **erőforrás-kezelő** telepítési modell lépéseket lásd: [konfigurálása P2S natív Azure-alapú hitelesítést használó](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* A **klasszikus** telepítési modell lépéseket lásd: [egy virtuális hálózat (klasszikus) pont – hely típusú VPN-kapcsolat konfigurálva](vpn-gateway-howto-point-to-site-classic-azure-portal.md).

P2S hibaelhárítási információkat lásd: [Azure hibaelhárítási pont – hely kapcsolatok](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).