---
title: SNAT magánhálózati IP-címtartományok Azure Firewall
description: A SNAT IP-címtartományok is konfigurálhatók.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 96b06e21d6e81010109ed8314980f34db0100e3b
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789378"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>SNAT magánhálózati IP-címtartományok Azure Firewall

A Azure Firewall a nyilvános IP-címekre irányuló összes kimenő forgalom automatikus SNAT biztosítja. Alapértelmezés szerint a Azure Firewall nem SNAT meg a hálózati szabályokkal, ha a célként megadott IP-cím az [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)magánhálózati IP-címtartomány része. Az alkalmazási szabályok mindig [transzparens proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) használatával lesznek alkalmazva a cél IP-címről függetlenül.

Ez a logika jól működik, ha közvetlenül az internetre irányítja át a forgalmat. Ha azonban engedélyezte a [kényszerített bújtatást](forced-tunneling.md), az internethez kötött forgalom a AzureFirewallSubnet egyik címfordítást, a forrást pedig a helyszíni tűzfalból elrejti.

Ha a szervezete nyilvános IP-címtartományt használ a magánhálózatok számára, Azure Firewall SNATs a forgalmat a AzureFirewallSubnet egyik tűzfal magánhálózati IP-címére. Azonban úgy is konfigurálhatja a Azure Firewallt, hogy **ne** SNAT a nyilvános IP-címtartományt.

Ha úgy szeretné konfigurálni a Azure Firewallt, hogy soha ne SNAT meg a cél IP-címről, használja a **0.0.0.0/0 értéket** a magánhálózati IP-címtartomány alapján. Ezzel a konfigurációval a Azure Firewall soha nem irányíthatja át a forgalmat közvetlenül az internetre. Ha úgy szeretné beállítani a tűzfalat, hogy mindig SNAT a célhelytől függetlenül, használja a **255.255.255.255/32** protokollt magánhálózati IP-címtartományként.

## <a name="configure-snat-private-ip-address-ranges---azure-powershell"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure PowerShell

A Azure PowerShell használatával megadhatja a tűzfal magánhálózati IP-címtartományt.

### <a name="new-firewall"></a>Új tűzfal

Új tűzfal esetén a Azure PowerShell parancs a következő:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> A IANAPrivateRanges a Azure Firewall aktuális alapértelmezett értékeire van kiterjesztve, míg a többi tartomány hozzá van adva. Ha meg szeretné őrizni a IANAPrivateRanges alapértelmezett értékeit a privát tartomány specifikációjában, akkor az `PrivateRange` alábbi példákban látható módon meg kell maradnia a specifikációban.

További információ: [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Meglévő tűzfal

Meglévő tűzfal konfigurálásához használja a következő Azure PowerShell parancsokat:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Sablonok

A szakaszhoz a következőket adhatja hozzá `additionalProperties` :

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="configure-snat-private-ip-address-ranges---azure-portal"></a>SNAT magánhálózati IP-címtartományok konfigurálása – Azure Portal

A Azure Portal segítségével megadhatja a tűzfal magánhálózati IP-címtartományt.

1. Válassza ki az erőforráscsoportot, majd válassza ki a tűzfalat.
2. Az **Áttekintés** oldalon válassza a **magánhálózati IP-címtartományok**elemet, majd az alapértelmezett értéket az **IANA RFC 1918**.

   Megnyílik a **privát IP-előtag szerkesztése** oldal:

   :::image type="content" source="media/snat-private-range/private-ip.png" alt-text="Magánhálózati IP-előtagok szerkesztése":::

1. Alapértelmezés szerint a **IANAPrivateRanges** konfigurálva van.
2. Szerkessze a környezet magánhálózati IP-címeinek tartományát, majd válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések

- Ismerkedjen meg [Azure Firewall kényszerített bújtatással](forced-tunneling.md).