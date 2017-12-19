---
title: "Hozzon létre egy egyéni mintavétel - Azure Application Gateway - klasszikus PowerShell |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy egyéni mintavétel az Alkalmazásátjáró a klasszikus üzembe helyezési modellel PowerShell használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 338a7be1-835c-48e9-a072-95662dc30f5e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: davidmu
ms.openlocfilehash: b167a0584740a4e583a35bd6d44ec5d616ba04f7
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Hozzon létre egy egyéni mintavétel az Azure Application Gateway (klasszikus) PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ebben a cikkben ad hozzá egy egyéni mintavételt meglévő Alkalmazásátjáró a PowerShell használatával. Egyéni mintavételt az alkalmazásokat, amelyek egy adott állapotának ellenőrzése lapon vagy az alapértelmezett webes alkalmazás a sikeres válasz nem biztosító alkalmazások hasznosak.

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](application-gateway-create-probe-ps.md)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Application Gateway létrehozásához tegye a következőket:

1. Egy Application Gateway erőforrás létrehozása.
2. Hozzon létre egy konfigurációs XML-fájlt vagy konfigurációs objektumot.
3. Véglegesítse az újonnan létrehozott Application Gateway erőforrás konfigurációját.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Egy egyéni mintavételi alkalmazás átjáró erőforrás létrehozása

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
> Az *InstanceCount* alapértelmezett értéke 2, a maximális értéke pedig 10. A *GatewaySize* alapértelmezett értéke Közepes. Kis, közepes és nagy közül választhat.
> 
> 

A *VirtualIPs* és a *DnsName* paraméterek azért üresek, mert az átjáró még nem indult el. Ezek az értékek jönnek létre, ha az átjáró már szerepel a futó állapotot.

### <a name="configure-an-application-gateway-by-using-xml"></a>Alkalmazásátjáró konfigurálása XML használatával

Az alábbi példában egy XML-fájllal konfigurálja az Application Gateway beállításait, és véglegesíti őket az Application Gateway-erőforráshoz.  

Másolja az alábbi szöveget a Jegyzettömbbe.

```xml
<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
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

A következő példa bemutatja, hogyan egy konfigurációs fájl használatával állítsa be a nyilvános port 80-as HTTP-forgalom és a hálózati forgalom elküldése a háttér-két IP-címre egy egyéni mintavételt a 80-as port az Alkalmazásátjáró.

> [!IMPORTANT]
> A Http és Https protokollelem különbséget tesz a kis- és a nagybetűk között.

Egy új konfigurációelemet \<mintavételi\> konfigurálása egyéni mintavételt kerül.

A konfigurációs paraméterek a következők:

|Paraméter|Leírás|
|---|---|
|**Name (Név)** |Egyéni mintavétel hivatkozás neve. |
* **Protokoll** | Használt protokoll (a lehetséges értékek: HTTP vagy HTTPS).|
| **Állomás** és **elérési útja** | Fejezze be az URL-címet, amelyet az Alkalmazásátjáró példány állapotának meghatározására. Például ha egy webhely http://contoso.com/, majd az egyéni vizsgálat is konfigurálható "http://contoso.com/path/custompath.htm" mintavételi ellenőrzi, hogy rendelkezik a sikeres HTTP-válasz.|
| **Időköz** | Konfigurálja a mintavételi intervallum ellenőrzések másodpercben.|
| **Időtúllépés** | Egy HTTP-válasz ellenőrzése mintavételi időtúllépésének meghatározása.|
| **UnhealthyThreshold** | A szükséges jelzőt a háttér-példányához, a sikertelen HTTP-válaszok száma *sérült*.|

A mintavétel nevének hivatkozik a \<BackendHttpSettings\> konfigurációját, és rendeljen mely háttér címkészletet egyéni tesztműveleti beállításokat használja.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Egyéni tesztműveleti hozzáadása egy meglévő Alkalmazásátjáró

Az Alkalmazásátjáró a jelenlegi konfiguráció módosításához szükséges három lépés: az aktuális XML konfigurációs fájl beolvasása módosítani szeretné, hogy egy egyéni mintavételt és az új XML-beállítások konfigurálása az Alkalmazásátjáró.

1. Az XML-fájl segítségével könnyebben nyerhet `Get-AzureApplicationGatewayConfig`. Ez a parancsmag exportálja a konfigurációs XML-kód módosítani kell hozzáadni a mintavételi beállítást.

  ```powershell
  Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
  ```

1. Nyissa meg az XML-fájlt egy szövegszerkesztőben. Adja hozzá a `<probe>` után szakasz `<frontendport>`.

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

  Az XML a backendhttpsettings beállítások területen adja hozzá a mintavétel nevének a következő példában látható módon:

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

1. Frissítse az alkalmazás átjáró konfigurációját az új XML-fájl használatával `Set-AzureApplicationGatewayConfig`. Ez a parancsmag az Alkalmazásátjáró frissítése az új konfigurációt.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Következő lépések

Ha a Secure Sockets Layer (SSL) kiszervezési konfigurálni szeretné, lásd: [konfigurálása az SSL-kiszervezés Alkalmazásátjáró](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

