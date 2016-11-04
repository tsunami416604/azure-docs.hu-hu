---
title: Application Gateway létrehozása, indítása vagy törlése | Microsoft Docs
description: Ez a lap utasításokat tartalmaz egy Azure Application Gateway létrehozásához, konfigurálásához, indításához és törléséhez
documentationcenter: na
services: application-gateway
author: georgewallace
manager: jdial
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/02/2016
ms.author: gwallace

---
# Application Gateway létrehozása, indítása vagy törlése
Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az Application Gateway az alábbi alkalmazáskézbesítési funkciókkal rendelkezik: HTTP-terheléselosztás, cookie-alapú munkamenet-affinitás és Secure Sockets Layer (SSL) alapú kiszervezés.

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)
> 
> 

Ez a cikk részletesen ismerteti a lépéseket, amelyekkel létrehozhat, konfigurálhat, elindíthat és törölhet egy Application Gateway-t.

## Előkészületek
1. Telepítse az Azure PowerShell-parancsmagok legújabb verzióját a Webplatform-telepítővel. A [Letöltések lap](https://azure.microsoft.com/downloads/) **Windows PowerShell** szakaszából letöltheti és telepítheti a legújabb verziót.
2. Ha rendelkezik meglévő virtuális hálózattal, válasszon ki egy meglévő üres alhálózatot, vagy hozzon létre egy új alhálózatot a meglévő virtuális hálózatban kizárólag az Application Gateway számára. Nem helyezheti üzembe az Application Gateway-t az Application Gateway mögött üzembe helyezni kívánt erőforrásoktól eltérő virtuális hálózatra.
3. Ellenőrizze, hogy rendelkezik-e működő virtuális hálózattal és hozzá tartozó érvényes alhálózattal. Győződjön meg arról, hogy egy virtuális gép vagy felhőalapú telepítés sem használja az alhálózatot. Az Application Gateway-nek egyedül kell lennie a virtuális hálózat alhálózatán.
4. A kiszolgálóknak, amelyeket az Application Gateway használatára konfigurál, már létezniük kell, illetve a virtuális hálózatban vagy hozzárendelt nyilvános/virtuális IP-címmel létrehozott végpontokkal kell rendelkezniük.

## Mire van szükség egy Application Gateway létrehozásához?
Amikor a **New-AzureApplicationGateway** parancsot használja az Application Gateway létrehozására, a konfigurációk még nincsenek beállítva, és az újonnan létrehozott erőforrást konfigurálni kell egy XML-fájl vagy egy konfigurációs objektum használatával.

Az értékek a következők:

* **Háttér-kiszolgálókészlet:** A háttérkiszolgálók IP-címeinek listája. A listán szereplő IP-címeknek a virtuális hálózat alhálózatához kell tartozniuk, vagy nyilvános/virtuális IP-címnek kell lenniük.
* **Háttér-kiszolgálókészlet beállításai:** Minden készletnek vannak beállításai, például port, protokoll vagy cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Előtérbeli port:** Az Application Gateway-en megnyitott nyilvános port. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** A figyelő egy előtérbeli porttal, egy protokollal (Http vagy Https, a kis- és a nagybetűk megkülönböztetésével) és SSL tanúsítványnévvel rendelkezik (SSL-kiszervezés konfigurálásakor).
* **Szabály:** A szabály összeköti a figyelőt és a háttérkiszolgáló-készletet, és meghatározza, hogy mely háttérkiszolgáló-készletre legyen átirányítva a forgalom, ha elér egy adott figyelőt.

## Application Gateway létrehozása
Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

> [!NOTE]
> Ha egy egyéni mintát kell konfigurálnia az Application Gateway számára: [Application Gateway létrehozása egyéni mintákkal a PowerShell használatával](application-gateway-create-probe-classic-ps.md). További információért lásd: [egyéni minták és állapotfigyelés](application-gateway-probe-overview.md).
> 
> 

### Application Gateway erőforrás létrehozása
Az átjáró létrehozásához használja a **New-AzureApplicationGateway** parancsmagot, és cserélje le az értékeket a saját értékeire. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

Az alábbi példa egy új Application Gateway-t hoz létre egy „testvnet1” nevű virtuális hálózat és egy „subnet-1” nevű alhálózat használatával.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 A *Description*, az *InstanceCount* és a *GatewaySize* opcionális paraméterek.

Az átjáró létrehozásának ellenőrzéséhez használhatja a **Get-AzureApplicationGateway** parancsmagot.

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A Kicsi, Közepes és a Nagy lehetőségek közül választhat.
> 
> 

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezek kitöltése akkor történik, amikor az átjáró futó állapotba kerül.

## Az Application Gateway konfigurálása
Az Application Gateway-t egy XML-fájl vagy konfigurációs objektum segítségével konfigurálhatja.

## Az Application Gateway konfigurálása XML-fájl használatával
Az alábbi példában egy XML-fájllal konfigurálja az Application Gateway beállításait, és véglegesíti őket az Application Gateway-erőforráshoz.  

### 1. lépés
Másolja az alábbi szöveget a Jegyzettömbbe.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

Szerkessze a zárójelek közötti értékeket a konfigurációs elemeknek megfelelően. Mentse a fájlt .xml kiterjesztéssel.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.
> 
> 

Az alábbi példa bemutatja, hogyan használhat egy konfigurációs fájlt az Application Gateway beállítására. A példa elosztja a nyilvános 80-as port HTTP-forgalmának a terhelését, illetve a háttérbeli 80-as portra küldi a két IP-cím közötti hálózati forgalmat.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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


### 2. lépés
A következő lépésként állítsa be az Application Gateway-t. Használja a **Set-AzureApplicationGatewayConfig** parancsmagot egy konfigurációs XML-fájllal.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Az Application Gateway konfigurálása konfigurációs objektum segítségével
Az alábbi példa bemutatja, hogyan konfigurálhatja az Application Gateway-t konfigurációs objektumok segítségével. Minden konfigurációs elemet külön kell konfigurálni, és utána kell hozzáadni egy Application Gateway konfigurációs objektumhoz. A konfigurációs objektum létrehozása után a **Set-AzureApplicationGateway** paranccsal véglegesíti a konfigurációt a korábban létrehozott Application Gateway-erőforráshoz.

> [!NOTE]
> Mielőtt értékeket rendelne a konfigurációs objektumokhoz, deklarálnia kell, hogy a PowerShell milyen típusú objektumot használ a tároláshoz. Az egyéni elemek létrehozásának első sora határozza meg, hogy milyen Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name) elemet használ a rendszer.
> 
> 

