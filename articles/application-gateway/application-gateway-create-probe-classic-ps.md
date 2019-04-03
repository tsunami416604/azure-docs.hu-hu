---
title: Hozzon létre egy egyéni mintát – az Azure Application Gateway – klasszikus PowerShell |} A Microsoft Docs
description: Ismerje meg, hogyan lehet egyéni mintavétel létrehozása az Application Gateway-lel a klasszikus üzemi modellben
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 01c1768f60da98206f0dfd041745428256f545fc
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58861879"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Egyéni mintavétel létrehozása az Azure Application Gateway (klasszikus) PowerShell-lel

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy meglévő application gateway a PowerShell használatával vegyen fel egy egyéni mintát. Az alkalmazásokat, egy adott állapotellenőrzési oldalt, vagy az alapértelmezett webalkalmazást a sikeres válasz nem biztosító alkalmazások egyéni minták hasznosak lehetnek.

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](application-gateway-create-probe-ps.md)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Egyéni mintavétel létrehozása az application gateway-erőforrás

Az átjáró létrehozásához használja a `New-AzureApplicationGateway` parancsmagot, és cserélje le az értékeket a saját értékeire. Az átjáró használati díjának felszámolása ekkor még nem kezdődik el. A használati díj felszámolása egy későbbi lépésnél kezdődik, amikor az átjáró sikeresen elindul.

Az alábbi példa egy új Application Gateway-t hoz létre egy „testvnet1” nevű virtuális hálózat és egy „subnet-1” nevű alhálózat használatával.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Az átjáró létrehozásának ellenőrzéséhez használhatja a `Get-AzureApplicationGateway` parancsmagot.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. Kicsi, közepes és nagy közül választhat.
> 
> 

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezeket az értékeket jönnek létre, ha az átjáró futó állapotba kerül.

### <a name="configure-an-application-gateway-by-using-xml"></a>Application gateway konfigurálása XML-fájl használatával

Az alábbi példában egy XML-fájllal konfigurálja az Application Gateway beállításait, és véglegesíti őket az Application Gateway-erőforráshoz.  

Másolja az alábbi szöveget a Jegyzettömbbe.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Szerkessze a zárójelek közötti értékeket a konfigurációs elemeknek megfelelően. Mentse a fájlt .xml kiterjesztéssel.

Az alábbi példa bemutatja, hogyan állítsa be a nyilvános 80-as port HTTP forgalmának terheléselosztására, és a hálózati forgalom elküldése a 80-as porton háttérbeli két IP-cím használatával egy egyéni mintát között az application gateway konfigurációs fájl használatával.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.

Egy új konfigurációelemet \<mintavételi\> adnak hozzá az egyéni minták konfigurálása.

A konfigurációs paraméterek a következők:

|Paraméter|Leírás|
|---|---|
|**Name (Név)** |Egyéni mintavétel hivatkozás neve. |
| **Protokoll** | Használt protokoll (a lehetséges értékek: HTTP vagy HTTPS).|
| **Gazdagép** és **elérési útja** | Teljes URL-cím, amelyeket az application gateway-példány állapotának hívnak. Például, ha egy webhely http:\//contoso.com/, majd az egyéni mintát is konfigurálható a "http:\//contoso.com/path/custompath.htm" a mintavétel ellenőrzi, hogy rendelkezik egy sikeres HTTP-választ.|
| **Intervallum** | Konfigurálja a mintavételező időköz másodpercben.|
| **Időtúllépés** | Határozza meg a mintavétel időkorlátja egy HTTP-válasz ellenőrzés.|
| **UnhealthyThreshold** | Ez a jelző azt a háttér-példányhoz szükséges sikertelen HTTP-válaszok száma *nem megfelelő állapotú*.|

A mintavétel nevének hivatkozik a \<BackendHttpSettings\> konfigurációját, és mely háttérkészlet-hozzárendelése egyéni mintavétel beállításait használja.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Egyéni mintavétel hozzáadása egy meglévő application gateway

Az application gateway az aktuális konfiguráció módosításának három lépésből áll: Az aktuális XML konfigurációs fájl beolvasása, egy egyéni mintát kell módosítani, és az application gateway konfigurálása XML új beállításokkal.

1. Az XML-fájl első használatával `Get-AzureApplicationGatewayConfig`. Ez a parancsmag exportálja a konfigurációs XML módosítani kell, hogy adjon hozzá egy mintavételi beállítást.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Nyissa meg az XML-fájlt egy szövegszerkesztőben. Adjon hozzá egy `<probe>` szakasz után `<frontendport>`.

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   XML-kód a backendHttpSettings területen adja hozzá a mintavétel nevének, a következő példában látható módon:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Mentse az XML-fájlt.

1. Frissítse az application gateway-konfigurációt az új XML-fájl használatával `Set-AzureApplicationGatewayConfig`. Ez a parancsmag frissíti az application gateway az új konfigurációt.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy Secure Sockets Layer (SSL) alapú kiszervezés konfigurálása, lásd: [konfigurálása az application gateway SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

