---
title: Egyéni hibalapok Azure Application Gateway létrehozása
description: Ez a cikk bemutatja, hogyan hozhat létre az Application Gateway vlastní chybové stránky.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/14/2019
ms.author: victorh
ms.openlocfilehash: abfe33ff679bef125d9bf5b78e1790a1a4c64863
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301604"
---
# <a name="create-application-gateway-custom-error-pages"></a>Egyéni hibalapok Application Gateway létrehozása

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

Például a saját karbantartás oldalon adhatja meg, ha a webalkalmazás nem érhető el. Vagy egy jogosulatlan hozzáférés oldalt hozhat létre, ha egy rosszindulatú kérelmet küld a webalkalmazást.

Egyéni hibalapok a következő két forgatókönyveket támogatja:

- **Karbantartás oldalon** -e hibalapot érkezik egy 502 Hibás átjáró lap helyett. Látható, amikor az Application Gateway rendelkezik a forgalom irányítása a háttérrendszer nélküli. Ha például azt, ütemezett karbantartás, illetve ha előre nem látott probléma hatással van a háttérrendszeri készlet hozzáférést.
- **Jogosulatlan hozzáférés lap** -e hibalapot érkezik egy 403-as jogosulatlan hozzáférési lap helyett. Látható, amikor az Application Gateway WAF rosszindulatú forgalom észleli és blokkolja ezt a műveletet.

Ha hiba történt a háttérkiszolgálók származik, majd átadva mentén, módosítás nélküli vissza a hívónak. Egyéni hibalap nem jelenik meg. Az Application gateway is egy egyéni hibalap megjelenítése, amikor a kérelem nem érhető el a háttérrendszert.

Egyéni hibalapok a globális szinten és a figyelő szintjén adható meg:

- **Globális szintű** -forgalom számára, hogy az application gateway szolgáltatásban üzembe helyezett összes webes alkalmazások a hibalap utal.
- **Figyelő szint** -forgalomhoz, a figyelő a hibalap vonatkozik.
- **Mindkét** – az egyéni hibalap, a figyelő szintjén definiált felülbírálások globális szinten az egy csoport.

Hozzon létre egy egyéni Hibaoldal, kell rendelkeznie:

- a válasz HTTP-állapotkódot.
- a hibalaphoz tartozó megfelelő hely. 
- a nyilvánosan elérhető Azure storage blob helyéhez.
- *.htm vagy *.html kiterjesztés típusú. 

A hibalap méretének 1 MB-nál kisebbnek kell lennie. Ha a hiba lapon társított képek, azok nyilvánosan hozzáférhető abszolút URL-címek vagy az egyéni hibalap a base64-kódolású lemezkép beágyazott kell lennie. Relatív hivatkozásokat képekkel blob ugyanazon a helyen jelenleg nem támogatott. 

Miután megadott egy hibalap, az application gateway a tárolási blob helyről, letölti, és menti a helyi application gateway gyorsítótár. Ezután a hibalap biztosítja az application gateway-ről. Egy meglévő egyéni hibalap módosításához az application gateway-konfigurációt egy másik blob pontjára kell mutatnia. Az application gateway nem rendszeres időközönként ellenőrzi a blob helyére beolvasni az új verziók.

## <a name="portal-configuration"></a>Portálbeállítások

1. Application gatewayhez a portálon keresse meg és válassza ki az application gateway.

    ![rendelkezésre állási csoport – áttekintés](media/custom-error/ag-overview.png)
2. Kattintson a **figyelői** , és keresse meg, ahol egy hibalap adja meg szeretné egy adott figyelőt.

    ![Application Gateway figyelők](media/custom-error/ag-listener.png)
3. A WAF 403-as hibaüzenet egyéni hibalap vagy egy 502 karbantartás oldalon konfigurálja a figyelő szintjén.

    > [!NOTE]
    > Globális szintű vlastní chybové stránky létrehozása az Azure Portal jelenleg nem támogatott.

4. Egy adott hiba állapotkód egy nyilvánosan elérhető-e a blob URL-címet, és kattintson a **mentése**. Az Application Gateway mostantól az egyéni hibalap van konfigurálva.

   ![Application Gateway-hibakódok](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-konfigurálás

Azure PowerShell segítségével konfigurálhatja egy egyéni Hibaoldal. Ha például egy globális vlastní chybovou stránku:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Vagy egy figyelő hibalap:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

További információkért lásd: [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) és [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>További lépések

Application Gateway-diagnosztika kapcsolatos információkért lásd: [háttérrendszer állapota, diagnosztikai naplók és mérőszámok az Application Gateway](application-gateway-diagnostics.md).