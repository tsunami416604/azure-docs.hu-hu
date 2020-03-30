---
title: Nagyméretű üzenetek kezelése darabolással
description: Megtudhatja, hogyan kezelhetők a nagy üzenetméretek az Azure Logic Apps alkalmazásokkal létrehozott automatizált feladatokban és munkafolyamatokban való darabolás használatával
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456551"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Nagy méretű üzenetek kezelése az Azure Logic Apps-ben

Az üzenetek kezelésekor a Logic Apps az üzenetek tartalmát maximális méretre korlátozza. Ez a korlát segít csökkenteni a nagyméretű üzenetek tárolásával és feldolgozásával létrehozott többletterhelést. Az ennél a korlátnál nagyobb üzenetek kezeléséhez a Logic Apps egy nagy üzenetet kisebb üzenetekre *is feltud darabolni.* Így továbbra is átviheti a nagy fájlokat a Logic Apps használatával meghatározott feltételek mellett. Ha összekötőkön vagy HTTP-n keresztül kommunikál más szolgáltatásokkal, a Logic Apps nagy méretű üzeneteket használhat fel, de *csak* adattömbökben. Ez a feltétel azt jelenti, hogy az összekötőknek is támogatniuk kell a darabolást, vagy az alapul szolgáló HTTP-üzenetváltást a Logic Apps és ezek a szolgáltatások között adattömböket kell használniuk.

Ez a cikk bemutatja, hogyan állíthatja be a korlátnál nagyobb üzeneteket kezelő műveletek darabolását. A Logic App eseményindítói nem támogatják a darabolást, mert a több üzenet cseréje megnövekedett terhelés. 

## <a name="what-makes-messages-large"></a>Miteszi az üzeneteket "nagy"?

Az üzenetek "nagyok", az üzeneteket kezelő szolgáltatás alapján. A nagy méretű üzenetek pontos méretkorlátja a Logic Apps és az összekötők között eltérő. A Logic Apps és az összekötők nem tudják közvetlenül felhasználni a nagy méretű üzeneteket, amelyeket meg kell darabolni. A Logic Apps üzenetméret-korlátról a [Logic Apps korlátai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md)című témakörben található.
Az egyes összekötők üzenetméret-korlátja az [összekötő speciális technikai részleteinek megtekintéséhez.](../connectors/apis-list.md)

### <a name="chunked-message-handling-for-logic-apps"></a>A Logic Apps tömbbedarabolt üzenetkezelése

A Logic Apps nem használhatja közvetlenül az üzenetméret-korlátnál nagyobb darabolt üzenetek kimeneteit. Csak a tömböket támogató műveletek férhetnek hozzá az üzenet tartalmához ezekben a kimenetekben. Így a nagyméretű üzeneteket kezelő műveletnek *meg* kell felelnie a következő feltételeknek:

* Natívan támogatja a darabolást, ha ez a művelet egy összekötőhöz tartozik. 
* A művelet futásidejű konfigurációjában engedélyezve van a darabolástámogatása. 

