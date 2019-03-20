---
title: App Service Environment-környezet kezelési címeit – Azure
description: Felsorolja a a parancs és egy App Service Environment-környezet kezelési címeit
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 632fa14bd96eaee2ca58b59dd855584c1fd961e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010105"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment-környezet kezelési címeit

Az App Service Environment (ASE) üzembe helyezése az Azure App Service az Azure virtuális hálózaton (VNet) egyik alhálózatában.  Az ASE-t a felügyeleti sík, az Azure App Service által használt elérhetőknek kell lenniük.  Az ASE felügyeleti adatforgalomra a felhasználó általi hálózaton. Ha ez a forgalom blokkolva van, vagy misrouted, az ASE fel lesz függesztve. Az ASE hálózati függőségekkel kapcsolatos további tudnivalókért olvassa el [megfontolandó szempontok és az App Service Environment hálózati][networking]. Általános információk az ASE: Kezdésként használhatja az [az App Service Environment bemutatása][intro].

Minden ASE rendelkezik egy nyilvános virtuális IP-CÍMEK melyik felügyeleti forgalom kerül. Ezek a címek bejövő felügyeleti forgalmat származik 454 és a 455 portra a nyilvános VIP-Címéhez, az ASE-t. Ez a dokumentum a felügyeleti forgalmat az ASE App Service-ben forrás címeinek listája. Ezek a címek szerepelnek, a AppServiceManagement nevű Szolgáltatáscímke.

A szolgáltatás címke, a hálózati biztonsági csoportokban AppServiceManagement nevű annak érdekében, hogy a bejövő felügyeleti forgalmak zárolását, az ASE-t is használhatja.  

Az alábbi esetekben címek konfigurálható egy útválasztási táblázatot. Ez akkor fontos, ha az ASE kényszerített módon működő virtuális hálózat, ahol ellenkező esetben előfordulhat, hogy rendelkezik egy aszimmetrikus útválasztás probléma bújtatott. Megtudhatja, hogyan konfigurálhatja az ASE számára, ahol kimenő adatforgalom a helyszíni környezetben működnek, olvassa el a [az ASE konfigurálása kényszerített bújtatással][forcedtunnel]

## <a name="list-of-management-addresses"></a>Felügyeleti címek listája ##

| Régió | Címek |
|--------|-----------|
| Az összes nyilvános régióban | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246, 52.225.177.153, 65.52.172.237, 52.151.25.45, 40.124.47.188 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>A hálózati biztonsági csoportok konfigurálása

Hálózati biztonsági csoportokkal, nem kell az egyes címek vagy a saját konfigurációs karbantartásával foglalkoznia. Egy IP-szolgáltatást, hogy mindig naprakészek lehetnek az összes cím AppServiceManagement nevű címke van. Szeretne használni az IP-szolgáltatáscímke az NSG-t, lépjen a portálra, nyissa meg a hálózati biztonsági csoportok felhasználói felület, és válassza ki a bejövő biztonsági szabályok. Ha rendelkezik egy meglévő szabályt a bejövő felügyeleti forgalmak szerkesztheti. Ha ez az NSG nem jött létre az ASE-t, vagy ha minden új, majd válassza ki **Hozzáadás**. A forrás legördülő menüben válassza a **Szolgáltatáscímke**.  Válassza a forrás-szolgáltatáscímke **AppServiceManagement**. Alkalmazásport-tartományok beállítása a forrás \*, célt a **bármely**, Célport-tartományokkal való **454-455**, protokoll használatával **TCP**, és művelet **engedélyezése** . Ha a szabályt, majd be kell állítani a prioritást. 

![az NSG-KET a szolgáltatáscímke létrehozása][1]

## <a name="configuring-a-route-table"></a>Útválasztási táblázat konfigurálása

A kezelési címek helyezhető egy útválasztási táblázatot egy következő ugrási típusú internettel annak biztosításához, hogy az összes bejövő felügyeleti forgalmak tudni lépjen vissza, ha ugyanazt az utat az. Ezeket az útvonalakat van szükség, amikor konfigurálása kényszerített bújtatás. Az útválasztási táblázat létrehozása, használhatja a portal, PowerShell vagy az Azure CLI használatával.  Az alábbiakban a parancsok hozzon létre egy útválasztási táblázatot, egy PowerShell-parancssort az Azure CLI használatával. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32 
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32 
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32

Az útvonaltábla létrehozása után meg kell azt a az ASE-alhálózattal.  

## <a name="get-your-management-addresses-from-api"></a>API-ból a kezelési címek beolvasása ##

A kezelési címek, amelyek megfelelnek az ASE-t a következő API-hívással történő listázhatja.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Az API-t adja vissza, amely tartalmazza az összes bejövő címet az ASE JSON-dokumentumok. A címek listáját tartalmazza a kezelési címek az ASE-t és az ASE alhálózat címtartománya maga által használt VIP-címet.  

Az API-t hívja a [armclient](https://github.com/projectkudu/ARMClient) használja a következő parancsokat, de helyettesítsen be, az az előfizetés-azonosító, az erőforráscsoportot és az ASE neve.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
