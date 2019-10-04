---
title: Azure Application Gateway önaláírt tanúsítvány létrehozása egyéni legfelső szintű HITELESÍTÉSSZOLGÁLTATÓval
description: Útmutató Azure Application Gateway önaláírt tanúsítvány létrehozásához egyéni legfelső szintű HITELESÍTÉSSZOLGÁLTATÓval
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 9966164ec1b6a37538a24d2ef8cb80007e6f6d29
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698233"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Azure Application Gateway önaláírt tanúsítvány létrehozása egyéni legfelső szintű HITELESÍTÉSSZOLGÁLTATÓval

A Application Gateway v2 SKU bevezeti a megbízható legfelső szintű tanúsítványok használatát a háttér-kiszolgálók engedélyezéséhez. Ezzel eltávolítja a v1 SKU-ban szükséges hitelesítési tanúsítványokat. A *Főtanúsítvány* egy Base-64 kódolású X. 509 (. CER) a főtanúsítvány formázása a háttérbeli tanúsítvány-kiszolgálóról. Ez azonosítja a kiszolgálói tanúsítványt kiállító legfelső szintű hitelesítésszolgáltatót (CA), a kiszolgálói tanúsítványt pedig az SSL-kommunikációhoz.

A Application Gateway alapértelmezés szerint megbízhatónak minősíti a webhely tanúsítványát, ha azt egy jól ismert HITELESÍTÉSSZOLGÁLTATÓ (például GoDaddy vagy DigiCert) írta alá. Ebben az esetben nem kell explicit módon feltöltenie a főtanúsítványt. További információ: [az SSL-lezárás áttekintése és a végpontok közötti ssl Application Gateway](ssl-overview.md)használatával. Ha azonban fejlesztési/tesztelési környezettel rendelkezik, és nem szeretne ellenőrzött HITELESÍTÉSSZOLGÁLTATÓ által aláírt tanúsítványt vásárolni, létrehozhat egy saját egyéni HITELESÍTÉSSZOLGÁLTATÓT, és létrehozhat egy önaláírt tanúsítványt. 

> [!NOTE]
> Az önaláírt tanúsítványok alapértelmezés szerint nem megbízhatók, és nehéz lehet fenntartani őket. Emellett elavult kivonatoló és titkosító csomagokat is használhatnak, amelyek esetleg nem erősek. A biztonság érdekében érdemes megvásárolnia egy jól ismert hitelesítésszolgáltató által aláírt tanúsítványt.

Ez a cikk azt ismerteti, hogyan lehet:

- Saját egyéni hitelesítésszolgáltató létrehozása
- Egyéni HITELESÍTÉSSZOLGÁLTATÓ által aláírt önaláírt tanúsítvány létrehozása
- Önaláírt főtanúsítvány feltöltése egy Application Gatewayba a háttér-kiszolgáló hitelesítéséhez

## <a name="prerequisites"></a>Előfeltételek

