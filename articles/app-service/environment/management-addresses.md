---
title: Az Azure App Service Environment-környezet kezelési címeit
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
ms.date: 05/29/2018
ms.author: ccompy
ms.openlocfilehash: b54d5abed3e0d29103f5a15ea7fb6dc37dba240e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967810"
---
# <a name="app-service-environment-management-addresses"></a>App Service Environment-környezet kezelési címeit

Az App Service Environment (ASE) üzembe helyezése az Azure App Service az Azure virtuális hálózaton (VNet) egyik alhálózatában.  Az ASE-t a felügyeleti sík, az Azure App Service által használt elérhetőknek kell lenniük.  Az ASE felügyeleti adatforgalomra a felhasználó általi hálózaton. Ha ez a forgalom blokkolva van, vagy misrouted, az ASE fel lesz függesztve. Az ASE hálózati függőségekkel kapcsolatos további tudnivalókért olvassa el [megfontolandó szempontok és az App Service Environment hálózati][networking]. Általános információk az ASE: Kezdésként használhatja az [az App Service Environment bemutatása][intro].

Ez a dokumentum felügyeleti forgalmat az ASE App Service-ben forrás címeinek listája, és két fontos célokra szolgál.  

1. Ezek a címek segítségével való zárolását, így a bejövő forgalmat hálózati biztonsági csoportok létrehozása.  
2. Ezeket a címeket, a kényszerített bújtatás központi telepítések támogatásához az útvonalakat hozhat létre. Megtudhatja, hogyan konfigurálhatja az ASE számára, ahol kimenő adatforgalom a helyszíni környezetben működnek, olvassa el a [az ASE konfigurálása kényszerített bújtatással][forcedtunnel]

Minden ASE rendelkezik egy nyilvános virtuális IP-CÍMEK melyik felügyeleti forgalom kerül. Ezek a címek bejövő felügyeleti forgalmat származik 454 és a 455 portra a nyilvános VIP-Címéhez, az ASE-t.  

## <a name="list-of-management-addresses"></a>Felügyeleti címek listája ##

| Régió | Címek |
|--------|-----------|
| Az összes nyilvános régióban | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246 |
| A Microsoft Azure Government (Fairfax vagy MAG) | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="get-your-management-addresses-from-api"></a>API-ból a kezelési címek beolvasása ##

A kezelési címek, amelyek megfelelnek az ASE-t a következő API-hívással történő listázhatja.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Az API-t adja vissza, amely tartalmazza az összes bejövő címet az ASE JSON-dokumentumok. A címek listáját tartalmazza a kezelési címek az ASE-t és az ASE alhálózat címtartománya maga által használt VIP-címet.  

Az API-t hívja a [armclient](https://github.com/projectkudu/ARMClient) használja a következő parancsokat, de helyettesítsen be, az az előfizetés-azonosító, az erőforráscsoportot és az ASE neve.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
