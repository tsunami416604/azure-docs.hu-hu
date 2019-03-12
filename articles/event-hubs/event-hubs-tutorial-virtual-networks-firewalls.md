---
title: Oktatóanyag – az engedélyezése a virtuális hálózatok, integráció és az Event Hubs-tűzfalak |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja az Event Hubs virtuális hálózatok és a tűzfalak biztonságosan elérni.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: 9eea40a8ad2f08099b2662a0e7539c326d4d143e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779045"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Oktatóanyag: Virtuális hálózat integrációja és a tűzfalak engedélyezése Event Hubs-névtér

[Virtuális hálózat (VNet) szolgáltatásvégpontjai](../virtual-network/virtual-network-service-endpoints-overview.md) bővítheti a virtuális hálózatának privát címterét és identitását az a virtuális hálózat az Azure-szolgáltatásokra egy közvetlen kapcsolaton keresztül. A végpontok segítségével biztosíthatja, hogy kritikus fontosságú Azure-szolgáltatási erőforrásai csak a virtuális hálózatain legyenek elérhetőek. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.

Tűzfalak, amellyel korlátozható az Event Hubs-névtér való hozzáférés meghatározott IP-címek (vagy IP-címtartományok) engedélyezése

Ez az oktatóanyag bemutatja, hogyan integrálható a virtuális hálózatokon a Szolgáltatásvégpontok, és állítsa be a tűzfalak (IP-szűrés) a meglévő Azure Event Hubs-névtér, a portál használatával.

Ebből az oktatóanyagból megtudhatja, hogyan lehet:
> [!div class="checklist"]
> * Hogyan lehet virtuális hálózatokon a Szolgáltatásvégpontok integrálása az Event Hubs-névtér.
> * Hogyan állíthatja be az Event Hubs-névtér-tűzfal (IP-szűrés).

>[!WARNING]
> Virtuális hálózatok integráció megvalósítása megakadályozhatja az egyéb Azure-szolgáltatásokhoz az Event hubs szolgáltatással való interakcióhoz.
>
> Első fél Integrációk nem támogatottak, ha engedélyezve vannak a virtuális hálózatok.
> Nem működnek a Virtual Network – gyakori Azure helyzetek
> * Az Azure Diagnostics és naplózás
> * Azure Stream Analytics
> * Event Grid-integráció
> * Web Apps és Functions van szükség a virtuális hálózaton.
> * IoT Hub Routes
> * IoT-Device Explorer


