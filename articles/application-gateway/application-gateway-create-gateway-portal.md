---
title: "Alkalmazásátjáró - létrehozása az Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre olyan átjárót a portál használatával"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Alkalmazásátjáró létrehozása a portállal

[Alkalmazásátjáró](application-gateway-introduction.md) egy dedikált virtuális készülék biztosító alkalmazás kézbesítési vezérlő (LÉPETT) szolgáltatásként, kínáló lehetőségeket biztosít az alkalmazás terheléselosztási különböző réteg 7. Ez a cikk végigvezeti az Azure portál használatával, és a háttérkiszolgáló tagként egy meglévő kiszolgálóról hozzáadása Alkalmazásátjáró létrehozásához szükséges lépéseket.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure portálon, a [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Alkalmazásátjáró létrehozása

Alkalmazásátjáró létrehozásához hajtsa végre az alábbi. Alkalmazásátjáró saját alhálózatba van szükség. Amikor egy virtuális hálózat létrehozásával, győződjön meg arról, hogy hagyja-e elég hely a cím több alhálózattal rendelkezik. Az Alkalmazásátjáró alhálózathoz telepítése után csak más alkalmazásátjárót adhatók hozzá.

1. Kattintson a Kedvencek ablaktáblában, a portál, **új**
1. Az **Új** panelen kattintson a **Hálózat** elemre. Az a **hálózati** panelen kattintson a **Alkalmazásátjáró**, a következő ábrán látható módon:

    ![Alkalmazásátjáró létrehozása][1]

1. Az a **alapjai** panelen megjelenő, adja meg a következő értékeket, majd kattintson a **OK**:

   | **Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Name (Név)**|AdatumAppGateway|Az Alkalmazásátjáró neve|
   |**Réteg**|Standard|Lehetséges értékek a következők: Standard és a WAF. Látogasson el [webalkalmazási tűzfal](application-gateway-web-application-firewall-overview.md) WAF tájékozódhat.|
   |**Termékváltozat-méretét**|Közepes|Standard csomag kiválasztásakor használhatók kis, közepes és nagy. WAF réteg kiválasztásakor beállítások: Közepes és nagy csak.|
   |**A példányok száma**|2|A magas rendelkezésre álláshoz az Alkalmazásátjáró példányainak száma. Példányok számát 1 csak tesztelési célokra használható.|
   |**Előfizetés**|[Az Ön előfizetése]|Válasszon ki egy előfizetést, amelyben létrehozza az alkalmazásátjárót.|
   |**Erőforráscsoport**|**Új:** AdatumAppGatewayRG|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) áttekintése című cikket.|
   |**Hely**|USA nyugati régiója||

1. Az a **beállítások** panel alatt megjelenő **virtuális hálózati**, kattintson a **virtuális hálózatot választ**. A **válasszon virtuális hálózati** panel nyílik meg.  Kattintson a **hozzon létre új** megnyitásához a **virtuális hálózat létrehozása** panelen.

   ![Válasszon egy virtuális hálózatot][2]

1. Az a **hozzon létre virtuális hálózat panel** adja meg a következő értékeket, majd kattintson a **OK**. A **virtuális hálózat létrehozása** és **válasszon virtuális hálózati** paneleken bezárásához. Ez a lépés feltölti a **alhálózati** található a **beállítások** a kiválasztott alhálózat panelről.

   | **Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Name (Név)**|AdatumAppGatewayVNET|Az Alkalmazásátjáró neve|
   |**Címterület**|10.0.0.0/16|Ez az a virtuális hálózat a címtér|
   |**Alhálózat neve**|AppGatewaySubnet|Az Alkalmazásátjáró az alhálózat nevét|
   |**Alhálózati címtartomány**|10.0.0.0/28|Ez az alhálózat lehetővé teszi több további alhálózatokat a virtuális hálózat háttér a készlet tagjainak|

1. Az a **beállítások** részen **előtérbeli IP-konfiguráció**, válassza a **nyilvános** , a **IP-cím típusa**

1. Az a **beállítások** részen **nyilvános IP-cím** kattintson **egy nyilvános IP-cím kiválasztása**, a **nyilvános IP-cím kiválasztása** panel nyílik meg, kattintson a **hozzon létre új**.

   ![Válassza ki a nyilvános IP-cím][3]

1. Az a **nyilvános IP-cím létrehozása** panelen fogadja el az alapértelmezett értéket, és kattintson a **OK**. A panel bezárása után, és feltölti a **nyilvános IP-cím** kiválasztott nyilvános IP-címmel.

1. Az a **beállítások** részen **figyelő konfigurációs**, kattintson a **HTTP** alatt **protokoll**. Adja meg a használni kívánt portot a **Port** mező.

