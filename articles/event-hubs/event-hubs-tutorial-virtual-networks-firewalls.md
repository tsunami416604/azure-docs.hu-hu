---
title: Azure Event Hubs – virtuális hálózatok integrációjának és tűzfalaknak a engedélyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Event Hubst virtuális hálózatokkal és tűzfalakkal a biztonságos hozzáférés érdekében.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 12/20/2019
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: f911a1513c6f89180ea51cc0de96dc8a475c7fc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437119"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Oktatóanyag: virtuális hálózatok integrációjának és tűzfalának engedélyezése Event Hubs névtérben

A [Virtual Network (VNet) szolgáltatási végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) közvetlen kapcsolaton keresztül bővítik a virtuális hálózat privát címterület-területét és a VNet identitását az Azure-szolgáltatásokhoz. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

A tűzfalak lehetővé teszik a Event Hubs névtér elérésének korlátozását adott IP-címekről (vagy IP-címtartományok).

Ez az oktatóanyag bemutatja, hogyan integrálhatja a virtuális hálózatok szolgáltatási végpontait, és hogyan állíthatja be a tűzfalakat (IP-szűrést) a meglévő Azure Event Hubs-névtérrel a portál használatával.

Ez az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Virtuális hálózatok szolgáltatásbeli végpontok integrálása a Event Hubs névtérrel.
> * A tűzfal (IP-szűrés) beállítása a Event Hubs névtérrel.

>[!WARNING]
> A virtuális hálózatok integrálásának megvalósításával megakadályozható, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
>
> Az első féltől származó integrációk nem támogatottak, ha a virtuális hálózatok engedélyezve vannak.
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal –
> * Azure Diagnostics és naplózás
> * Azure Stream Analytics
> * Event Grid integráció
> * A virtuális hálózatokon Web Apps & függvények szükségesek.
> * IoT Hub útvonalak
> * IoT Device Explorer


> [!IMPORTANT]
> A virtuális hálózatok a **standard** és a **dedikált** Event Hubsban támogatottak. Alapszintű csomag esetén nem támogatott.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot] [].

## <a name="prerequisites"></a>Előfeltételek

Kihasználunk egy meglévő Event Hubs névteret, ezért győződjön meg arról, hogy rendelkezésre áll Event Hubs névtér. Ha nem, tekintse meg [ezt az oktatóanyagot](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Először lépjen a [Azure Portalra][Azure portal] , és jelentkezzen be az Azure-előfizetésével.

## <a name="select-event-hubs-namespace"></a>Event Hubs névtér kiválasztása

Ebben az oktatóanyagban egy Event Hubs névteret hoztunk létre, és a rendszer erre fogja navigálni.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navigáljon a tűzfalak és a virtuális hálózatok felületére

A **"tűzfalak és virtuális hálózatok"** lehetőség kiválasztásához használja a portál bal oldali ablaktáblájának navigációs menüjét.

  ![Navigáljon a menühöz](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Amikor első alkalommal meglátogatja ezt az oldalt, ki kell választania a **minden hálózat** választógombot. Ez azt jelenti, hogy a Event Hubs névtér lehetővé teszi az összes bejövő kapcsolat használatát.

## <a name="add-virtual-network-service-endpoint"></a>Virtual Network szolgáltatási végpont hozzáadása

A hozzáférés korlátozásához integrálnia kell a Virtual Network szolgáltatási végpontot ehhez a Event Hubs névtérhez.

1. A lap tetején található **kijelölt hálózatok** választógombra kattintva engedélyezheti a lap további részeit a menüparancsokkal.
  ![kiválasztott hálózatok](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. A lap Virtual Network szakaszában válassza a ***meglévő virtuális hálózat hozzáadása***lehetőséget. Ekkor megjelenik a panel, amely lehetővé teszi egy már létrehozott virtuális hálózat kiválasztását.
  ![meglévő virtuális hálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Válassza ki a Virtual Network a listából, és válassza ki az alhálózatot.
   ![válassza az alhálózat lehetőséget](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. A Virtual Network a listához való hozzáadása előtt engedélyeznie kell a szolgáltatási végpontot. Ha a szolgáltatási végpont nincs engedélyezve, a portál felszólítja, hogy engedélyezze.
  ![az alhálózat kiválasztása és a végpont engedélyezése](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Ha nem tudja engedélyezni a szolgáltatási végpontot, figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatás végpontját az ARM sablonnal. Ez a funkció nem érhető el a portálon.

5. Miután engedélyezte a szolgáltatás végpontját a kiválasztott alhálózaton, folytathatja a hozzáadását az engedélyezett virtuális hálózatok listájához.
  ![alhálózat hozzáadása a végpont engedélyezése után](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. A felső menüszalag **Mentés** gombjára kattintva mentse a virtuális hálózat konfigurációját a szolgáltatásban. Várjon néhány percet, amíg megtörténik a megerősítés, hogy megjelenjen a portál értesítései.

## <a name="add-firewall-for-specified-ip"></a>Tűzfal hozzáadása a megadott IP-címhez

Korlátozott számú IP-címtartomány vagy adott IP-cím esetén a tűzfalszabályok használatával korlátozható a hozzáférés a Event Hubs névtérhez.

1. A lap tetején található **kijelölt hálózatok** választógombra kattintva engedélyezheti a lap további részeit a menüparancsokkal.
  ![kiválasztott hálózatok](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. A **tűzfal** szakaszban, a ***címtartomány*** rács alatt egy vagy több konkrét IP-címet vagy IP-címtartományt is hozzáadhat.
  IP-címek ![hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Miután hozzáadta a több IP-címet (vagy IP-címtartományt), a felső menüszalagon **mentse a mentést** , hogy a konfiguráció a szolgáltatás oldalán legyen mentve. Várjon néhány percet, amíg megtörténik a megerősítés, hogy megjelenjen a portál értesítései.
  ![adjon hozzá IP-címeket, és nyomja meg a Mentés gombot](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Az aktuális IP-cím hozzáadása a tűzfalszabályok számára

1. Az aktuális IP-címet gyorsan is hozzáadhatja az ***ügyfél IP-címének hozzáadása (az aktuális IP-cím)*** jelölőnégyzet bejelölésével, közvetlenül a ***címtartomány*** rácsa felett.
  ![aktuális IP-cím hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Miután hozzáadta a jelenlegi IP-címét a tűzfalszabályok számára, a felső menüszalagon a **Mentés** gombra kattintva ellenőrizheti, hogy a konfiguráció a szolgáltatás oldalán van-e mentve. Várjon néhány percet, amíg megtörténik a megerősítés, hogy megjelenjen a portál értesítései.
  ![az aktuális IP-cím hozzáadása és a Mentés megnyomva](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Összegzés

Ebben az oktatóanyagban integrált Virtual Network-végpontokat és tűzfalszabályok egy meglévő Event Hubs névtérrel. Megtanulta, hogyan:
> [!div class="checklist"]
> * Virtuális hálózatok szolgáltatásbeli végpontok integrálása a Event Hubs névtérrel.
> * A tűzfal (IP-szűrés) beállítása a Event Hubs névtérrel.


[Azure portal]: https://portal.azure.com/
