---
title: Önaláírt tanúsítvány létrehozása egyéni legfelső szintű hitelesítésszolgáltatóval
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogy miként hozhat létre önaláírt Azure-tanúsítványt egyéni legfelső szintű hitelesítésszolgáltatóval
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311945"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Azure Application Gateway önaláírt tanúsítvány létrehozása egyéni legfelső szintű hitelesítésszolgáltatóval

Az Application Gateway v2 termékváltozat bevezeti a megbízható főtanúsítványok használatát a háttérkiszolgálók engedélyezéséhez. Ezzel eltávolítja a v1 termékváltozatban szükséges hitelesítési tanúsítványokat. A *főtanúsítvány* egy Base-64 kódolású X.509(. CER) a háttértanúsítvány-kiszolgálóról származó gyökértanúsítvány formázása. Azonosítja a kiszolgálói tanúsítványt kiállító legfelső szintű hitelesítésszolgáltatót, majd a kiszolgálói tanúsítványt használja a TLS/SSL-kommunikációhoz.

Az Application Gateway alapértelmezés szerint megbízik a webhely tanúsítványátban, ha azt egy jól ismert hitelesítésszolgáltató (például GoDaddy vagy DigiCert) írta alá. Ebben az esetben nem kell explicit módon feltöltenie a főtanúsítványt. További információt a [TLS-végződtetés áttekintése és a tls és az application gateway végpontok között című témakörben talál.](ssl-overview.md) Ha azonban fejlesztői/tesztelési környezettel rendelkezik, és nem szeretne ellenőrzött hitelesítésszolgáltatói aláírt tanúsítványt vásárolni, létrehozhat ja saját egyéni hitelesítésszolgáltatóját, és létrehozhat vele egy önaláírt tanúsítványt. 

> [!NOTE]
> Az önaláírt tanúsítványok alapértelmezés szerint nem megbízhatók, és nehezen karbantarthatók. Emellett elavult kivonatoló és rejtjel-csomagokat is használhatnak, amelyek nem feltétlenül erősek. A nagyobb biztonság érdekében vásároljon egy jól ismert hitelesítésszolgáltató által aláírt tanúsítványt.

Ebben a cikkben megtudhatja, hogyan:

- Saját egyéni hitelesítésszolgáltató létrehozása
- Az egyéni hitelesítésszolgáltató által aláírt önaláírt tanúsítvány létrehozása
- Önaláírt főtanúsítvány feltöltése alkalmazásátjáróba a háttérkiszolgáló hitelesítéséhez

## <a name="prerequisites"></a>Előfeltételek