> [!IMPORTANT]
> A virtuális hálózatok támogatottak **standard** és **dedikált** az Event hubs szinten. Az alapszintű díjcsomagban nem támogatott.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiók] [-] megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Microsoft ezért használja egy meglévő Event Hubs-névtér, győződjön meg arról, hogy elérhető Event Hubs-névtér. Ha nem, olvassa el a [ebben az oktatóanyagban](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Először nyissa meg az [Azure Portalt][Azure portal], és Azure-előfizetésének használatával jelentkezzen be.

## <a name="select-event-hubs-namespace"></a>Az Event Hubs-névtér kijelölése

Jelen oktatóanyag esetében azt létrehozott Event Hubs-névtér és nyitja meg, amely.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Navigáljon a tűzfalak és virtuális hálózatok élmény

A portál bal oldali ablaktáblán a navigációs menü segítségével válasszon a **"Tűzfalak és virtuális hálózatok"** lehetőséget.

  ![Navigáljon a menü](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Ezen a lapon első meglátogatásakor az **minden hálózatból elérhető** választógomb ki kell választani. Ez azt jelenti, hogy az Event Hubs-névtér lehetővé teszi, hogy az összes bejövő kapcsolat.

## <a name="add-virtual-network-service-endpoint"></a>Adja hozzá a virtuális hálózati szolgáltatásvégpont

A hozzáférés korlátozásához, a virtuális hálózati szolgáltatásvégpontot az Event Hubs-névtér integrálnia kell.

1. Kattintson a **kiválasztott hálózatok** választógombot ahhoz, hogy a többi a menüpontok tartalmazó lap az oldal felső részén.
  ![kijelölt hálózatok](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Az oldal a virtuális hálózat területen válassza a ***+ meglévő virtuális hálózat hozzáadása***. Ez a panel, amely lehetővé teszi, hogy egy már létrehozott virtuális hálózatot válassza ki a megnyitáskor.
  ![Meglévő virtuális hálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. A virtuális hálózatot válasszon a listából, és válassza ki az alhálózatot.
   ![alhálózat kiválasztása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Hogy a szolgáltatásvégpont engedélyezése előtt a virtuális hálózat hozzáadása a listához. Ha a szolgáltatásvégpont nincs engedélyezve, a portál felszólítja majd engedélyezheti azt.
  ![Válassza ki az alhálózatot, és engedélyezze a végpont](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Ha Ön nem sikerült engedélyezni a szolgáltatásvégpont, figyelmen kívül hagyhatja a hiányzó virtuális hálózati szolgáltatásvégpontot az ARM-sablon használatával. Ez a funkció a portálon nem érhető el.

5. Miután engedélyezte a szolgáltatásvégpont a kiválasztott alhálózatnak, folytassa az engedélyezett virtuális hálózatok listájához hozzáadni.
  ![Miután engedélyezte a végpont alhálózat hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. A folytatáshoz nyomja le a **mentése** gombra a virtuális hálózati konfiguráció mentéséhez kattintson a szolgáltatás a felső szalagon. Várjon néhány percet, amíg a jóváhagyást a portál értesítési jelenik meg.

## <a name="add-firewall-for-specified-ip"></a>Megadott IP-tűzfal hozzáadása

Azt is férjenek hozzá az Event Hubs-névtér egy korlátozott IP-címtartomány vagy egy adott IP-cím tűzfalszabályok használatával.

1. Kattintson a **kiválasztott hálózatok** választógombot ahhoz, hogy a többi a menüpontok tartalmazó lap az oldal felső részén.
  ![kijelölt hálózatok](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. Az a **tűzfal** terület a ***címtartomány*** rács, előfordulhat, hogy adjon hozzá egy vagy több adott IP-cím vagy IP-címek tartományát.
  ![ip-címek hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Miután hozzáadta a több IP-cím (vagy IP-címek tartományát), kattintson a **mentése** annak érdekében, hogy a konfigurációs szolgáltatás oldalán menti a felső szalagon. Várjon néhány percet, amíg a jóváhagyást a portál értesítési jelenik meg.
  ![ip-címek hozzáadása, és kattintson a Mentés gombra](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>A tűzfalszabályok az aktuális IP-cím hozzáadása

1. Azt is megteheti az aktuális IP-címhez gyorsan ellenőrzésével a ***adja hozzá ügyfél IP-címét (az aktuális IP-cím)*** jelölőnégyzet feladatállapotában a ***címtartomány*** rács.
  ![jelenlegi IP-cím hozzáadása](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Miután hozzáadta a tűzfalszabályok az aktuális IP-címhez, nyomja le az **mentése** annak érdekében, hogy a konfigurációs szolgáltatás oldalán menti a felső szalagon. Várjon néhány percet, amíg a jóváhagyást a portál értesítési jelenik meg.
  ![Adja hozzá az aktuális IP-címet, és kattintson a Mentés gombra](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Összegzés

Ebben az oktatóanyagban, integrált virtuális hálózati végpontokat és -tűzfalszabályok egy meglévő Event Hubs-névtér. Hogyan tanulással, hogy:
> [!div class="checklist"]
> * Hogyan lehet virtuális hálózatokon a Szolgáltatásvégpontok integrálása az Event Hubs-névtér.
> * Hogyan állíthatja be az Event Hubs-névtér-tűzfal (IP-szűrés).


[Azure portal]: https://portal.azure.com/
