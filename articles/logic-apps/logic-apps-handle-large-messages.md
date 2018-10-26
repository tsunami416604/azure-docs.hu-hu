---
title: Nagyméretű üzenetek – Azure Logic Apps-alkalmazások kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelje az Azure Logic Apps darabolás nagy méretű üzenetek mérete
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 5aa5ea2a39a0fb9f969e965fed14063522197cda
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085798"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Az Azure Logic Apps darabolás nagyméretű üzenetek kezelése

Üzenetek kezelése, korlátozza a Logic Apps és az üzenet tartalma a maximális méretet. Ez a korlátozás segít a terhelés csökkentése érdekében tárolására és feldolgozására a nagy méretű üzenetek által létrehozott. Ennél nagyobb üzenetek kezeléséhez, a Logic Apps is *adattömbök* egy nagy méretű üzenetek kisebb üzenetbe. Ily módon továbbra is átadhatja a Logic Apps használatával meghatározott feltételek mellett nagy méretű fájlok. Ha más szolgáltatások összekötők vagy HTTP használatával kommunikál, a Logic Apps felhasználhatja a nagy méretű üzenetek, de *csak* az adattömböket. Ez az állapot azt jelenti, hogy a összekötők is támogatnia kell a darabolás, vagy az alapul szolgáló Logic Apps és a szolgáltatások közötti HTTP üzenet exchange darabolás kell használnia.

Ez a cikk bemutatja, hogyan állíthatja darabolás üzeneteket, amelyek nagyobbak, mint a korlát kezelési műveletek esetében. Logikaialkalmazás-triggerek nem támogatják a darabolás miatt a nagyobb, több üzenetváltásokban a terhelése. 

## <a name="what-makes-messages-large"></a>"Nagy" teszi üzenetek?

