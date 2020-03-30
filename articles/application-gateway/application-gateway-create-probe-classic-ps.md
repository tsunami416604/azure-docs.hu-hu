---
title: Egyéni mintavétel létrehozása a PowerShell használatával – Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre egyéni mintavételt az Application Gateway a PowerShell használatával a klasszikus telepítési modell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e01a1cad98ded9d7ce8683b6adf38b5d53959774
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75966804"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Egyéni mintavétel létrehozása az Azure Application Gateway (klasszikus) számára a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure-portál](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben egy egyéni mintavételt ad hozzá egy meglévő alkalmazásátjáróhoz a PowerShell használatával. Az egyéni mintavételek olyan alkalmazásokhoz hasznosak, amelyek egy adott állapot-ellenőrző lappal rendelkeznek, vagy olyan alkalmazások hoz hasznosak, amelyek nem nyújtanak sikeres választ az alapértelmezett webalkalmazáshoz.

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, [hogyan hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Alkalmazásátjáró-erőforrás létrehozása egyéni mintavétellel

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
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. A kicsi, a közepes és a nagy között választhat.
> 
> 

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezek az értékek akkor jönnek létre, amikor az átjáró fut.

### <a name="configure-an-application-gateway-by-using-xml"></a>Alkalmazásátjáró konfigurálása XML használatával

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

A következő példa bemutatja, hogyan lehet egy konfigurációs fájl segítségével beállítani az alkalmazás átjáró terheléselosztása HTTP-forgalom nyilvános porton 80 és hálózati forgalmat küldeni a back-end port 80 két IP-címek egy egyéni mintavétel használatával.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.

Egy új \<konfigurációs elem probe\> egészül ki az egyéni mintavételek konfigurálásához.

A konfigurációs paraméterek a következők:

|Paraméter|Leírás|
|---|---|
|**Név** |Az egyéni mintavétel hivatkozási neve. |
| **Protocol (Protokoll)** | használt protokoll (a lehetséges értékek HTTP vagy HTTPS).|
| **Állomás** és **elérési út** | Teljes URL-elérési út, amelyet az alkalmazásátjáró hív meg a példány állapotának meghatározásához. Ha például rendelkezik egy\/http: /contoso.com/ webhelyhellyel, akkor az\/egyéni mintavétel konfigurálható a "http: /contoso.com/path/custompath.htm" értékre a mintavételi ellenőrzések sikeres HTTP-válaszához.|
| **Intervallum** | A mintavételi időköz ellenőrzéseit másodpercben állítja be.|
| **Időtúllépés** | A HTTP-válasz ellenőrzésének mintavételi idő-egyenlési idejét határozza meg.|
| **UnhealthyThreshold (Nem kifogástalan küszöbérték)** | A háttérpéldány *nem megfelelő*állapotúként való megjelöléséhez szükséges sikertelen HTTP-válaszok száma.|

A mintavétel neve hivatkozik \<a BackendHttpSettings\> konfiguráció hozzárendelése, amely háttérkészlet egyéni mintavételi beállításokat használ.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Egyéni mintavétel hozzáadása meglévő alkalmazásátjáróhoz

Az alkalmazásátjáró jelenlegi konfigurációjának módosítása három lépést igényel: Az aktuális XML-konfigurációs fájl beszereznie, módosítani kell egy egyéni mintavételt, és konfigurálni a application átjárót az új XML-beállításokkal.

1. Az XML-fájl `Get-AzureApplicationGatewayConfig`beszereznie a használatával. Ez a parancsmag a mintavételi beállítás hozzáadásához exportálja a módosítandó konfigurációs XML-t.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Nyissa meg az XML-fájlt egy szövegszerkesztőben. `<probe>` Szakasz hozzáadása `<frontendport>`után .

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

   Az XML háttérrendszerHttpSettings szakaszában adja hozzá a mintavétel nevét az alábbi példában látható módon:

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

1. Frissítse az alkalmazásátjáró konfigurációját az `Set-AzureApplicationGatewayConfig`új XML-fájllal a használatával. Ez a parancsmag frissíti az alkalmazásátjárót az új konfigurációval.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>További lépések

Ha a Secure Sockets Layer (SSL) kiszervezését szeretné konfigurálni, olvassa el [az Alkalmazásátjáró konfigurálása SSL-kiszervezéshez](application-gateway-ssl.md)című témakört.

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