### 1. lépés
Hozza létre az összes egyedi konfigurációs elemet.

Hozza létre az előtérbeli IP-címet az alábbi példában látható módon.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Hozza létre az előtérbeli portot az alábbi példában látható módon.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Hozza létre a háttér-kiszolgálókészletet.

 Határozza meg a háttérkiszolgáló-készlethez hozzáadni kívánt IP-címeket a következő példában látható módon.

    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Adja hozzá az értékeket a háttérkészlet objektumhoz ($pool) a $server objektum segítségével.

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Hozza létre a háttér-kiszolgálókészlet beállítást.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Hozza létre a figyelőt.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Hozza létre a szabályt.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### 2. lépés
Rendelje hozzá az összes egyéni konfigurációs elemeket egy Application Gateway konfigurációs objektumhoz ($appgwconfig).

Adja hozzá az előtérbeli IP-címet a konfigurációhoz.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Adja hozzá az előtérbeli portot a konfigurációhoz.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Adja hozzá a háttér-kiszolgálókészletet a konfigurációhoz.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)  

Adja hozzá a háttérkészlet beállításait a konfigurációhoz.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Adja hozzá a figyelőt a konfigurációhoz.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Adja hozzá a szabályt a konfigurációhoz.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### 3. lépés
Véglegesítse a konfigurációs objektumot az Application Gateway erőforráshoz a **Set-AzureApplicationGatewayConfig** paranccsal.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Az átjáró indítása
Az átjáró konfigurálása után indítsa el az átjárót a **Start-AzureApplicationGateway** parancsmaggal. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik.

> [!NOTE]
> A **Start-AzureApplicationGateway** parancsmag futtatása akár 15–20 percet is igénybe vehet.
> 
> 

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Az átjáró állapotának ellenőrzése
Ellenőrizze az átjáró állapotát a **Get-AzureApplicationGateway** parancsmaggal. Ha a **Start-AzureApplicationGateway** sikeres volt az előző lépésben, a *State* paraméternél a „Running” állapotnak kell szerepelnie, a *Vip* és a *DnsName* paramétereknek pedig érvényes bejegyzéssel kell rendelkezniük.

Az alábbi példa egy működő Application Gateway-t mutat, amely kész fogadni a `http://<generated-dns-name>.cloudapp.net` felé tartó forgalmat.

    Get-AzureApplicationGateway AppGwTest

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


## Application Gateway törlése
Application Gateway törléséhez tegye a következőket:

1. Állítsa le az átjárót a **Stop-AzureApplicationGateway** parancsmaggal.
2. Távolítsa el az átjárót a **Remove-AzureApplicationGateway** parancsmaggal.
3. A **Get-AzureApplicationGateway** parancsmaggal győződjön meg arról, hogy az átjáró el lett távolítva.

Az alábbi példa a **Stop-AzureApplicationGateway** parancsmag első sorát mutatja, amelyet a kimenet követ.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Amint az Application Gateway leállított állapotba kerül, távolítsa el a szolgáltatást a **Remove-AzureApplicationGateway** parancsmaggal.

    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

A szolgáltatás eltávolításának ellenőrzéséhez használhatja a **Get-AzureApplicationGateway** parancsmagot. Ez a lépés nem kötelező.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## Következő lépések
Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!--HONumber=Sep16_HO4-->


