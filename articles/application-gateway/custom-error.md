---
title: Azure-Application Gateway egyéni hibák lapjainak létrehozása
description: Ez a cikk bemutatja, hogyan hozhat létre Application Gateway egyéni hibaüzeneteket. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: d78f7aa2a02f14dc9b875895e3057bd4dee29b74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808088"
---
# <a name="create-application-gateway-custom-error-pages"></a>Egyéni hibaüzenetek Application Gateway létrehozása

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

Megadhatja például saját karbantartási oldalát, ha a webalkalmazás nem érhető el. A jogosulatlan hozzáférési lapokat is létrehozhat, ha rosszindulatú kérést küld egy webalkalmazásnak.

Az egyéni hibák a következő két forgatókönyv esetén támogatottak:

- **Karbantartási lap** – ezt az egyéni hibaüzenetet a rendszer a 502 hibás átjáró oldal helyett elküldi. Akkor jelenik meg, ha Application Gateway nem rendelkezik a forgalom átirányításához szükséges háttérrel. Például, ha ütemezett karbantartást végez, vagy ha egy nem várt probléma a háttérbeli készlethez való hozzáférésre van hatással.
- **Jogosulatlan hozzáférés lap** – ezt az egyéni hibaüzenetet a rendszer a 403 jogosulatlan hozzáférési oldal helyett elküldi. Akkor jelenik meg, ha a Application Gateway WAF kártékony forgalmat észlel, és blokkolja azt.

Ha egy hiba a háttér-kiszolgálókról származik, akkor azt a rendszer visszaadja a hívónak visszaadott vissza. Nem jelenik meg egyéni hiba lap. Az Application Gateway egy egyéni hibaüzenetet jelenít meg, ha egy kérelem nem tudja elérni a háttérrendszer elérését.

Az egyéni hibaüzenetek a globális szinten és a figyelő szintjén is meghatározhatók:

- **Globális szint** – a hiba lap az adott Application Gateway-ben üzembe helyezett webalkalmazások forgalmára vonatkozik.
- **Figyelő szintje** – a hiba oldal a figyelőn fogadott forgalomra vonatkozik.
- **Mindkettő** – a figyelő szintjén definiált egyéni hibaüzenet felülbírálja az egy globális szinten beállított készletet.

Egyéni hiba lap létrehozásához a következőket kell tennie:

- egy HTTP-válasz állapotkódot.
- a hiba oldalának megfelelő hely. 
- nyilvánosan elérhető Azure Storage-blob a helyhez.
- egy *. htm vagy *. html kiterjesztés típusa. 

A hiba lap méretének 1 MB-nál kisebbnek kell lennie. Ha a hiba oldalon található képek vannak csatolva, akkor az egyéni hiba lapon a nyilvánosan elérhető abszolút URL-címek vagy Base64-kódolású lemezképek jelennek meg. Az azonos blob-helyen található rendszerképekkel való relatív hivatkozások jelenleg nem támogatottak. 

Miután megadta a hibaüzenetet, az Application Gateway letölti a tárolási blob helyéről, és a helyi Application Gateway-gyorsítótárba menti azt. Ezt követően a rendszer közvetlenül az Application gatewayből kézbesíti a hiba lapot. Egy meglévő egyéni hiba lap módosításához az Application Gateway konfigurációjában egy másik blob-helyre kell mutatnia. Az Application Gateway nem ellenőrzi rendszeresen a blob helyét az új verziók beolvasásához.

## <a name="portal-configuration"></a>Portál konfigurálása

1. Navigáljon Application Gateway a portálon, és válasszon egy Application Gateway-t.

    ![AG – áttekintés](media/custom-error/ag-overview.png)
2. Kattintson a **figyelők** elemre, és navigáljon egy adott figyelőhöz, ahol egy hibaüzenetet szeretne megadni.

    ![Application Gateway figyelők](media/custom-error/ag-listener.png)
3. Egyéni hibaüzenetet adhat meg egy 403 WAF-hiba vagy egy 502-karbantartási lap számára a figyelő szintjén.

    > [!NOTE]
    > A globális szintű egyéni hibák lapjainak létrehozása a Azure Portal jelenleg nem támogatott.

4. Adja meg az adott hibakódhoz tartozó nyilvánosan elérhető blob URL-címét, majd kattintson a **Mentés**gombra. A Application Gateway mostantól az egyéni hiba lapra van konfigurálva.

   ![Application Gateway hibakódok](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-konfigurálás

A Azure PowerShell használatával egyéni hibaüzeneteket adhat meg. Például egy globális egyéni hiba lap:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Vagy egy figyelő szintű hiba lapja:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

További információ: [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) és [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>További lépések

További információ a Application Gateway diagnosztika használatáról: [a háttér állapota, a diagnosztikai naplók és a Application Gateway metrikái](application-gateway-diagnostics.md).