---
title: "Konfigurálja az SSL - Azure Application Gateway - kiszervezés klasszikus PowerShell |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure klasszikus telepítési modell segítségével kiszervezése SSL Alkalmazásátjáró létrehozásához nyújt útmutatást"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 9540522a945e1ea2a09456b42d64b7b94753791f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>SSL kiszervezési Alkalmazásátjáró konfigurálása a klasszikus telepítési modell segítségével

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Az Azure klasszikus PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálókat, amelyeket az alkalmazás átjáró használatához konfigurálnia kell létezik, vagy a végpont létrehozott virtuális hálózatban vagy egy nyilvános IP-cím vagy a hozzárendelt virtuális IP-cím (VIP).

Alkalmazásátjáró SSL kiszervezési konfigurálásához kövesse az alábbi lépéseket, a megadott sorrendben:

1. [Alkalmazásátjáró létrehozása](#create-an-application-gateway)
2. [SSL-tanúsítványok feltöltése](#upload-ssl-certificates)
3. [Az átjáró konfigurálása](#configure-the-gateway)
4. [Állítsa be az átjáró konfigurálása](#set-the-gateway-configuration)
5. [Az átjáró elindítása](#start-the-gateway)
6. [Az átjáró állapotának megerősítése](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Létrehozhatja az átjárót, adja meg a `New-AzureApplicationGateway` parancsmag, az értékeket a saját cserélje le. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Ellenőrzése, hogy az átjáró hozták létre, megadhatja a `Get-AzureApplicationGateway` parancsmag.

A minta **leírás**, **InstanceCount**, és **GatewaySize** opcionális paraméterek. Az alapértelmezett érték **InstanceCount** van **2**, a legnagyobb értékű **10**. Az alapértelmezett érték **GatewaySize** van **Közepes**. Kis és nagy más elérhető értékek. **Virtualip értékek** és **DnsName** jelennek meg az üres, mert az átjáró még nem kezdődött meg. Ezek az értékek jönnek létre, miután az átjáró a futó állapotot.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-tanúsítványok feltöltése

Adja meg `Add-AzureApplicationGatewaySslCertificate` a kiszolgálói tanúsítvány PFX formátumban feltölteni az Alkalmazásátjáró. A tanúsítvány nevét a felhasználó által választott név, és az Alkalmazásátjáró belül egyedieknek kell lenniük. Ezt a tanúsítványt ezt a nevet a tanúsítvány az alkalmazás-átjárón összes felügyeleti művelet is hivatkozik.

Az alábbi minta-parancsmagot szemlélteti. Cserélje le a mintában szereplő értékekkel saját.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

A következő érvényesítse a tanúsítvány feltöltése. Adja meg a `Get-AzureApplicationGatewayCertificate` parancsmag.

A következő példában a parancsmag az első sorban, a kimeneti követi:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> A tanúsítvány jelszavát 4 – 12 karakterből áll, számjegyekből és betűkből álló között kell lennie. Speciális karakterek használata nem engedélyezett.

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Egy alkalmazás átjáró konfigurálása több érték áll. Az értékek is kötődik együtt a konfiguráció létrehozásához.

Az értékek a következők:

* **Háttér-kiszolgálófiók készlet**: a háttér-kiszolgálók IP-címek listáját. A felsorolt IP-címek a virtuális hálózati alhálózat kell tartoznia, vagy nyilvános IP-cím vagy a virtuális IP-címnek kell lennie.
* **Háttér-kiszolgálófiók Készletbeállítások**: minden készlethez beállítások, például a portot, a protokoll és a cookie-alapú kapcsolat van. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port**: Ez a port nem nyilvános port meg van nyitva, az alkalmazás-átjárón. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő**: A figyelő rendelkezik egy előtér-portot, a protokollt (Http vagy Https; ezek az értékek-és nagybetűk), és az SSL-tanúsítvány neve (ha konfigurálása az SSL-kiszervezés).
* **A szabály**: A szabály van kötve, a figyelő és a háttér-kiszolgálófiók készletben, és határozza meg, melyik háttér-kiszolgálófiók készlet át tudja irányítani a forgalmat, a találatok, amikor egy adott figyelő. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához **Https**-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCert** elem **HttpListener** neve szerepel az érték a [feltöltése SSL-tanúsítványok](#upload-ssl-certificates) szakasz. Az előtér-port frissíteni kell, hogy **443-as**.

**Ahhoz, hogy a cookie-alapú kapcsolat**: annak érdekében, hogy egy ügyfél kérelmet mindig van irányítva az azonos virtuális gép a webfarm Alkalmazásátjáró konfigurálhatja. Ennek megvalósítása érdekében beszúrása egy munkamenetcookie-t, amely lehetővé teszi, hogy az átjáró megfelelően irányítja a forgalmat. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen **Enabled** a **BackendHttpSettings** elemen belül.

A konfigurációs hogyan hozhat létre, vagy hozzon létre egy konfigurációs objektumot, vagy egy konfigurációs XML-fájl használatával.
A konfiguráció létrehozásához egy konfigurációs XML-fájl használatával, adja meg az alábbi minta:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Állítsa be az átjáró konfigurálása

A következő lépésként állítsa be az Application Gateway-t. Megadhatja a `Set-AzureApplicationGatewayConfig` parancsmag és egy konfigurációs objektum vagy egy konfigurációs XML-fájlt.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálása után adja meg a `Start-AzureApplicationGateway` parancsmag segítségével indítsa el az átjárót. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag 15-20 percig is eltarthat.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Adja meg a `Get-AzureApplicationGateway` parancsmagot, hogy az átjáró állapotának ellenőrzéséhez. Ha `Start-AzureApplicationGateway` sikeres volt az előző lépésben a **állapot** kell **futtató**, és a **virtualip értékek** és **DnsName** kell rendelkezik érvényes bejegyzésekkel.

Ez a példa bemutatja, hogy működőképes, futó, és készen áll a forgalom érvénybe Alkalmazásátjáró:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Következő lépések

További információ a terheléselosztás beállítások általában, lásd:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
