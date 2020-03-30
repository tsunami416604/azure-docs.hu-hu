---
title: SSL-kiszervezés a PowerShell használatával – Azure Application Gateway
description: Ez a cikk utasításokat ad egy alkalmazásátjáró SSL-kiszervezéssel az Azure klasszikus telepítési modell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047923"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Alkalmazásátjáró konfigurálása SSL-kiszervezéshez a klasszikus telepítési modell használatával

> [!div class="op_single_selector"]
> * [Azure-portál](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure klasszikus PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/)**Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. Az alkalmazásátjáró használatára konfigurált kiszolgálóknak létezniük kell, vagy rendelkezniük kell a virtuális hálózatban, illetve nyilvános IP-címmel vagy virtuális IP-címmel (VIP) létrehozott végpontokkal.

Az SSL-kiszervezés alkalmazásátjárón történő konfigurálásához hajtsa végre a következő lépéseket a felsorolt sorrendben:

1. [Alkalmazásátjáró létrehozása](#create-an-application-gateway)
2. [Ssl-tanúsítványok feltöltése](#upload-ssl-certificates)
3. [Az átjáró konfigurálása](#configure-the-gateway)
4. [Az átjáró konfigurációjának beállítása](#set-the-gateway-configuration)
5. [Az átjáró indítása](#start-the-gateway)
6. [Az átjáró állapotának ellenőrzése](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az átjáró létrehozásához `New-AzureApplicationGateway` írja be a parancsmagát, és cserélje le az értékeket a sajátjára. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Az átjáró létrehozásának ellenőrzéséhez adja `Get-AzureApplicationGateway` meg a parancsmabot.

A mintában a **Leírás**, **InstanceCount**és **GatewaySize** nem kötelező paraméterek. A **InstanceCount** alapértelmezett értéke **2**, legfeljebb **10.** A **GatewaySize** alapértelmezett értéke **közepes**. A kicsi és a nagy érték más elérhető érték. **A VirtualIP-k** és a **DnsName** üresként jelennek meg, mert az átjáró még nem indult el. Ezek az értékek az átjáró futási állapotának befutása után jönnek létre.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>Ssl-tanúsítványok feltöltése

Adja `Add-AzureApplicationGatewaySslCertificate` meg a kiszolgálói tanúsítvány PFX formátumban történő feltöltéséhez az alkalmazásátjáróba. A tanúsítvány név egy felhasználó által választott név, és egyedinek kell lennie az alkalmazásátjárón belül. Erre a néven hivatkoznak erre a néven az alkalmazásátjáró összes tanúsítványkezelési műveletében.

A következő minta a parancsmamot mutatja. Cserélje le a minta értékeit a sajátjára.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Ezután ellenőrizze a tanúsítvány feltöltését. Adja `Get-AzureApplicationGatewayCertificate` meg a parancsmagát.

A következő minta az első sorban lévő parancsmagatot mutatja, amelyet a kimenet követ:

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
> A tanúsítvány jelszavának 4 és 12 karakter között kell lennie, betűkből vagy számokból. A speciális karaktereket nem fogadják el.

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Az alkalmazásátjáró konfigurációja több értékből áll. Az értékek a konfiguráció létrehozásához összeköthetők.

Az értékek a következők:

* **Háttérkiszolgáló-készlet**: A háttérkiszolgálók IP-címeinek listája. A felsorolt IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános IP- vagy VIP-címnek kell lenniük.
* **Háttérkiszolgáló-készlet beállításai:** Minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll és a cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port**: Ez a port az alkalmazásátjárón megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő rendelkezik egy előtér-port, egy protokoll (Http vagy Https; ezek az értékek a kis- és nagybetűk megkülönböztetése), és az SSL tanúsítvány neve (ha egy SSL-kiszervezés konfigurálása).
* **Szabály**: A szabály köti a figyelő és a háttér-kiszolgáló készlet, és meghatározza, hogy melyik háttér-kiszolgáló készlet irányítani a forgalmat, amikor eléri egy adott figyelő. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához **Https**-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCert** elemet a **HttpListener** hez úgy, hogy az érték az [SSL-tanúsítványok feltöltése](#upload-ssl-certificates) szakaszban használt névre van beállítva. Az előtér-portot **443-ra**kell frissíteni.

**Cookie-alapú affinitás engedélyezése**: Konfigurálhat egy alkalmazásátjárót annak biztosítására, hogy az ügyfélmunkamenetből érkező kérés mindig ugyanarra a virtuális gépre irányuljon a webfarmban. Ehhez helyezzen be egy munkamenet-cookie-t, amely lehetővé teszi, hogy az átjáró megfelelően irányítsa a forgalmat. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen **Enabled** a **BackendHttpSettings** elemen belül.

A konfigurációt konfigurációs objektum létrehozásával vagy konfigurációs XML-fájl használatával hozhatja létre.
A konfiguráció konfigurációs XML-fájl használatával történő létrehozásához adja meg a következő mintát:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

A következő lépésként állítsa be az Application Gateway-t. A `Set-AzureApplicationGatewayConfig` parancsmatot konfigurációs objektummal vagy konfigurációs XML-fájllal is megadhatja.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálása után `Start-AzureApplicationGateway` adja meg a parancsmabot az átjáró elindításához. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag 15-20 percet is igénybe vehet.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Adja `Get-AzureApplicationGateway` meg a parancsmabot az átjáró állapotának ellenőrzéséhez. Ha `Start-AzureApplicationGateway` az előző lépésben sikerült, az **állapotnak** **futnia**kell, és a **VirtualIPs** és a DnsName érvényes bejegyzéssel kell **rendelkeznie.**

Ez a minta egy futó, és forgalomhasználatra kész alkalmazásátjárót mutat be:

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

A terheléselosztási lehetőségekről általában az:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