2. Kattintson a **OK** a a **beállítások** panelt, és továbbra is.

1. Tekintse át a beállításokat a a **összegzés** panel megnyitásához, és kattintson **OK** elindítani az Alkalmazásátjáró létrehozása. Alkalmazásátjáró létrehozása egy hosszú ideig futó feladat, és időt vesz igénybe.

## <a name="add-servers-to-backend-pools"></a>Adjon hozzá kiszolgálókat háttérkészlet

Az Alkalmazásátjáró, a rendszer az alkalmazás üzemeltetésére létrehozása után az elosztott terhelésű továbbra is fel kell venni az Alkalmazásátjáró szükséges. Az IP-címek, teljesen minősített Tartományneve, vagy ezek a kiszolgálók hálózati adapterek a háttércímkészletek kerülnek.

### <a name="ip-address-or-fqdn"></a>IP-cím vagy teljes Tartományneve

1. Az Azure-portálon létrehozott alkalmazás átjáró **Kedvencek** ablaktáblán kattintson a **összes erőforrás**. Kattintson a **AdatumAppGateway** Alkalmazásátjáró az összes erőforrások panelen. Ha már kijelölt előfizetésben több erőforrást tartalmaz, megadhatja **AdatumAppGateway** a a **Szűrés név alapján...** mezőbe.

1. Az Alkalmazásátjáró létrehozott jelenik meg. Kattintson a **háttérkészletek**, és válassza ki az aktuális háttérkészlet **appGatewayBackendPool**, a **appGatewayBackendPool** panel nyílik meg.

   ![Alkalmazáskészletek átjáró háttér][4]

1. Kattintson a **tároló hozzáadása** hozzáadása a teljes tartományneveket, IP-címét. Válasszon **IP címe vagy teljes Tartományneve** , a **típus** és adja meg az IP-cím vagy teljes tartománynév mezőben. Ismételje meg ezt a lépést további háttér a készlet tagjainak. Miután befejezte az kattintson **mentése**.

### <a name="virtual-machine-and-nic"></a>Virtuális gép és a hálózati adapter

Virtuális gép hálózati adapterek háttér címkészletet tagként is hozzáadhat. Az Alkalmazásátjáró azonos virtuális hálózaton belül csak virtuális gépek a legördülő lista keresztül érhetők el.

1. Az Azure-portálon létrehozott alkalmazás átjáró **Kedvencek** ablaktáblán kattintson a **összes erőforrás**. Kattintson a **AdatumAppGateway** Alkalmazásátjáró az összes erőforrások panelen. Ha már kijelölt előfizetésben több erőforrást tartalmaz, megadhatja **AdatumAppGateway** a a **Szűrés név alapján...** mezőbe.

1. Az Alkalmazásátjáró létrehozott jelenik meg. Kattintson a **háttérkészletek**, és válassza ki az aktuális háttérkészlet **appGatewayBackendPool**, a **appGatewayBackendPool** panel nyílik meg.

   ![Alkalmazáskészletek átjáró háttér][5]

1. Kattintson a **tároló hozzáadása** hozzáadása a teljes tartományneveket, IP-címét. Válasszon **virtuális gép** , a **típus** , és válassza ki a virtuális gép és a hálózati adapter használatára. Miután befejezte az kattintson **mentése**

   > [!NOTE]
   > Az azonos virtuális hálózatban, mint az Alkalmazásátjáró csak virtuális gépek szerepelnek a legördülő listából.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, az erőforráscsoport, Alkalmazásátjáró és minden kapcsolódó erőforrás törléséhez. Ehhez az szükséges, az alkalmazás átjáró panelen jelölje ki az erőforráscsoportot, majd kattintson **törlése**.

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben Alkalmazásátjáró telepíti, és a háttér hozzáadott kiszolgálót. A következő lépésekre az Alkalmazásátjáró beállítások módosítása, és a beállító szabályok konfigurálhatja az átjárót. Ezeket a lépéseket érhetők el a következő cikkekben talál:

Megtudhatja, hogyan hozzon létre egyéni állapotteljesítmény ellátogatva [hozzon létre egy egyéni állapotmintáihoz](application-gateway-create-probe-portal.md)

Megtudhatja, hogyan konfigurálja az SSL-Feladatkiszervezést, és tegye meg a költséges SSL visszafejtési ki a webkiszolgálók ellátogatva [SSL-kiszervezés konfigurálása](application-gateway-ssl-portal.md)

Megtudhatja, hogyan az alkalmazások védelmét [webalkalmazási tűzfal](application-gateway-webapplicationfirewall-overview.md) Alkalmazásátjáró szolgáltatása.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