- **[OpenSSL](https://www.openssl.org/) Windows vagy Linux rendszerű számítógépen** 

   Habár más eszközök is elérhetők a Tanúsítványkezelők számára, ez az oktatóanyag az OpenSSL-t használja. Számos Linux-disztribúcióval, például Ubuntuval rendelkező OpenSSL-csomagot talál.
- **Egy webkiszolgáló**

   Például: Apache, IIS vagy NGINX a tanúsítványok teszteléséhez.

- **Egy Application Gateway v2 SKU**
   
  Ha nem rendelkezik meglévő Application gatewayvel, tekintse [meg a gyors útmutató: Közvetlen webes forgalom az Azure Application Gateway-Azure Portal](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

Hozza létre a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt az OpenSSL használatával.

### <a name="create-the-root-key"></a>A legfelső szintű kulcs létrehozása

1. Jelentkezzen be a számítógépre, ahol az OpenSSL telepítve van, és futtassa a következő parancsot. Ezzel létrehoz egy jelszóval védett kulcsot.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. A parancssorba írjon be egy erős jelszót. Például legalább kilenc karakter, nagybetűvel, kisbetű, szám és szimbólum használatával.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Főtanúsítvány létrehozása és saját aláírása

1. A CSR és a tanúsítvány létrehozásához használja a következő parancsokat.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Az előző parancs létrehozza a főtanúsítványt. Ezt a kiszolgáló tanúsítványának aláírásához fogja használni.

1. Ha a rendszer kéri, írja be a legfelső szintű kulcshoz tartozó jelszót, valamint az egyéni HITELESÍTÉSSZOLGÁLTATÓ szervezeti adatait, például az ország, az állapot, a szervezeti egység és a teljes tartománynevet (ez a kibocsátó tartománya).

   ![főtanúsítvány létrehozása](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Kiszolgálótanúsítvány létrehozása

Ezután létre fog hozni egy kiszolgálói tanúsítványt az OpenSSL használatával.

### <a name="create-the-certificates-key"></a>A tanúsítvány kulcsának létrehozása

Használja a következő parancsot a kiszolgálói tanúsítvány kulcsának létrehozásához.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>A CSR létrehozása (tanúsítvány-aláírási kérelem)

A CSR egy nyilvános kulcs, amelyet a rendszer a tanúsítvány igénylése során kap a HITELESÍTÉSSZOLGÁLTATÓ számára. A HITELESÍTÉSSZOLGÁLTATÓ kibocsátja a tanúsítványt az adott kérelemhez.

> [!NOTE]
> A kiszolgálói tanúsítványhoz tartozó CN (köznapi név) nem lehet azonos a kiállító tartományával. Ebben az esetben például a kiállító www.contoso.com a CN, a kiszolgálói tanúsítvány CN pedig www.fabrikam.com


1. A CSR létrehozásához használja a következő parancsot:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Ha a rendszer kéri, írja be a legfelső szintű kulcshoz tartozó jelszót és az egyéni HITELESÍTÉSSZOLGÁLTATÓ szervezeti adatait: Ország, állam, org, OU és a teljes tartománynév. Ez a webhely tartománya, és a kiállítótól eltérőnek kell lennie.

   ![Kiszolgálói tanúsítvány](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>A tanúsítvány előállítása a CSR-sel és a kulccsal, és aláírás a HITELESÍTÉSSZOLGÁLTATÓ legfelső szintű kulcsával

1. A tanúsítvány létrehozásához használja a következő parancsot:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Az újonnan létrehozott tanúsítvány ellenőrzése

1. A következő parancs használatával nyomtassa ki a CRT-fájl kimenetét, és ellenőrizze annak tartalmát:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Tanúsítvány ellenőrzése](media/self-signed-certificates/verify-cert.png)

1. Ellenőrizze a címtárban található fájlokat, és győződjön meg arról, hogy rendelkezik a következő fájlokkal:

   - contoso. CRT
   - contoso. Key
   - fabrikam. CRT
   - fabrikam. Key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>A tanúsítvány konfigurálása a webkiszolgáló SSL-beállításaiban

A webkiszolgálón konfigurálja az SSL-t a fabrikam. CRT és fabrikam. Key fájlok használatával. Ha a webkiszolgáló nem tud két fájlt készíteni, kombinálhatja őket egyetlen. PEM vagy. pfx fájllal az OpenSSL-parancsok használatával.

### <a name="iis"></a>IIS

A tanúsítvány importálásával és kiszolgálói tanúsítványként való feltöltésével kapcsolatos útmutatásért lásd [: How to: Importált tanúsítványok telepítése egy webkiszolgálón a Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)-ben.

Az SSL-kötési utasításokért lásd: az [SSL beállítása az IIS 7 rendszeren](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

Az alábbi konfiguráció egy [virtuális gép](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) , amely az SSL-hez van konfigurálva az Apache-ban:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

A következő konfiguráció egy példa a [NGINX-kiszolgáló blokkolására](http://nginx.org/docs/http/configuring_https_servers.html) az SSL-konfigurációval:

![NGINX SSL-lel](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>A kiszolgáló elérése a konfiguráció ellenőrzéséhez

1. Adja hozzá a főtanúsítványt a számítógép megbízható legfelső szintű tárolójához. Amikor hozzáfér a webhelyhez, győződjön meg arról, hogy a teljes tanúsítványlánc látható a böngészőben.

   ![Megbízható legfelső szintű tanúsítványok](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Feltételezzük, hogy a DNS úgy lett konfigurálva, hogy a webkiszolgáló nevét (ebben a példában www.fabrikam.com) a webkiszolgáló IP-címére mutasson. Ha nem, akkor szerkesztheti a [hosts fájlt](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) a név feloldásához.
1. Keresse meg a webhelyet, és kattintson a böngésző címsorába a Zárolás ikonra a hely és a tanúsítvány adatainak ellenőrzéséhez.

## <a name="verify-the-configuration-with-openssl"></a>A konfiguráció ellenőrzése az OpenSSL-vel

Vagy használhatja az OpenSSL-t a tanúsítvány ellenőrzéséhez.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL-tanúsítvány ellenőrzése](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>A főtanúsítvány feltöltése Application Gateway HTTP-beállításaira

A tanúsítvány Application Gatewayba való feltöltéséhez exportálnia kell a. CRT-tanúsítványt egy. cer formátumba, Base-64 kódolással. Mivel a. CRT már tartalmazza a nyilvános kulcsot a Base-64 kódolású formátumban, egyszerűen nevezze át a fájlkiterjesztést a. CRT fájlból a. cer névre. 

### <a name="azure-portal"></a>Azure Portal

A megbízható főtanúsítványnak a portálról való feltöltéséhez válassza ki a **http-beállításokat** , és válassza a **https** protokollt.

![Tanúsítvány hozzáadása a portál használatával](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Vagy az Azure CLI vagy a Azure PowerShell használatával feltöltheti a főtanúsítványt. A következő kód egy Azure PowerShell minta.

> [!NOTE]
> Az alábbi minta egy megbízható főtanúsítványt hoz létre az Application gatewayhez, létrehoz egy új HTTP-beállítást, és új szabályt vesz fel, feltéve, hogy a háttér-készlet és a figyelő már létezik.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```
### <a name="verify-the-application-gateway-backend-health"></a>Az Application Gateway-háttér állapotának ellenőrzése

1. Az Application Gateway **háttér-állapot** nézetére kattintva ellenőrizze, hogy a mintavétel kifogástalan-e.
1.  Látnia kell, hogy a HTTPS-mintavétel állapota **kifogástalan** .

    ![HTTPS-mintavétel](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Application Gateway SSL\TLS kapcsolatban, tekintse meg az [SSL-lezárás áttekintése és a végpontok közötti SSL a Application Gateway](ssl-overview.md)használatával című cikket.

