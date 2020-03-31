---
title: Egyéni Azure Application Gateway létrehozása egyéni hibalapok
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egyéni alkalmazásátjáró-lapokat. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129862"
---
# <a name="create-application-gateway-custom-error-pages"></a>Egyéni alkalmazásátjáró létrehozása egyéni hibalapok

Az Application Gatewayjel testreszabhatók a hibaoldalak. Az egyéni hibaoldalakon feltüntetheti saját védjegyeit, és egyéni elrendezést használhat.

Például megadhatja a saját karbantartási oldalt, ha a webalkalmazás nem érhető el. Ha rosszindulatú kérést küld egy webalkalmazásnak, létrehozhat egy jogosulatlan hozzáférési lapot is.

Az egyéni hibalapok a következő két esetben támogatottak:

- **Karbantartási lap** – Ez az egyéni hibalap az 502-es rossz átjárólap helyett kerül elküldésre. Akkor jelenik meg, ha az Application Gateway nem rendelkezik háttérrendszera forgalom irányításához. Például ha van ütemezett karbantartás, vagy ha egy előre nem látható probléma hatások háttérkészlet-hozzáférés.
- **Jogosulatlan hozzáférési lap** – Ezt az egyéni hibalapot a rendszer 403-as jogosulatlan hozzáférési lap helyett küldi el. Akkor jelenik meg, ha az Application Gateway WAF észleli a rosszindulatú forgalmat, és blokkolja azt.

Ha egy hiba származik a háttérkiszolgálók, majd átadják a módosítatlan vissza a hívó. Nem jelenik meg egyéni hibaoldal. Az alkalmazásátjáró megjeleníthet egy egyéni hibalapot, ha egy kérelem nem éri el a háttérprogramot.

Az egyéni hibalapok globális és a figyelő szinten is definiálhatók:

- **Globális szint** – a hibalap az adott alkalmazásátjárón telepített összes webalkalmazás forgalmára vonatkozik.
- **Figyelő szintje** – a hibaoldal az adott figyelőn fogadott forgalomra vonatkozik.
- **Mindkettő** – a figyelő szinten definiált egyéni hibalap felülbírálja a globális szinten beállított at.

Egyéni hibalap létrehozásához a következőkre van szüksége:

- egy HTTP-válasz állapotkódot.
- a hibaoldal megfelelő helye. 
- egy nyilvánosan elérhető Azure storage blob a helyhez.
- *.htm vagy *.html kiterjesztéstípus. 

A hibalap méretének 1 MB-nál kisebbnek kell lennie. Ha vannak képek a hibaoldalon, akkor nyilvánosan elérhető abszolút URL-eknek vagy base64 kódolású képszövegközinek kell lenniük az egyéni hibalapon. Az azonos blobhelyen lévő képekkel való relatív kapcsolatok jelenleg nem támogatottak. 

Miután megadta a hibalapot, az alkalmazásátjáró letölti azt a tárolóblob helyéről, és menti azt a helyi alkalmazásátjáró gyorsítótárába. Ezután a hibalap közvetlenül az alkalmazásátjáróból jelenik meg. Meglévő egyéni hibalap módosításához az alkalmazásátjáró konfigurációjában egy másik blobhelyre kell mutatnia. Az alkalmazásátjáró nem ellenőrzi rendszeresen a blob helyét az új verziók lekéréséhez.

## <a name="portal-configuration"></a>Portál konfigurációja

1. Nyissa meg az Alkalmazásátjárót a portálon, és válasszon egy alkalmazásátjárót.

    ![ag-áttekintés](media/custom-error/ag-overview.png)
2. Kattintson **a Figyelők** elemre, és keresse meg azt a figyelőt, ahol hibalapot szeretne megadni.

    ![Alkalmazásátjáró-figyelők](media/custom-error/ag-listener.png)
3. Konfiguráljon egyéni hibalapot 403-as WAF-hibához vagy egy 502-es karbantartási laphoz a figyelő szintjén.

    > [!NOTE]
    > Globális szintű egyéni hibalapok létrehozása az Azure Portalon jelenleg nem támogatott.

4. Adjon meg egy nyilvánosan elérhető blob URL-címet egy adott hibaállapot-kódhoz, és kattintson a **Mentés gombra.** Az Alkalmazásátjáró most már az egyéni hibalappal van konfigurálva.

   ![Alkalmazásátjáró hibakódjai](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-konfigurálás

Az Azure PowerShell segítségével egyéni hibalap konfigurálásához. Például egy globális egyéni hibaoldal:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Vagy egy figyelő szintű hibaoldal:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

További információ: [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) és [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>További lépések

Az Application Gateway diagnosztikájáról további információt a [Háttérrendszer állapotáról, a diagnosztikai naplókról és az Application Gateway metrikáiról talál.](application-gateway-diagnostics.md)