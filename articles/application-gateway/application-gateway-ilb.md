---
title: "Az Azure alkalmazás átjáró használata belső elosztott terhelésű |} Microsoft Docs"
description: "Ezen a lapon egy Azure-alkalmazás átjáró konfigurálása egy belső elosztott terhelésű végpont utasításokat tartalmazza."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 7403d28e-909f-46a2-b282-43a8e942f53c
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 7ca9307e8a78f6dade4b231fa3a0d83a68af3f21
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Alkalmazásátjáró létrehozása belső Load Balancerrel (ILB)

> [!div class="op_single_selector"]
> * [Klasszikus Azure PowerShell](application-gateway-ilb.md)
> * [Azure Resource Manager PowerShell](application-gateway-ilb-arm.md)

Alkalmazásátjáró Internet felé néző virtuális IP-cím vagy egy belső végpont nem kommunikál az internettel, más néven belső Load Balancer (ILB) végpont is kell konfigurálni. Az átjáró konfigurálása egy ILB akkor hasznos, a belső üzleti alkalmazások nem kommunikál az internettel. Is célszerű a szolgáltatások vagy rétegek, egy többrétegű alkalmazást, amely a nem kommunikál az internettel biztonsági határ helyezkedik el, de továbbra is szükséges a ciklikus multiplexelés terheléselosztási, a munkamenet tölcsérútvonalak vagy az SSL-lezárást belül. Ez a cikk részletesen ismerteti egy Application Gateway ILB-hez történő konfigurálásának lépéseit.

## <a name="before-you-begin"></a>Előkészületek

