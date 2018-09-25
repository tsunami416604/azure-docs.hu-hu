---
title: Konfigurálja az SSL-kiszervezési – az Azure Application Gateway – klasszikus PowerShell |} A Microsoft Docs
description: Ez a cikk utasításait követve hozzon létre egy application gateway SSL-lel kiszervezheti a klasszikus Azure üzemi modell használatával
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 63f28d96-9c47-410e-97dd-f5ca1ad1b8a4
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: victorh
ms.openlocfilehash: 782e5c4b33cc62ab5af80e823dc63b3e79a707b3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980525"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Application gateway SSL-alapú kiszervezés konfigurálása a klasszikus üzemi modell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. A kiszolgálókat, az application gateway használatára konfigurál a kell létezik, vagy a virtuális hálózatban, és a egy nyilvános IP-címmel vagy a hozzárendelt virtuális IP-címet (VIP) létrehozott végpontokkal rendelkezik.

SSL-alapú kiszervezés konfigurálása az application gateway-en, a következő lépéseket a megadott sorrendben:

1. [Application gateway létrehozása](#create-an-application-gateway)
2. [SSL-tanúsítványok feltöltése](#upload-ssl-certificates)
3. [Az átjáró konfigurálása](#configure-the-gateway)
4. [Az átjáró konfigurációjának beállítása](#set-the-gateway-configuration)
5. [Indítsa el az átjárót](#start-the-gateway)
6. [Az átjáró állapotának ellenőrzése](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az átjáró létrehozásához írja be a `New-AzureApplicationGateway` parancsmagot, és cserélje le az értékeket saját. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Ellenőrzése, hogy az átjáró létrehozásának, megadhatja a `Get-AzureApplicationGateway` parancsmagot.

A mintában **leírás**, **InstanceCount**, és **GatewaySize** opcionális paraméterek. Az alapértelmezett érték a **InstanceCount** van **2**, maximális értéke pedig a **10**. Az alapértelmezett érték a **GatewaySize** van **Közepes**. Kis és nagy más elérhető értékek. **VirtualIPs** és **DnsName** jelennek meg üres, mert az átjáró még nem kezdődött meg. Ezeket az értékeket jönnek létre, miután az átjáró futó állapotba kerül.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-tanúsítványok feltöltése

Adja meg `Add-AzureApplicationGatewaySslCertificate` feltölteni az application gateway a tanúsítvány PFX formátumban. A tanúsítvány nevét egy felhasználó által választott név, és az application gateway egyedinek kell lennie. Ezt a tanúsítványt az összes tanúsítvány műveletek az application gateway az ezen a néven hivatkozik.

Az alábbi minta-parancsmagot szemlélteti. Cserélje le az értékeket a minta saját.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Ezután ellenőrizze a tanúsítvány feltöltése. Adja meg a `Get-AzureApplicationGatewayCertificate` parancsmagot.

A következő minta bemutatja a parancsmag első sorát, a kimenet követ:

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
> A tanúsítvány jelszavát és betűkből álló áll 4 és 12 karakter között kell lennie. Speciális karakterek használata nem engedélyezett.

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Egy application gateway-konfigurációt több értéket tartalmaz. Az értékeket is időpontjától együtt a konfiguráció létrehozásához.

Az értékek a következők:

* **Háttér-kiszolgálókészlet**: a háttér-kiszolgálók IP-címek listáját. Megadott IP-címek a virtuális hálózat alhálózatához kell tartozniuk, vagy egy nyilvános IP-cím vagy a virtuális IP-címet kell lennie.
* **Háttér-kiszolgálókészlet beállításai**: minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port**: A port az application gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő**: A figyelőt tartalmaz egy előtérbeli porttal, egy protokollal (Http vagy https előtaggal; megkülönböztetésével), és az SSL-tanúsítvány neve (ha az SSL-alapú kiszervezés konfigurálása).
* **A szabály**: A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, melyik háttérkiszolgáló-készletet a forgalmat, ha elér egy adott figyelőt. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához **Https**-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCert** elem **HttpListener** értékre van állítva a használt ugyanazzal a névvel, a [töltse fel az SSL-tanúsítványok](#upload-ssl-certificates) szakaszban. Az előtérbeli portot frissíteni kell, hogy **443-as**.

**Cookie-alapú affinitás engedélyezéséhez**: konfigurálhatja az application gateway, győződjön meg arról, hogy érkező kérelmet mindig van irányítva a webfarm ugyanazon virtuális Géphez. Ennek érdekében a szúrjon be egy munkamenetcookie-t, amely lehetővé teszi az átjáró számára a forgalom megfelelő irányítását. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen **Enabled** a **BackendHttpSettings** elemen belül.

A konfiguráció hozhatnak létre, vagy egy konfigurációs objektumot hoz létre, vagy egy konfigurációs XML-fájl használatával.
A konfiguráció létrehozására egy konfigurációs XML-fájl használatával, adja meg a következő mintát:


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

## <a name="set-the-gateway-configuration"></a>Az átjáró konfigurációjának beállítása

A következő lépésként állítsa be az Application Gateway-t. Megadhatja a `Set-AzureApplicationGatewayConfig` parancsmagot egy konfigurációs objektumot, vagy egy konfigurációs XML-fájl.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálása után adja meg a `Start-AzureApplicationGateway` parancsmag segítségével indítsa el az átjárót. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag a 15-20 percet is igénybe vehet.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Adja meg a `Get-AzureApplicationGateway` parancsmagot, hogy az átjáró állapotának ellenőrzéséhez. Ha `Start-AzureApplicationGateway` sikeres volt az előző lépésben a **állapot** kell **futó**, és a **VirtualIPs** és **DnsName** kell érvényes bejegyzéssel kell.

Ez a példa egy application gateway fut, és készen áll a tartó forgalmat felfelé is látható:

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

## <a name="next-steps"></a>További lépések

További információ a terheléselosztó beállítások általában lásd:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