Ellenkező esetben futásidejű hibaüzenet jelenik meg, amikor nagy tartalomkimenetet próbál elérni. Az adattömbök engedélyezéséhez olvassa el a [Darabolás támogatásának beállítása.](#set-up-chunking)

### <a name="chunked-message-handling-for-connectors"></a>Darabolt üzenetek kezelése összekötőkhöz

A Logic Apps alkalmazásokkal kommunikáló szolgáltatások saját üzenetméret-korláttal rendelkezhetnek. Ezek a korlátok gyakran kisebbek, mint a Logic Apps korlát. Például, feltételezve, hogy egy összekötő támogatja a darabolás, egy összekötő fontolja meg egy 30 MB-os üzenet nagy, míg a Logic Apps nem. Az összekötő korlátjának való megfelelés érdekében a Logic Apps a 30 MB-nál nagyobb üzeneteket kisebb adattömbökre osztja fel.

Az összekötők, amelyek támogatják a darabolás, az alapul szolgáló darabolási protokoll láthatatlan a végfelhasználók számára. Azonban nem minden összekötőtámogatja az adattömbösítést, így ezek az összekötők futásidejű hibákat hoznak létre, amikor a bejövő üzenetek meghaladják az összekötők méretkorlátait.

> [!NOTE]
> A műveletek, amelyek a darabolás, nem adja át az `@triggerBody()?['Content']` eseményindító törzs, vagy kifejezések használata, például ezekben a műveletekben. Ehelyett szöveges vagy JSON-fájltartalom esetén megpróbálhatja használni az [ **Írás** műveletet,](../logic-apps/logic-apps-perform-data-operations.md#compose-action) vagy [létrehozhat egy változót](../logic-apps/logic-apps-create-variables-store-values.md) a tartalom kezelésére. Ha az eseményindító törzs más tartalomtípusokat is tartalmaz, például médiafájlokat, akkor további lépéseket kell végrehajtania a tartalom kezeléséhez.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Darabolás beállítása HTTP-n keresztül

Általános HTTP-forgatókönyvekben feloszthatja a nagy tartalomletöltéseket és -feltöltéseket HTTP-n keresztül, így a logikai alkalmazás és egy végpont nagy üzeneteket cserélhet. Azonban meg kell darabolni az üzeneteket, ahogy a Logic Apps elvárja. 

Ha egy végpont engedélyezte a letöltések vagy feltöltések adattömbök, a logikai alkalmazásban a HTTP-műveletek automatikusan nagy üzeneteket darabolnak. Ellenkező esetben be kell állítania a végponton a darabolási támogatást. Ha nem a végpont vagy az összekötő tulajdonában van, vagy nem vezérelheti, előfordulhat, hogy nincs lehetőség a darabolás beállítására.

Emellett ha egy HTTP-művelet még nem engedélyezi a darabolást, akkor a `runTimeConfiguration` művelet tulajdonságában is be kell állítania a darabolást. Ezt a tulajdonságot a műveleten belül állíthatja be, akár közvetlenül a kódnézet-szerkesztőben, ahogy azt később leírtuk, vagy a Logic Apps Designerben az itt leírtak szerint:

1. A HTTP-művelet jobb felső sarkában válassza a három pont gombot (**...**), majd a **Beállítások lehetőséget.**

   ![A műveletben nyissa meg a beállítások menüt](./media/logic-apps-handle-large-messages/http-settings.png)

2. A **Tartalomátvitel**csoportban állítsa be **az** **Adattömbök engedélyezése** lehetőséget.

   ![A darabolás bekapcsolása](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. A letöltések és feltöltések darabolásának további beállításához folytassa a következő szakaszokkal.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Tartalom letöltése adattömbökben

Számos végpont automatikusan nagy méretű üzeneteket küld adattömbökben, ha http GET-kérésen keresztül tölti le. Ha egy HTTP-n keresztüli végpontról szeretne adattömböt letölteni, a végpontnak támogatnia kell a részleges tartalomkérelmeket vagy *a darabolt letöltéseket.* Amikor a logikai alkalmazás http get-kérelmet küld egy végpontnak a tartalom letöltéséhez, és a végpont "206" állapotkóddal válaszol, a válasz darabtömböt tartalmaz. A Logic Apps nem szabályozhatja, hogy egy végpont támogatja-e a részleges kérelmeket. Azonban amikor a logikai alkalmazás megkapja az első "206" választ, a logikai alkalmazás automatikusan több kérelmet küld az összes tartalom letöltésére.

Annak ellenőrzéséhez, hogy egy végpont támogatja-e a részleges tartalmat, küldjön egy HEAD-kérelmet. Ez a kérés segít meghatározni, `Accept-Ranges` hogy a válasz tartalmazza-e a fejlécet. Így, ha a végpont támogatja a darabolt letöltéseket, de nem küld darabtömbbel, `Range` *javasolhatja* ezt a beállítást a HTTP GET-kérelem fejlécének beállításával. 

Ezek a lépések azt ismertetik, hogy a Logic Apps milyen részletes folyamatot használ a tömbbel, mint a logikai alkalmazásból a darabolt tartalom letöltéséhez:

1. A logikai alkalmazás http get-kérelmet küld a végpontnak.

   A kérelemfejléc tartalmazhat olyan `Range` mezőt, amely a tartalomadattömbök igényléséhez egy bájttartományt ír le.

2. A végpont a "206" állapotkóddal és egy HTTP-üzenet törzsével válaszol.

    Az adattömb tartalmának részletei megjelennek `Content-Range` a válasz fejlécében, beleértve azokat az információkat is, amelyek segítségével a Logic Apps meghatározhatja az adattömb kezdetét és végét, valamint a teljes tartalom teljes méretét az adattömbök összedarabolása előtt.

3. A logikai alkalmazás automatikusan elküldi a nyomon követési HTTP GET-kérelmeket.

    A logikai alkalmazás nyomon követési GET-kérelmeket küld, amíg a teljes tartalom lekérésre nem kerül.

Ez a műveletdefiníció például egy HTTP `Range` GET-kérelmet jelenít meg, amely beállítja a fejlécet. A fejléc *azt javasolja,* hogy a végpont nak darabolt tartalommal kell válaszolnia:

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

A GET kérés a "Tartomány" fejlécet "bytes=0-1023"-ra állítja, amely a bájtok tartománya. Ha a végpont támogatja a részleges tartalomra vonatkozó kérelmeket, a végpont a kért tartományból származó tartalomtömbbel válaszol. A végpont alapján a "Tartomány" fejlécmező pontos formátuma eltérő lehet.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Tartalom feltöltése adattömbökben

A HTTP-műveletből származó darabolt tartalom feltöltéséhez a műveletnek `runtimeConfiguration` engedélyeznie kell a művelet tulajdonságán keresztüli darabolási támogatást. Ez a beállítás lehetővé teszi a művelet elindítását a darabolási protokoll. A logikai alkalmazás ezután küldhet egy kezdeti POST vagy PUT üzenetet a célvégpontra. Miután a végpont válaszol egy javasolt adattömbmérettel, a logikai alkalmazás nyomon követi a tartalomadattömböket tartalmazó HTTP PATCH-kérelmek küldésével.

Az alábbi lépések azt ismertetik, hogy a Logic Apps milyen részletes folyamatot használ a tömbbel töltött tartalom nak a logikai alkalmazásból egy végpontra történő feltöltéséhez:

1. A logikai alkalmazás egy kezdeti HTTP POST vagy PUT kérelmet küld egy üres üzenettörzskel. A kérelem fejléce tartalmazza a logikai alkalmazás által adattömbökben feltölteni kívánt tartalommal kapcsolatos információkat:

   | Logic Apps kérelem fejlécmezője | Érték | Típus | Leírás |
   |---------------------------------|-------|------|-------------|
   | **x-ms-átviteli mód** | darabolt | Sztring | Azt jelzi, hogy a tartalom adattömbökben van feltöltve |
   | **x-ms-tartalom-hosszúságú** | <*tartalom hossza*> | Egész szám | A teljes tartalomméret bájtban darabolás előtt |
   ||||

2. A végpont a "200" sikerességi állapotkóddal válaszol, és ez a választható információ:

   | Végpont válaszfejléc-mezője | Típus | Kötelező | Leírás |
   |--------------------------------|------|----------|-------------|
   | **x-ms-darabméret** | Egész szám | Nem | A javasolt adattömbméret bájtban |
   | **Helyen** | Sztring | Igen | A HTTP PATCH-üzenetek küldésének URL-címe |
   ||||

3. A logikai alkalmazás nyomon követési HTTP PATCH-üzeneteket hoz létre és küld – mindegyik ezekkel az információkkal rendelkezik:

   * **X-ms-darabméreten** vagy valamilyen belsőleg számított méreten alapuló tartalomtömb, amíg az **összes x-ms-content-length** tartalmat fel nem tölti fel egymás után.

   * Ezek a fejléc részletek az egyes PATCH-üzenetekben küldött tartalomtömbről:

     | Logic Apps kérelem fejlécmezője | Érték | Típus | Leírás |
     |---------------------------------|-------|------|-------------|
     | **Tartalomtartomány** | <*Tartomány*> | Sztring | Az aktuális tartalomadattömb bájttartománya, beleértve a kezdő értéket, a befejezési értéket és a teljes tartalomméretet, például: "bytes=0-1023/10100" |
     | **Tartalomtípusa** | <*tartalomtípus*> | Sztring | A darabolt tartalom típusa |
     | **Tartalom hossza** | <*tartalom hossza*> | Sztring | Az aktuális adattömb méretének hossza bájtban |
     |||||

4. Minden PATCH-kérelem után a végpont a "200" állapotkóddal és a következő válaszfejlécekkel válaszolva megerősíti az egyes adattömbök bevételezését:

   | Végpont válaszfejléc-mezője | Típus | Kötelező | Leírás |
   |--------------------------------|------|----------|-------------|
   | **Tartomány** | Sztring | Igen | A végpont által fogadott tartalom bájttartománya, például: "bytes=0-1023" |   
   | **x-ms-darabméret** | Egész szám | Nem | A javasolt adattömbméret bájtban |
   ||||

Ez a műveletdefiníció például egy HTTP POST-kérelmet jelenít meg a darabolt tartalom végpontra való feltöltéséhez. A művelet `runTimeConfiguration` tulajdonságában a `contentTransfer` tulajdonság `transferMode` `chunked`a következőket állítja be:

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
