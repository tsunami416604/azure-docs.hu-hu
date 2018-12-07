---
title: Application Gateway létrehozása, indítása vagy törlése | Microsoft Docs
description: Ez a lap utasításokat tartalmaz egy Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.assetid: 577054ca-8368-4fbf-8d53-a813f29dc3bc
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: victorh
ms.openlocfilehash: 862d587442b4d3b752092810c33a197ba15a6b51
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994071"
---
# <a name="create-start-or-delete-an-application-gateway-with-powershell"></a>Application Gateway létrehozása, indítása vagy törlése PowerShell-lel 

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az Application Gateway számos alkalmazáskézbesítési vezérlőszolgáltatást (ADC) biztosít, beleértve a HTTP-terheléselosztást, a cookie-alapú munkamenet-affinitást, a Secure Sockets Layer (SSL) alapú kiszervezést, az egyéni állapotteszteket, a többhelyes támogatást és még sok mást. A támogatott szolgáltatások teljes listájáért látogasson el [Az Application Gateway áttekintése](application-gateway-introduction.md) című oldalra

Ez a cikk részletesen ismerteti a lépéseket, amelyekkel létrehozhat, konfigurálhat, elindíthat és törölhet egy Application Gateway-t.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ha rendelkezik meglévő virtuális hálózattal, válasszon ki egy meglévő üres alhálózatot, vagy hozzon létre egy új alhálózatot a meglévő virtuális hálózatban kizárólag az Application Gateway számára. Nem helyezheti üzembe az Application Gateway szolgáltatást az Application Gateway mögött üzembe helyezni kívánt erőforrásoktól eltérő virtuális hálózaton, kivéve ha a virtuális hálózatok között társviszony van. További információért tekintse meg a [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md) című cikket
3. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
4. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## <a name="what-is-required-to-create-an-application-gateway"></a>Mire van szükség egy Application Gateway létrehozásához?

Amikor a `New-AzureApplicationGateway` parancsot használja az Application Gateway létrehozására, a konfigurációk még nincsenek beállítva, és az újonnan létrehozott erőforrást konfigurálni kell egy XML-fájl vagy egy konfigurációs objektum használatával.

Az értékek a következők:

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy mely háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt.

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

> [!NOTE]
> Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-classic-ps.md). További információért lásd: [egyéni minták és állapotfigyelés](application-gateway-probe-overview.md).

![Példaforgatókönyv][scenario]

### <a name="create-an-application-gateway-resource"></a>Application Gateway erőforrás létrehozása

Az átjáró létrehozásához használja a `New-AzureApplicationGateway` parancsmagot, és cserélje le az értékeket a saját értékeire. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

Az alábbi példa egy új Application Gatewayt hoz létre egy „testvnet1” nevű virtuális hálózat és egy „subnet-1” nevű alhálózat használatával.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

A *Description*, az *InstanceCount* és a *GatewaySize* opcionális paraméterek.

Az átjáró létrehozásának ellenőrzéséhez használhatja a `Get-AzureApplicationGateway` parancsmagot.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Stopped
VirtualIPs    : {}
DnsName       :
```

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Kicsi, Közepes és a Nagy lehetőségek közül választhat.

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezek kitöltése akkor történik, amikor az átjáró futó állapotba kerül.

## <a name="configure-the-application-gateway"></a>Az Application Gateway konfigurálása

Az Application Gateway-t egy XML-fájl vagy konfigurációs objektum segítségével konfigurálhatja.

### <a name="configure-the-application-gateway-by-using-xml"></a>Az Application Gateway konfigurálása XML-fájl használatával

Az alábbi példában egy XML-fájllal konfigurálja az Application Gateway beállításait, és véglegesíti őket az Application Gateway-erőforráshoz.  

#### <a name="step-1"></a>1. lépés

Másolja az alábbi szöveget a Jegyzettömbbe.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>(name-of-your-frontend-port)</Name>
            <Port>(port number)</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>(name-of-your-backend-pool)</Name>
            <IPAddresses>
                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>(backend-setting-name-to-configure-rule)</Name>
            <Port>80</Port>
            <Protocol>[Http|Https]</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>(name-of-the-listener)</Name>
            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
            <Protocol>[Http|Https]</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>(name-of-load-balancing-rule)</Name>
            <Type>basic</Type>
            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
            <Listener>(name-of-the-listener)</Listener>
            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Szerkessze a zárójelek közötti értékeket a konfigurációs elemeknek megfelelően. Mentse a fájlt .xml kiterjesztéssel.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.

Az alábbi példa bemutatja, hogyan használhat egy konfigurációs fájlt az Application Gateway beállítására. A példa elosztja a nyilvános 80-as port HTTP-forgalmának a terhelését, illetve a háttérbeli 80-as portra küldi a két IP-cím közötti hálózati forgalmat.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>80</Port>
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
            <Protocol>Http</Protocol>
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

#### <a name="step-2"></a>2. lépés

A következő lépésként állítsa be az Application Gateway-t. Használja a `Set-AzureApplicationGatewayConfig` parancsmagot egy konfigurációs XML-fájllal.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
```

### <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Az Application Gateway konfigurálása konfigurációs objektum segítségével

