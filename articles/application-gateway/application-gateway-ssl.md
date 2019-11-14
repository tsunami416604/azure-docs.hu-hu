---
title: SSL-kiszervezés a PowerShell használatával – Azure Application Gateway
description: Ez a cikk útmutatást nyújt az Application Gateway SSL-alapú kiszervezéssel való létrehozásához a klasszikus Azure üzemi modell használatával
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: c456a0856adb0d36349b5f96ba0ab8bab3eec5c9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047923"
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Application Gateway konfigurálása SSL-alapú kiszervezéshez a klasszikus üzembe helyezési modell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-ssl.md)
> * [Azure CLI](application-gateway-ssl-cli.md)

Az Azure Application Gateway konfigurálható úgy, hogy leállítsa a Secure Sockets Layer (SSL) munkamenetét az átjárónál, így elkerülhetők a költséges SSL visszafejtési feladatok a webfarmon. Az SSL-alapú kiszervezés emellett leegyszerűsíti az előtér-kiszolgáló számára webalkalmazás telepítését és kezelését.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A **Letöltések lap** [Windows PowerShell](https://azure.microsoft.com/downloads/) szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
3. Az Application Gateway használatára konfigurált kiszolgálóknak léteznie kell, vagy a virtuális hálózaton vagy a hozzárendelt nyilvános IP-címmel vagy virtuális IP-címmel (VIP) létrehozott végpontokkal kell rendelkezniük.

Az SSL-alapú kiszervezés az Application gatewayben való konfigurálásához hajtsa végre a következő lépéseket a felsorolt sorrendben:

1. [Application Gateway létrehozása](#create-an-application-gateway)
2. [SSL-tanúsítványok feltöltése](#upload-ssl-certificates)
3. [Az átjáró konfigurálása](#configure-the-gateway)
4. [Az átjáró konfigurációjának beállítása](#set-the-gateway-configuration)
5. [Az átjáró elindítása](#start-the-gateway)
6. [Átjáró állapotának ellenőrzése](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az átjáró létrehozásához írja be a `New-AzureApplicationGateway` parancsmagot, és cserélje le az értékeket a saját értékeire. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Az átjáró létrehozásának ellenőrzéséhez megadhatja a `Get-AzureApplicationGateway` parancsmagot.

A mintában a **Leírás**, a **InstanceCount**és a **GatewaySize** választható paraméterek. A **InstanceCount** alapértelmezett értéke **2**, maximális értéke pedig **10**. A **GatewaySize** alapértelmezett értéke **közepes**. A kicsi és a nagy a többi elérhető érték. A **VirtualIPs** és a **DnsName** üresként jelennek meg, mert az átjáró még nem indult el. Ezek az értékek akkor jönnek létre, amikor az átjáró fut állapotban van.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-ssl-certificates"></a>SSL-tanúsítványok feltöltése

Adja meg `Add-AzureApplicationGatewaySslCertificate` a kiszolgálói tanúsítvány PFX formátumban való feltöltéséhez az Application gatewaybe. A tanúsítvány neve felhasználó által választott név, és az Application gatewayen belül egyedinek kell lennie. Ennek a tanúsítványnak a neve az Application Gateway összes tanúsítványkezelő műveletében szerepel.

A következő minta a parancsmagot mutatja be. Cserélje le a minta értékeit a saját adataira.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Ezután érvényesítse a tanúsítvány feltöltését. Adja meg a `Get-AzureApplicationGatewayCertificate` parancsmagot.

Az alábbi minta az első sorban lévő parancsmagot mutatja, amelyet a kimenet követ:

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
> A tanúsítvány jelszavának 4 – 12 karakterből kell állnia, amely betűkből vagy számokból áll. A speciális karakterek nem fogadhatók el.

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása

Egy Application Gateway-konfiguráció több értékből áll. Az értékek összekapcsolhatók a konfiguráció létrehozásához.

Az értékek a következők:

* **Háttér-kiszolgáló készlet**: a háttér-kiszolgálók IP-címeinek listája. A felsorolt IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános IP-címnek vagy VIP-címnek kell lenniük.
* **Háttérbeli kiszolgáló készletének beállításai**: minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll és a cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtér-port**: Ez a port az Application gatewayen megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő**: a figyelő egy előtérbeli porttal, egy protokollal (http vagy https; ezek az értékek a kis-és nagybetűk megkülönböztetésével) és az SSL-tanúsítvány nevével rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály**: a szabály köti a figyelőt és a háttér-kiszolgáló készletet, és meghatározza, hogy melyik háttér-kiszolgáló készletre irányítsa a forgalmat, amikor egy adott figyelőt üt meg. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

**További konfigurációs megjegyzések**

Az SSL-tanúsítványok konfigurálásához **Https**-re kell módosítani a **HttpListener** protokollját (megkülönböztetve a kis- és nagybetűket). Adja hozzá a **SslCert** elemet a **HttpListener** értékhez az [SSL-tanúsítványok feltöltése](#upload-ssl-certificates) szakaszban használt névvel. Az előtér-portot **443**-re kell frissíteni.

A **cookie-alapú affinitás engedélyezése**: az Application Gateway konfigurálható úgy, hogy az ügyfél-munkamenetből érkező kérések mindig ugyanarra a virtuális gépre legyenek irányítva a webfarmban. Ehhez helyezzen be egy munkamenet-cookie-t, amely lehetővé teszi, hogy az átjáró megfelelően irányítsa a forgalmat. A cookie-alapú affinitás engedélyezéséhez a **CookieBasedAffinity** paraméter beállítása legyen **Enabled** a **BackendHttpSettings** elemen belül.

A konfigurációt létrehozhat egy konfigurációs objektum létrehozásával vagy egy konfigurációs XML-fájl használatával.
Ha konfigurációs XML-fájl használatával szeretné létrehozni a konfigurációt, adja meg a következő mintát:


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

A következő lépésként állítsa be az Application Gateway-t. Megadhatja a `Set-AzureApplicationGatewayConfig` parancsmagot egy konfigurációs objektummal vagy egy konfigurációs XML-fájllal.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálását követően adja meg az `Start-AzureApplicationGateway` parancsmagot az átjáró elindításához. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag 15-20 percet is igénybe vehet.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Adja meg az `Get-AzureApplicationGateway` parancsmagot az átjáró állapotának vizsgálatához. Ha `Start-AzureApplicationGateway` sikeres volt az előző lépésben, az **állapotnak** **futnia**kell, és a **VirtualIPs** és a **DnsName** érvényes bejegyzéseket kell tartalmaznia.

Ez a példa egy olyan Application Gateway-átjárót mutat be, amely fut, és készen áll a forgalom átvételére:

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

Az általános terheléselosztási lehetőségekről további információt a következő témakörben talál:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