1. Telepítse az Azure PowerShell-parancsmagok a Webplatform-telepítővel történő legújabb verzióját. Töltse le, és telepítse a legújabb verziót a **Windows PowerShell** szakasza a [letöltési oldala](https://azure.microsoft.com/downloads/).
2. Győződjön meg arról, hogy rendelkezik-e egy működő virtuális alhálózattal rendelkező hálózatot érvényes.
3. Ellenőrizze, a virtuális hálózaton, vagy egy nyilvános IP-cím/VIP hozzárendelve a háttérkiszolgálók rendelkezik.

Alkalmazásátjáró létrehozásához a megadott sorrendben hajtsa végre az alábbi lépéseket. 

1. [Alkalmazásátjáró létrehozása](#create-a-new-application-gateway)
2. [Az átjáró konfigurálása](#configure-the-gateway)
3. [Állítsa be az átjáró konfigurálása](#set-the-gateway-configuration)
4. [Az átjáró elindítása](#start-the-gateway)
5. [Az átjáró ellenőrzése](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Alkalmazásátjáró létrehozása:

**Az átjáró létrehozása**, használja a `New-AzureApplicationGateway` parancsmag, az értékeket a saját cserélje le. Ne feledje, hogy az átjáró használati díjának felszámolása nem indul el ezen a ponton. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

```
VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399
```

**Érvényesítéséhez** , hogy az átjáró lett létrehozva, használja a `Get-AzureApplicationGateway` parancsmag. 

A minta *leírás*, *InstanceCount*, és *GatewaySize* opcionális paraméterek. Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. Kis és nagy más elérhető értékek. *VIP* és *DnsName* jelennek meg az üres, mert az átjáró még nem kezdődött meg. Ezek kitöltése akkor történik, amikor az átjáró futó állapotba kerül. 

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
VERBOSE: 4:39:39 PM - Begin Operation:
Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
Operation: Get-AzureApplicationGateway
Name: AppGwTest    
Description: 
VnetName: testvnet1 
Subnets: {Subnet-1} 
InstanceCount: 2 
GatewaySize: Medium 
State: Stopped 
VirtualIPs: 
DnsName:
```

## <a name="configure-the-gateway"></a>Az átjáró konfigurálása
Egy alkalmazás átjáró konfigurálása több érték áll. Az értékek is kötődik együtt a konfiguráció létrehozásához.

Az értékek a következők:

* **Kiszolgáló háttérkészlet:** e a háttérkiszolgálók IP-címek listáját. A felsorolt IP-címek vagy a virtuális hálózat alhálózathoz kell tartoznia, vagy egy nyilvános IP-cím/VIP kell lennie. 
* **Háttérkiszolgáló-készlet beállításai:** Minden készlet rendelkezik olyan beállításokkal, mint a port, a protokoll vagy a cookie-alapú affinitás. Ezek a beállítások egy adott készlethez kapcsolódnak, és a készlet minden kiszolgálójára érvényesek.
* **Az elülső rétegbeli portot:** Ez a port nem a nyilvános portot nyit meg az Alkalmazásátjáró. Amikor a forgalom eléri ezt a portot, a port átirányítja az egyik háttérkiszolgálóra.
* **Figyelő:** a figyelő rendelkezik egy elülső rétegbeli portot, a protokollt (Http vagy Https, amelyek kis-és nagybetűket), és az SSL-tanúsítvány neve (ha az SSL beállításának-kiszervezés). 
* **Szabály:** a szabály a figyelő és a kiszolgáló háttérkészlet van kötve, és határozza meg, melyik kiszolgáló háttérkészlet a forgalom legyenek irányítva, ha a találatok száma a egy adott figyelő. Jelenleg csak a *basic* szabály támogatott. A *basic* szabály a ciklikus időszeleteléses terheléselosztás.

A konfigurációs hogyan hozhat létre, vagy hozzon létre egy konfigurációs objektumot, vagy egy konfigurációs XML-fájl használatával. A konfiguráció létrehozásához egy konfigurációs XML-fájl használatával, használja az alábbi minta.

Vegye figyelembe a következőket:

* A *frontendipconfiguration osztálya lehet* elem Alkalmazásátjáró konfigurálható egy ILB vonatkozó ILB részleteit ismerteti. 
* Az előtér-IP- *típus* "Private" értékre kell állítani
* A *StaticIPAddress* kell beállítani a kívánt belső IP-, amelyen az átjáró fogadja a forgalmat. Vegye figyelembe, hogy a *StaticIPAddress* elem nem kötelező megadni. Ha nincs olyan elérhető belső IP-címet a telepített alhálózatból be van állítva, van kiválasztva. 
* Értékét a *neve* elemben megadott *FrontendIPConfiguration* a HTTPListener használandó *FrontendIP* tekintse meg a FrontendIPConfiguration elemet.
  
  **Konfigurációs XML-minta**
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name> 
            <Type>Private</Type> 
            <StaticIPAddress>10.0.0.10</StaticIPAddress> 
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>
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
            <FrontendIP>fip1</FrontendIP>
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


## <a name="set-the-gateway-configuration"></a>Állítsa be az átjáró konfigurálása
A következő lesznek állítva az Alkalmazásátjáró. Használhatja a `Set-AzureApplicationGatewayConfig` parancsmag egy konfigurációs objektumot, vagy egy konfigurációs XML-fájlt. 

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

```
VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d
```

## <a name="start-the-gateway"></a>Az átjáró indítása

Az átjáró konfigurálása után indítsa el az átjárót a `Start-AzureApplicationGateway` parancsmaggal. Az Application Gateway használati díjának felszámolása az átjáró sikeres indítása után kezdődik. 

> [!NOTE]
> A `Start-AzureApplicationGateway` parancsmag előfordulhat, hogy akár 15-20 percig tarthat. 
> 
> 

```powershell
Start-AzureApplicationGateway AppGwTest 
```

```
VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error 
----       ----------------     ------------                             ----
Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b
```

## <a name="verify-the-gateway-status"></a>Az átjáró állapotának ellenőrzése

Használja a `Get-AzureApplicationGateway` parancsmag az átjáró állapotának ellenőrzéséhez. Ha `Start-AzureApplicationGateway` sikeres volt az előző lépésben, az állapot kell *futtató*, és a Vip és DnsName érvényes bejegyzést kell rendelkeznie. Ez a példa bemutatja a parancsmag az első sorba a kimeneti követ. Ez a példa az átjáró fut, és készen áll a forgalom igénybe vehet. 

> [!NOTE]
> Az Alkalmazásátjáró konfigurálva van a konfigurált ILB végpont 10.0.0.10 ebben a példában a forgalom fogadására.

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
VirtualIPs    : {10.0.0.10}
DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net
```

## <a name="next-steps"></a>További lépések
Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