- **[OpenSSL](https://www.openssl.org/) Windows vagy Linux rendszert futtató számítógépen** 

   Bár lehetnek más eszközök is a tanúsítványkezeléshez, ez az oktatóanyag openSSL-t használ. Megtalálható OpenSSL csomagban sok Linux disztribúciók, mint például az Ubuntu.
- **Webszerver**

   Például Apache, IIS vagy NGINX a tanúsítványok teszteléséhez.

- **Alkalmazásátjáró 2-es termékváltozat**
   
  Ha nem rendelkezik meglévő alkalmazásátjáróval, olvassa el a rövid útmutató: Közvetlen webes forgalom az Azure Application Gateway - Azure Portal című [témakört.](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>Legfelső szintű hitelesítésszolgáltatói tanúsítvány létrehozása

Hozza létre a legfelső szintű hitelesítésszolgáltatói tanúsítványt az OpenSSL segítségével.

### <a name="create-the-root-key"></a>A gyökérkulcs létrehozása

1. Jelentkezzen be a számítógépre, ahol az OpenSSL telepítve van, és futtassa a következő parancsot. Ezzel jelszóval védett kulcsot hoz létre.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. A kérdésmezőbe írjon be egy erős jelszót. Például legalább kilenc karakter, nagybetűs, kisbetűs, számok és szimbólumok használatával.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Gyökértanúsítvány létrehozása és aláírása

1. A következő parancsokkal hozza létre a csr-t és a tanúsítványt.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Az előző parancsok létrehozzák a főtanúsítványt. Ezzel alá írhatja a kiszolgálói tanúsítványt.

1. Amikor a rendszer kéri, írja be a gyökérkulcs jelszavát és az egyéni hitelesítésszolgáltató szervezeti adatait, például az ország, állam, szervezeti egység és a teljesen minősített tartománynév (ez a kibocsátó tartománya).

   ![legfelső szintű tanúsítvány létrehozása](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Kiszolgálói tanúsítvány létrehozása

Ezután hozzon létre egy kiszolgálói tanúsítványt az OpenSSL használatával.

### <a name="create-the-certificates-key"></a>A tanúsítvány kulcsának létrehozása

A következő paranccsal hozza létre a kiszolgálói tanúsítvány kulcsát.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Az CSR létrehozása (tanúsítvány-aláírási kérelem)

Az csr egy nyilvános kulcs, amelytanúsítvány igénylésekor kap egy hitelesítésszolgáltatót. A hitelesítésszolgáltató kiadja az adott kérelemhez tartozó tanúsítványt.

> [!NOTE]
> A kiszolgálói tanúsítvány KN-jének (köznapi neve) nem lehet más, mint a kibocsátó tartománya. Ebben az esetben például a kibocsátó KN-je, `www.contoso.com` és a kiszolgáló `www.fabrikam.com`tanúsítványának KN-je .


1. Az CSR létrehozásához használja a következő parancsot:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Amikor a rendszer kéri, írja be a gyökérkulcs jelszavát és az egyéni hitelesítésszolgáltató szervezeti adatait: Ország, állam, szervezeti egység, szervezeti egység és a teljesen minősített tartománynév. Ez a domain a honlapon, és meg kell különböznie a kibocsátó.

   ![Kiszolgálói tanúsítvány](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>A tanúsítvány létrehozása az CSR-rel és a kulccsal, és írja alá a hitelesítésszolgáltató gyökérkulcsával

1. A tanúsítvány létrehozásához használja a következő parancsot:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Az újonnan létrehozott tanúsítvány ellenőrzése

1. A CRT-fájl kimenetének nyomtatásához és tartalmának ellenőrzéséhez használja a következő parancsot:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Tanúsítvány ellenőrzése](media/self-signed-certificates/verify-cert.png)

1. Ellenőrizze a könyvtárban lévő fájlokat, és győződjön meg arról, hogy a következő fájlokat kapta:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>A tanúsítvány konfigurálása a webkiszolgáló TLS-beállításaiban

A webkiszolgálón konfigurálja a TLS fájlt a fabrikam.crt és a fabrikam.key fájlokkal. Ha a webkiszolgáló nem tud két fájlt bevenni, az OpenSSL parancsokkal egyetlen .pem vagy .pfx fájlba egyesítheti őket.

### <a name="iis"></a>IIS

A tanúsítványok iIS-re történő importálásáról és kiszolgálói tanúsítványként való feltöltésével kapcsolatos tudnivalókat a [Következő témakörben találja: Importált tanúsítványok telepítése webkiszolgálóra Windows Server 2003 rendszerben.](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)

A TLS-kötési utasításokat az [SSL beállítása az IIS 7-en (Útmutató az SSL beállítása) témakörben találja.](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)

### <a name="apache"></a>Apache

A következő konfiguráció egy példa [az Apache-ban ssl-hez konfigurált virtuális gazdagép:](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts)

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

A következő konfiguráció egy példa [NGINX kiszolgálóblokk](https://nginx.org/docs/http/configuring_https_servers.html) TLS konfigurációval:

![NGINX TLS-sel](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>A kiszolgáló elérése a konfiguráció ellenőrzéséhez

1. Adja hozzá a főtanúsítványt a számítógép megbízható gyökértárolójához. A webhely elérésekor győződjön meg arról, hogy a teljes tanúsítványlánc látható a böngészőben.

   ![Megbízható főtanúsítványok](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Feltételezzük, hogy a DNS úgy lett beállítva, hogy a webkiszolgáló nevét (ebben a példában www.fabrikam.com) a webkiszolgáló IP-címére irányítsa. Ha nem, a név feloldásához szerkesztheti a [hosts fájlt.](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)
1. Tallózással keresse meg a webhelyet, és kattintson a böngésző címmezőjének lakat ikonjára a webhely és a tanúsítvány adatainak ellenőrzéséhez.

## <a name="verify-the-configuration-with-openssl"></a>A konfiguráció ellenőrzése az OpenSSL segítségével

Vagy használhatja az OpenSSL-t a tanúsítvány ellenőrzéséhez.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL tanúsítvány ellenőrzése](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>A főtanúsítvány feltöltése az Application Gateway HTTP-beállításaiba

A tanúsítvány alkalmazásátjáróban való feltöltéséhez a .crt tanúsítványt .cer formátumba kell exportálnia Base-64 kódolással. Mivel a .crt már tartalmazza a nyilvános kulcsot a 64-es alapú kódolású formátumban, egyszerűen nevezze át a fájlkiterjesztést .crt-ről .cer-re. 

### <a name="azure-portal"></a>Azure Portal

A megbízható főtanúsítvány portálról való feltöltéséhez válassza a **HTTP-beállítások lehetőséget,** és válassza a **HTTPS protokollt.**

![Tanúsítvány hozzáadása a portál használatával](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Vagy használhatja az Azure CLI vagy az Azure PowerShell a főtanúsítvány feltöltéséhez. A következő kód egy Azure PowerShell-minta.

> [!NOTE]
> A következő minta hozzáad egy megbízható főtanúsítványt az alkalmazásátjáróhoz, létrehoz egy új HTTP-beállítást, és új szabályt ad hozzá, feltéve, hogy a háttérkészlet és a figyelő már létezik.

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

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
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

### <a name="verify-the-application-gateway-backend-health"></a>Az alkalmazásátjáró háttérállapotának ellenőrzése

1. Kattintson az alkalmazásátjáró **háttérállapot** nézetére, és ellenőrizze, hogy a mintavétel kifogástalan állapotú-e.
1. Látnia kell, hogy az állapot **kifogástalan** a HTTPS-mintavétel.

![HTTPS-szonda](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni az SSL\TLS alkalmazásátjáróban című témakörből: [A TLS-végződés áttekintése és a végpontok között a TLS az Application Gateway alkalmazásátjáróval](ssl-overview.md)című témakörben olvashat bővebben.

