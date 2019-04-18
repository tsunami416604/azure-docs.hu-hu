---
title: Egyéni mintavétel - létrehozása az Azure Application Gateway – Azure Portal |} A Microsoft Docs
description: Egyéni mintavétel létrehozása az Application Gateway a portál használatával
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: 90d576fd00a39f7e871cbe0922ce131dfbe38ff0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58862165"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Egyéni mintavétel létrehozása az Application Gateway a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Klasszikus Azure PowerShell](application-gateway-create-probe-classic-ps.md)

Ez a cikk az Azure Portalon keresztül meglévő application gateway vegyen fel egy egyéni mintát. Az alkalmazásokat, egy adott állapotellenőrzési oldalt, vagy az alapértelmezett webalkalmazást a sikeres válasz nem biztosító alkalmazások egyéni minták hasznosak lehetnek.

## <a name="before-you-begin"></a>Előkészületek

Ha Ön még nem rendelkezik az application gateway, keresse fel [hozzon létre egy Application Gateway](application-gateway-create-gateway-portal.md) szeretne dolgozni egy application gateway létrehozásához.

## <a name="createprobe"></a>A mintavétel létrehozása

Mintavételezők egy kétlépéses folyamat, a portálon keresztül lehet konfigurálni. Az első lépés, hogy a projekt létrehozásához. A második lépésben hozzáadja az application Gateway a háttérbeli http-beállítások a mintavétel.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, akkor regisztráljon egy [ingyenes egy hónapos próbaidőszak](https://azure.microsoft.com/free)

1. A az Azure portal Kedvencek panelen kattintson az összes erőforrást. Kattintson az application gateway az összes erőforrás panelen. Ha a kiválasztott előfizetésben már több erőforrás szerepel, adhatja meg partners.contoso.net a Szűrés név alapján... mezőbe.

1. Kattintson a **mintavételek** , és kattintson a **Hozzáadás** mintavétel hozzáadása gombra.

   ![Adja hozzá a mintavétel panel töltötte ki adatokkal][1]

1. Az a **állapotadat-mintavétel hozzáadása** panelen adja meg a mintavétel szükséges adatokat, és kattintson a teljes **OK**.

   |**Beállítás** | **Érték** | **Részletek**|
   |---|---|---|
   |**Name (Név)**|customProbe|Ez az érték egy rövid nevet a mintavétel, amely elérhető a portálon.|
   |**Protocol (Protokoll)**|HTTP- vagy HTTPS | A protokoll, amely használja az állapotmintához.|
   |**Gazdagép**|i.e contoso.com|Ez az érték a gazdagép nevét, hogy a mintavétel használt. Alkalmazható csak akkor, ha a többhelyes konfigurálva van az Application Gatewayen, ellenkező esetben használja a "127.0.0.1". Ez az érték eltér a virtuális gép állomásnevét.|
   |**Elérési út**|/ vagy egy másik elérési utat|Egyéni mintavétel teljes URL-cím része. Érvényes elérési utat kezdődik (/). Az alapértelmezett elérési út http:\//contoso.com használja (/) |
   |**Időtartam (másodperc)**|30|A mintavétel gyakoriságát fut állapotának ellenőrzéséhez. Nem javasoljuk, hogy állítsa be az alacsonyabb, mint 30 másodperc.|
   |**Időkorlát (másodperc)**|30|Mennyi ideig időkorlátját vár a mintavétel. Az időkorlát kell lennie, elég nagy, hogy egy http-hívással hozható létre a háttérrendszer health-oldal az elérhető legyen.|
   |**Nem kifogástalan állapot küszöbértéke**|3|Nem megfelelő állapotú venni a sikertelen kísérletek száma. Ha az állapot-ellenőrzése sikertelen lesz a háttéralkalmazás határozza meg. nem megfelelő állapotú azonnal 0 azt jelenti, hogy küszöbértéket.|

   > [!IMPORTANT]
   > A gazdagép név nem ugyanaz, mint a kiszolgáló nevét. Ez az érték az alkalmazáskiszolgáló fut a virtuális gazdagép nevét. A mintavétel a http://(host name):(port from httpsetting)/urlpath attribútumra érkezik

## <a name="add-probe-to-the-gateway"></a>Az átjáró mintavétel hozzáadása

Most, hogy a mintavétel létrehozása után eljött az idő az átjáró történő hozzáadáshoz. A http-háttérbeállítások az application Gateway mintavételi beállítások vannak megadva.

1. Kattintson a **HTTP-beállítások** az application gatewayen viszi, megjelenik a konfigurációs panelen kattintson az aktuális háttérbeli http-beállítások a ablakban megjelenő.

   ![HTTPS-beállítások ablak][2]

1. Az a **appGatewayBackEndHttpSettings** beállítások panelen ellenőrizze az **egyéni mintavétel használata** jelölőnégyzetet, és válassza ki a létrehozott mintavétel a [a mintavétel létrehozása](#createprobe) a szakaszán **Egyéni mintavétel** legördülő...
   Amikor végzett, kattintson a **mentése** és a beállítások lesznek alkalmazva.

Az alapértelmezett mintavétel az alapértelmezett hozzáférés a webalkalmazáshoz. Most, hogy egyéni mintavétel létrehozása az application gateway használja az egyéni elérési út a háttérkiszolgálókhoz állapotának figyeléséhez definiálva. A megadott feltételek alapján, az application gateway ellenőrzi a mintavétel a megadott elérési út. Ha a gazdagép és a portszám hívást / elérési út nem ad vissza egy olyan HTTP 200-399 állapotválasz, a kiszolgáló a rotációból a nem kifogástalan állapot küszöbértéke elérése után. Tesztelés továbbra is a nem megfelelő állapotú példányon megállapítani, hogy mikor válik, kifogástalan állapotú újra. A példány vissza megfelelő kiszolgálókészlethez hozzáadott, forgalom kezdődik áramló rá, és a példányhoz tesztelés továbbra is a szokásos módon felhasználói megadott időközönként.

## <a name="next-steps"></a>További lépések

SSL-kiürítés konfigurálása az Azure Application Gateway szolgáltatással kapcsolatban lásd: [SSL kiürítési konfigurálása](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