Az alábbi példa bemutatja, hogyan konfigurálhatja az Application Gateway-t konfigurációs objektumok segítségével. Minden konfigurációs elemet külön kell konfigurálni, és utána kell hozzáadni egy Application Gateway konfigurációs objektumhoz. A konfigurációs objektum létrehozása után a `Set-AzureApplicationGateway` paranccsal véglegesíti a konfigurációt a korábban létrehozott Application Gateway-erőforráshoz.

> [!NOTE]
> Mielőtt értékeket rendelne a konfigurációs objektumokhoz, deklarálnia kell, hogy a PowerShell milyen típusú objektumot használ a tároláshoz. Az egyéni elemek létrehozásának első sora határozza meg, hogy milyen `Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name)` elemet használ a rendszer.

#### <a name="step-1"></a>1. lépés

Hozza létre az összes egyedi konfigurációs elemet.

Hozza létre az előtérbeli IP-címet az alábbi példában látható módon.

```powershell
$fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
$fip.Name = "fip1"
$fip.Type = "Private"
$fip.StaticIPAddress = "10.0.0.5"
```

Hozza létre az előtérbeli portot az alábbi példában látható módon.

```powershell
$fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
$fep.Name = "fep1"
$fep.Port = 80
```

Hozza létre a háttér-kiszolgálókészletet.

Határozza meg a háttérkiszolgáló-készlethez hozzáadni kívánt IP-címeket a következő példában látható módon.

```powershell
$servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
$servers.Add("10.0.0.1")
$servers.Add("10.0.0.2")
```

Adja hozzá az értékeket a háttérkészlet objektumhoz ($pool) a $server objektum segítségével.

```powershell
$pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
$pool.BackendServers = $servers
$pool.Name = "pool1"
```

Hozza létre a háttér-kiszolgálókészlet beállítást.

```powershell
$setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
$setting.Name = "setting1"
$setting.CookieBasedAffinity = "enabled"
$setting.Port = 80
$setting.Protocol = "http"
```

Hozza létre a figyelőt.

```powershell
$listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
$listener.Name = "listener1"
$listener.FrontendPort = "fep1"
$listener.FrontendIP = "fip1"
$listener.Protocol = "http"
$listener.SslCert = ""
```

Hozza létre a szabályt.

```powershell
$rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
$rule.Name = "rule1"
$rule.Type = "basic"
$rule.BackendHttpSettings = "setting1"
$rule.Listener = "listener1"
$rule.BackendAddressPool = "pool1"
```

#### <a name="step-2"></a>2. lépés

Rendelje hozzá az összes egyéni konfigurációs elemeket egy Application Gateway konfigurációs objektumhoz ($appgwconfig).

Adja hozzá az előtérbeli IP-címet a konfigurációhoz.

```powershell
$appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
$appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
$appgwconfig.FrontendIPConfigurations.Add($fip)
```

Adja hozzá az előtérbeli portot a konfigurációhoz.

```powershell
$appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
$appgwconfig.FrontendPorts.Add($fep)
```
Adja hozzá a háttér-kiszolgálókészletet a konfigurációhoz.

```powershell
$appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
$appgwconfig.BackendAddressPools.Add($pool)
```

Adja hozzá a háttérkészlet beállításait a konfigurációhoz.

```powershell
$appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
$appgwconfig.BackendHttpSettingsList.Add($setting)
```

Adja hozzá a figyelőt a konfigurációhoz.

```powershell
$appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
$appgwconfig.HttpListeners.Add($listener)
```

Adja hozzá a szabályt a konfigurációhoz.

```powershell
$appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
$appgwconfig.HttpLoadBalancingRules.Add($rule)
```

### <a name="step-3"></a>3. lépés
Véglegesítse a konfigurációs objektumot az Application Gateway-erőforráshoz a `Set-AzureApplicationGatewayConfig` paranccsal.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálása után indítsa el az átjárót a `Start-AzureApplicationGateway` parancsmaggal. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag futtatása akár 15–20 percet is igénybe vehet.

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Ellenőrizze az átjáró állapotát a `Get-AzureApplicationGateway` parancsmaggal. Ha a `Start-AzureApplicationGateway` sikeres volt az előző lépésben, a *State* paraméternél a „Running” (Fut) állapotnak kell szerepelnie, a *Vip* és a *DnsName* paraméternek pedig érvényes bejegyzéssel kell rendelkeznie.

Az alábbi példa egy működő Application Gateway-t mutat, amely kész fogadni a `http://<generated-dns-name>.cloudapp.net` felé tartó forgalmat.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
Name          : AppGwTest
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
Vip           : 138.91.170.26
DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net
```

## <a name="delete-the-application-gateway"></a>Application Gateway törlése

Az Application Gateway törlése:

1. Állítsa le az átjárót a `Stop-AzureApplicationGateway` parancsmaggal.
2. Távolítsa el az átjárót a `Remove-AzureApplicationGateway` parancsmaggal.
3. A `Get-AzureApplicationGateway` parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.

Az alábbi példa a `Stop-AzureApplicationGateway` parancsmag első sorát mutatja, amelyet a kimenet követ.

```powershell
Stop-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a `Remove-AzureApplicationGateway` parancsmaggal.

```powershell
Remove-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

A szolgáltatás eltávolításának ellenőrzéséhez használhatja a `Get-AzureApplicationGateway` parancsmagot. Ez a lépés nem kötelező.

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
.....
```

## <a name="next-steps"></a>További lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png
