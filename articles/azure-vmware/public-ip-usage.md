---
title: A nyilvános IP-funkciók használata a Virtual WAN-ban
description: Ez a cikk azt ismerteti, hogyan használható a nyilvános IP-funkció az Azure Virtual WAN-ban.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: ec8af45a98e82a7c1c657776c4fee2c3ef068dca
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744927"
---
# <a name="how-to-use-the-public-ip-functionality-in-azure-virtual-wan"></a>A nyilvános IP-funkciók használata az Azure Virtual WAN-ban

A nyilvános IP-cím az Azure VMware megoldás kapcsolatának új funkciója, és az ügyfelek kétféleképpen engedélyezhetik a nyilvános internet-hozzáférést. 

- Az alkalmazások a HTTP/HTTPS-forgalom Application Gateway Load Balancer szolgáltatásában futtathatók és közzétehetők.
- Az Azure Virtual WAN nyilvános IP-funkcióival lett közzétéve.

Az Azure VMware-megoldás saját Felhőbeli üzembe helyezésének részeként a nyilvános IP-funkciók engedélyezése után a rendszer automatikusan létrehozza és engedélyezi a szükséges összetevőket az automatizáláshoz:

-  Virtual WAN

-  Virtuális WAN hub ExpressRoute-kapcsolattal

-  Szolgáltatások Azure Firewall nyilvános IP-címmel

Ez a cikk részletesen ismerteti, hogyan használhatja a virtuális WAN nyilvános IP-funkcióit olyan erőforrások, például webkiszolgálók, virtuális gépek (VM-EK) és gazdagépek számára, amelyek nyilvános hálózaton keresztül elérhetők.

## <a name="prerequisites"></a>Előfeltételek

-   Azure VMware-megoldás környezet

-   Azure VMware-megoldási környezetben futó webkiszolgáló.

## <a name="reference-architecture"></a>Referenciaarchitektúra

:::image type="content" source="media/public-ip-usage/public-ip-architecture-diagram.png" alt-text="Nyilvános IP-architektúra diagramja" border="false" lightbox="media/public-ip-usage/public-ip-architecture-diagram.png":::

Az architektúra ábrán egy Azure VMware megoldási környezetben üzemeltetett ügyfél-webkiszolgáló látható, amely RFC1918 magánhálózati IP-címekkel van konfigurálva.  Ezt a webszolgáltatást a virtuális WAN nyilvános IP-funkcióival elérhetővé tettük az interneten.  A nyilvános IP-cím jellemzően Azure Firewall lefordított cél NAT. A DNAT-szabályokkal a tűzfalszabályok a nyilvános IP-címekre irányuló kéréseket privát címekre (webkiszolgálóra) fordítják egy porttal.

A felhasználói kérések megnyomják a tűzfalat egy nyilvános IP-címen, amelyet a rendszer a Azure Firewall DNAT-szabályainak használatával fordít a magánhálózati IP-címekre. A tűzfal ellenőrzi a NAT-táblázatot, és ha a kérelem megfelel egy bejegyzésnek, továbbítja a forgalmat a lefordított címről és portra az Azure VMware-megoldás környezetében.

A webkiszolgáló fogadja a kérést, és a kért információkat vagy oldalt válaszolja meg a tűzfalon, majd a tűzfal továbbítja az adatokat a felhasználónak a nyilvános IP-címen.

## <a name="test-case"></a>Teszteset
Ebben az esetben közzé kell tennie az IIS webkiszolgálót az interneten. Az Azure VMware-megoldás nyilvános IP-funkciója segítségével közzéteheti a webhelyet egy nyilvános IP-címen.  A NAT-szabályokat a tűzfalon konfigurálja, és a nyilvános IP-címmel elérheti az Azure VMware megoldás erőforrását (a webkiszolgálóval rendelkező virtuális gépeket).

## <a name="deploy-virtual-wan"></a>A Virtual WAN üzembe helyezése

1. Jelentkezzen be a Azure Portalba, majd keresse meg és válassza ki az **Azure VMware-megoldást**.

1. Válassza ki az Azure VMware-megoldás saját felhőjét.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-resource.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/avs-private-cloud-resource.png":::

1. A **kezelés**területen válassza a **kapcsolat**lehetőséget.

   :::image type="content" source="media/public-ip-usage/avs-private-cloud-manage-menu.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/avs-private-cloud-manage-menu.png":::

1. Válassza a **nyilvános IP-cím** lapot, majd válassza a **Konfigurálás**lehetőséget.

   :::image type="content" source="media/public-ip-usage/connectivity-public-ip-tab.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/connectivity-public-ip-tab.png":::

2.  Fogadja el az alapértelmezett értékeket, vagy módosítsa, majd válassza a **Létrehozás**lehetőséget.

   -  Virtuális nagykiterjedésű hálózati erőforráscsoport

   -  Virtuális nagykiterjedésű hálózat neve

   -  Virtuális központ – címterület

   -  Nyilvános IP-címek száma (1-100)