Üzenetek "nagy" ezeket az üzeneteket kezelő szolgáltatás alapján. A nagy méretű üzenetek pontos méretkorlátja eltér a Logic Apps és összekötők között. A Logic Apps és összekötők is közvetlenül nem lehet felhasználni nagy üzenetekről, amelyeken a darabolásos kell lennie. A Logic Apps üzenet maximális mérete, lásd: [Logic Apps-korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).
Egyes összekötők üzenet méretkorlátot, lásd: a [összekötő adott technikai részletek](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>A Logic Apps kezelése darabolt üzenet

A Logic Apps közvetlenül nem használható a darabolásos üzeneteket, amelyek nagyobbak, mint az üzenet mérete meghaladja a kimenetek. Csak olyan műveleteket, amelyek támogatják a darabolás hozzáférhetnek az ezeket a kimeneteket üzenet tartalma. Tehát meg kell felelnie egy műveletet, amely kezeli a nagy méretű üzenetek *vagy* ezeknek a feltételeknek:

* Natív módon támogatja a darabolás, ha a művelet egy összekötő tartozik. 
* Rendelkezik darabolás támogatása engedélyezve van az adott művelet futásidejű konfigurációja. 

Ellenkező esetben futásidejű hiba kap, amikor megpróbálja elérni a tartalom nagy kimeneti. Engedélyezheti a darabolás [állítsa be a támogatási darabolás](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Az összekötők kezelése darabolt üzenet

A Logic Apps szolgáltatással kommunikáló szolgáltatások saját üzenetek méretkorlátjának rendelkezhet. Ezek a korlátok gyakran kisebbek, mint a Logic Apps-korlátot. Például feltéve, hogy az összekötő támogatja az darabolás, összekötő érdemes megfontolni nagyobb, mint 30 MB-os üzenet Logic Apps azonban nem. Ahhoz, hogy megfeleljenek az összekötő korlátot, a Logic Apps bontja üzeneteié 30 MB-nál nagyobb, kisebb adattömbökben.

Darabolás támogató összekötők esetében az alapul szolgáló tömbösítési protokoll nem látható, a végfelhasználók számára. Azonban nem minden összekötők támogatja darabolás, így ezek az összekötők készítése a futásidejű hibák, ha a bejövő üzenetek meghaladja a méretbeli korlátokat az összekötők.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Állítsa be a HTTP protokollon keresztül darabolás

Az általános HTTP-forgatókönyvek esetén is nagy tartalomletöltésekhez megosztani, és feltölti HTTP-n keresztül, hogy a logikai alkalmazás és a egy végpontot is exchange nagy méretű üzenetek. Azonban bontják részekre a Logic Apps vár módon üzeneteket. 

Ha a végpont letöltések vagy feltöltések darabolás engedélyezve van, a HTTP-műveleteket a logikai alkalmazás automatikusan darabolja nagy méretű üzenetek. Ellenkező esetben kell állítania a végponton támogatási darabolás. Ha nem a saját vagy a végpont vagy az összekötő, nem feltétlenül darabolás beállítása lehetőség.

Emellett ha egy HTTP-művelet már nem engedélyezi az darabolás, is be kell állítania a művelet darabolás `runTimeConfiguration` tulajdonság. Ez a tulajdonság a műveletben, vagy közvetlenül a szerkesztőben kód nézet később ismertetett, vagy a Logic Apps Designerben Itt állíthatja be:

1. A HTTP-művelet jobb felső sarokban, válassza a három pont gombra (**...** ), majd **beállítások**.

   ![Nyissa meg a műveletet, a beállítások menü](./media/logic-apps-handle-large-messages/http-settings.png)

2. A **tartalom átvitele**állítsa be **darabolás engedélyezése** való **a**.

   ![Darabolás bekapcsolása](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Letöltések vagy feltöltések darabolás telepítésének folytatásához, folytassa a következő szakaszok.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Az adattömbök tartalom letöltése

Sok végpont automatikusan adattömbök egy HTTP GET kérés letöltött nagy méretű üzenetek küldése. Darabolt üzenetek letöltését a végpont HTTP protokollon keresztül, a végpont támogatnia kell a részleges tartalom kérelmeket, vagy *letöltések darabolásos*. Amikor a logikai alkalmazás egy HTTP GET kérést küld a tartalom letöltése a végpont, és a végpont válaszol "206" állapotkód, a válasz tartalmazza a darabolásos tartalom. A Logic Apps nem tudja vezérelni támogatja-e a végpont a részleges kéréseket. Ha a logikai alkalmazás lekérdezi az első "206" választ, az a logikai alkalmazás automatikusan küld minden tartalom letöltése a kéréseket.

Ellenőrizze, hogy a végpont is támogatja a részleges tartalom, HEAD-kérések küldése. Ezt a kérelmet segít annak meghatározásában, hogy tartalmaz-e a válasz a `Accept-Ranges` fejléc. Így ha a végpont támogatja a darabolásos letöltések, de nem küld a darabolásos tartalmat, akkor *javaslat* ezt a beállítást állítsa a `Range` a HTTP GET kérés fejlécében. 

Ezeket a lépéseket ismertetik a részletes eljárást használ a Logic Apps darabolt történő letöltésük egy végpontot a logikai alkalmazáshoz:

1. A logikai alkalmazás egy HTTP GET kérést küld a végpont.

   A kérelem fejlécében szükség esetén belefoglalhatja a `Range` tartalom adattömbök kérelmezési bájttartomány a mezőbe.

2. A végpont fűzi hozzá a "206" állapotkódot és a egy HTTP-üzenet törzse.

    Az adattömbök lévő tartalommal kapcsolatos adatok megjelennek-e a válaszban `Content-Range` határozza meg a kezdő és befejező az adatköteget, valamint a teljes tartalom teljes mérete a fejléc, beleértve a Logic Apps megkönnyíti darabolás előtt.

3. A logikai alkalmazás automatikusan elküldi a HTTP GET kérések követési.

    A logikai alkalmazás küld követő GET kérelmek mindaddig, amíg a teljes tartalom lekérését.

Például ez a művelet a definíció látható-e egy HTTP GET kérés, amely beállítja a `Range` fejléc. A fejléc *javasol* , hogy a végpontot kell visszaadnia darabolásos tartalom:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

A GET-kérés állítja be a "Range" fejléc "bájt = 0-1023", amely az, hogy a tartomány bájt. Ha a végpont kérelmeket támogatja a részleges tartalom, a végpont válaszként tartalom adattömb a kért tartomány. A végpont alapján, a "Tartomány" fejléc mező formátummal eltérőek lehetnek.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Az adattömbök tartalom feltöltése

Egy HTTP-művelet darabolt tartalmat feltölteni, a művelet engedélyeznie kell a művelet tömbösítési támogatását `runtimeConfiguration` tulajdonság. Ez a beállítás lehetővé teszi, hogy a műveletet az elindításához a csonkolási protokoll. A logikai alkalmazás majd is kezdeti POST és PUT üzenet küldése a céloldali végpont. Után a végpont javasolt adattömbméretet válaszol, a logikai alkalmazás a következő a tartalom adattömbök tartalmazó HTTP-javítás kérések küldésével.

Ezeket a lépéseket ismertetik a részletes folyamat, Logic Apps számára a logikai alkalmazás egy végpontnak darabolt tartalmak feltöltéséhez használ:

1. A logikai alkalmazás küld egy kezdeti HTTP POST és PUT kérés az üzenettörzse üres. A kérelem fejlécét, ezt a tartalmat, amely a logikai alkalmazás szeretne feltölteni tömbökben kapcsolatos információkat tartalmazza:

   | A Logic Apps fejlécmezőt kérése | Érték | Típus | Leírás |
   |---------------------------------|-------|------|-------------|
   | **x-ms-átviteli-mód** | darabolásos | Sztring | Azt jelzi, hogy a tartalom tömbökben van feltöltve. |
   | **x-ms-content-length** | <*tartalom-hossza*> | Egész szám | A teljes tartalom mérete (bájt) darabolás előtt |
   ||||

2. A végpont válaszol "200" sikeres állapotkód és a nem kötelező információkkal:

   | Végpont válasz fejléce mező | Típus | Szükséges | Leírás |
   |--------------------------------|------|----------|-------------|
   | **x-ms-adattömbméret** | Egész szám | Nem | A javasolt adatrészlet mérete (bájt) |
   | **Hely** | Sztring | Nem | Az URL-címét, hova küldhetők a HTTP-javítás üzenetek |
   ||||

3. A logikai alkalmazás hoz létre, és ezt az információt az egyes küldi az üzeneteket követő HTTP PATCH:

   * Tartalom adattömb alapján **x-ms-adattömbméret** vagy néhány belső számított mérete, amíg az összes tartalom összesítése **x-ms-content-length** egymást követően dolgozzák fel a rendszer

   * Ezek az egyes javítás üzenetben küldött tartalom adattömb fejléc részleteit:

     | A Logic Apps fejlécmezőt kérése | Érték | Típus | Leírás |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*Címtartomány*> | Sztring | A bájttartomány számára a tartalom darabban, beleértve a kezdő érték, a befejezési értéket, és a teljes tartalom méretét, például: "bájtok száma 0-1023/10100 =" |
     | **A Content-Type** | <*a Content-type*> | Sztring | A darabolt tartalmának típusa |
     | **Content-Length** | <*tartalom-hossza*> | Sztring | A hosszát mérete (bájt) a aktuální Blok dat |
     |||||

4. Után minden PATCH-kérés a végpont megerősíti, hogy az egyes adattömbök fogadását a "200" állapotkód válaszolva.

Például ez a művelet a definíció látható, a darabolásos tartalmat töltenek fel a végpont HTTP POST-kérelmet. A művelet `runTimeConfiguration` tulajdonság, a `contentTransfer` tulajdonság beállítása `transferMode` való `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```