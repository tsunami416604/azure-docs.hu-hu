---
title: Felkészülés a bejövő IP-címváltozásra
description: Ha a bejövő IP-cím meg fog változni, olvassa el, hogy mi a teendő, hogy az alkalmazás a módosítás után is működjön.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672417"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Felkészülés a bejövő IP-címváltozásra

Ha értesítést kapott arról, hogy az Azure App Service-alkalmazás bejövő IP-címe változik, kövesse a jelen cikkben található utasításokat.

## <a name="determine-if-you-have-to-do-anything"></a>Határozza meg, hogy tennie kell-e valamit

* 1. lehetőség: Ha az App Service-alkalmazás nem rendelkezik egyéni tartománnyal, nincs szükség műveletre.

* 2. lehetőség: Ha csak egy CNAME rekord (EGY URI-ra mutató DNS-rekord) van konfigurálva a domain regisztrációs portálon (külső DNS-szolgáltató vagy Az Azure DNS), nincs szükség műveletre.

* 3. lehetőség: Ha egy A rekord (közvetlenül az IP-címre mutató DNS-rekord) konfigurálva van a domain regisztrációs portálon (külső DNS-szolgáltató vagy Az Azure DNS), cserélje le a meglévő IP-címet az újra. Az új IP-címet a következő szakaszban található utasításokat követve találhatja meg.

* 4. lehetőség: Ha az alkalmazás egy terheléselosztó, IP-szűrő vagy bármely más OLYAN IP-mechanizmus mögött van, amelyhez az alkalmazás IP-címét igényli, cserélje le a meglévő IP-címet az újra. Az új IP-címet a következő szakaszban található utasításokat követve találhatja meg.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Az új bejövő IP-cím megkeresése az Azure Portalon

Az alkalmazásnak megadott új bejövő IP-cím a virtuális **IP-cím** mezőben található a portálon. Mind az új IP-cím, mind a régi most csatlakozik az alkalmazáshoz, és később a régi megszakad.

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza az **App Services**lehetőséget.

3.  Válassza ki az App Service-alkalmazást a listából.

1.  Ha az alkalmazás függvényalkalmazás, olvassa el a [Függvényalkalmazás bejövő IP-címét.](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs sáv **Tulajdonságok gombjára,** és keresse meg a **Virtuális IP-cím**feliratú szakaszt.

5. Másolja az IP-címet, és konfigurálja újra a tartományrekordot vagy az IP-mechanizmust.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan készülhet fel az Azure által kezdeményezett IP-címmódosításra. Az Azure App Service IP-címeiről további információt a [Bejövő és kimenő IP-címek az Azure App Service-ben című témakörben talál.](overview-inbound-outbound-ips.md)