Az összes összetevő üzembe helyezésének befejezéséhez körülbelül egy órát vesz igénybe. Ezt az üzembe helyezést csak egyszer kell megtörténnie, hogy támogassa az Azure VMware-megoldás környezetének összes jövőbeli nyilvános IP-címeit.  

>[!TIP]
>Nyomon követheti az **értesítési** területek állapotát. 

## <a name="view-and-add-public-ip-addresses"></a>Nyilvános IP-címek megtekintése és hozzáadása

Az alábbi lépések végrehajtásával további nyilvános IP-címeket is megtekintheti és hozzáadhat.

1. A Azure Portal keresse meg és válassza a **tűzfal**lehetőséget.

1. Válasszon ki egy központilag telepített tűzfalat, majd válassza a **látogatás Azure Firewall Manager elemet a tűzfal konfigurálásához és kezeléséhez**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Válassza a **biztonságos virtuális hubok** lehetőséget, és a listából válassza ki a virtuális hubot.

   :::image type="content" source="media/public-ip-usage/select-virtual-hub.png" alt-text="Nyilvános IP-architektúra diagramja" lightbox="media/public-ip-usage/select-virtual-hub.png":::

1. A virtuális központ lapon válassza a **nyilvános IP-konfiguráció**lehetőséget, és adjon hozzá több nyilvános IP-címet, majd válassza a **Hozzáadás**lehetőséget. 

   :::image type="content" source="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/virtual-hub-page-public-ip-configuration.png":::

1. Adja meg a szükséges IP-címek számát, és válassza a **Hozzáadás**lehetőséget.

   :::image type="content" source="media/public-ip-usage/add-number-of-ip-addresses-required.png" alt-text="Nyilvános IP-architektúra diagramja" border="true":::


## <a name="create-firewall-policies"></a>Tűzfalszabályok létrehozása

Az összes összetevő üzembe helyezése után megtekintheti őket a hozzáadott erőforráscsoporthoz. A következő lépés egy tűzfal-házirend hozzáadása.

1. A Azure Portal keresse meg és válassza a **tűzfal**lehetőséget.

1. Válasszon ki egy központilag telepített tűzfalat, majd válassza a **látogatás Azure Firewall Manager elemet a tűzfal konfigurálásához és kezeléséhez**.

   :::image type="content" source="media/public-ip-usage/configure-manage-deployed-firewall.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/configure-manage-deployed-firewall.png":::

1. Válassza ki **Azure Firewall házirendeket** , majd válassza a **Azure Firewall házirend létrehozása**lehetőséget.

   :::image type="content" source="media/public-ip-usage/create-firewall-policy.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/create-firewall-policy.png":::

1. Az **alapok** lapon adja meg a szükséges adatokat, és válassza a **Tovább: DNS-beállítások**elemet. 

1. A **DNS** lapon válassza a **Letiltás**lehetőséget, majd válassza a **Tovább: szabályok**elemet.

1. Válassza a **szabálygyűjtemény hozzáadása**lehetőséget, adja meg az alábbi adatokat, majd kattintson a **Hozzáadás** , majd a **Tovább gombra: fenyegetés intelligencia**elemre.

   -  Név
   -  Szabályok gyűjteményének típusa – DNAT
   -  Prioritás
   -  Szabály-gyűjtési művelet – engedélyezés
   -  Szabály neve
   -  Forrás típusa – **IP** -cím
   -  Forrás **\***
   -  Protokoll – **TCP**
   -  Célport – **80**
   -  Cél típusa – **IP-cím**
   -  Cél – **nyilvános IP-cím**
   -  Lefordított cím – **Azure VMware-megoldás webkiszolgáló magánhálózati IP-címe**
   -  Lefordított port – **Azure VMware-megoldás webkiszolgáló-portja**

1. Hagyja meg az alapértelmezett értéket, majd válassza a **Tovább: hubok**elemet.

1. Válassza a **virtuális központ hozzárendelése**lehetőséget.

   :::image type="content" source="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/associate-virtual-hubs-azure-firewall-policy.png":::

1. Válasszon egy hubot a listából, és válassza a **Hozzáadás**lehetőséget.

   :::image type="content" source="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png" alt-text="Nyilvános IP-architektúra diagramja" border="true" lightbox="media/public-ip-usage/secure-hubs-with-azure-firewall-polcy.png":::

1. Válassza a **Tovább: Címkék** lehetőséget. 

1. Választható Hozzon létre név/érték párokat az erőforrások kategorizálásához. 

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** , majd a **Létrehozás**lehetőséget.

## <a name="limitations"></a>Korlátozások

SDDCs legfeljebb 100 nyilvános IP-cím adható meg.

## <a name="next-steps"></a>További lépések

További információ a nyilvános IP-címek [Azure Virtual WAN](../virtual-wan/virtual-wan-about.md)használatával történő használatáról.

