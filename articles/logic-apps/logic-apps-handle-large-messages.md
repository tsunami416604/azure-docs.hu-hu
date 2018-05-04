---
title: Az Azure Logic Apps nagy méretű üzenetek kezeléséhez |} Microsoft Docs
description: Ismerje meg, hogyan kezelje a logic apps adattömbösítő a nagy méretű üzenetek méretének
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: SyntaxC4
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 4/27/2018
ms.author: shhurst; LADocs
ms.openlocfilehash: 421a207456908fa3b10582c2287b1b2467ff74b1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="handle-large-messages-with-chunking-in-logic-apps"></a>A Logic Apps adattömbösítő a nagy méretű üzenetek kezeléséhez

Üzenetek kezelésekor a Logic Apps üzenet tartalom mérete korlátozza.
Ezt a határt, csökkentheti a terhelés hozta létre tárolja, és nagy méretű üzenetek feldolgozása.
Ennél nagyobb üzenetek kezeléséhez, a Logic Apps is *adatrészlet* nagy méretű üzenetek kisebb üzenetbe. Így továbbra is átviheti nagy fájlokat a Logic Apps meghatározott feltételek mellett.
Ha más szolgáltatások összekötők vagy HTTP keresztül kommunikál, a Logic Apps nagy méretű üzenetek is felhasználhatnak, de *csak* található adattömböket.
Ez azt jelenti, összekötők is támogatnia kell a adattömbösítő, vagy az alapul szolgáló HTTP üzenet exchange Logic Apps, és ezek a szolgáltatások közötti adattömbösítő kell használnia.

Ez a cikk bemutatja, hogyan beállíthat adattömbösítő támogatott kezelése üzeneteket, amelyek mérete meghaladja a korlátot.

## <a name="what-makes-messages-large"></a>Hasznossá üzenetek "nagy"?

Üzenetek nagyok "" a szolgáltatás kezelése az üzenetek alapján.
A pontos méretkorlátot a nagy méretű üzenetek eltér a logikai alkalmazások és összekötők.
A Logic Apps, mind az összekötők nem lehet közvetlenül felhasználni a nagy méretű üzenetek, amely darabolásos kell lennie. A Logic Apps üzenet méretkorlátot, lásd: [Logic Apps korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md).
Minden egyes összekötőt üzenet méretkorlátot, tekintse meg a [összekötő technikai részleteket](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Kezelése a Logic Apps darabolt üzenet

A Logic Apps közvetlenül nem használható a Logic Apps üzenet mérete korláton darabolt üzenetek kimeneteinek.
Csak azok a műveletek, amelyek támogatják a adattömbösítő hozzáférhet a kimeneti üzenet tartalma.
Egy műveletet, amely kezeli a nagy méretű üzenetek kell tehát *vagy*:

* Natív módon támogatja a adattömbösítő, ha a művelet egy összekötő tartozik.
* Rendelkezik adattömbösítő támogatás futásidejű konfigurációban, hogy a művelet engedélyezett.

Ellenkező esetben futásidejű hiba kap, amikor megpróbál hozzáférni a nagy méretű tartalom kimeneti.
Engedélyezheti a adattömbösítő [támogatási adattömbösítő beállítása](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Az összekötők kezelése darabolt üzenet

A Logic Apps kommunikációs szolgáltatások saját üzenet méretkorlátai rendelkezhet.
Ezek a korlátozások általában kisebb, mint a Logic Apps korlátot. Például feltéve, hogy az összekötő támogatja az adattömbösítő, összekötő érdemes lehet nagyobb, mint 30 MB-os üzenet Logic Apps azonban nem.
Ahhoz, hogy megfeleljenek az összekötő korlátot, a Logic Apps felosztja a minden üzenet 30 MB-nál nagyobb kisebb csoportjai.

Amely támogatja a adattömbösítő összekötők a csonkolási protokollt nem látható a végfelhasználók számára.
Azonban nem minden összekötők támogatja adattömbösítő, így az összekötők futásidejű hibákat generálnak, amikor a bejövő üzenetek túllépi az összekötők méretkorlátait.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>HTTP Protokollon keresztül adattömbösítő beállítása

Általános HTTP-forgatókönyvekben nagy tartalomletöltésekhez lehet megosztani, és feltölti HTTP,-kapcsolaton keresztül, hogy a Logic Apps alkalmazást és egy végpontot továbbíthatja az nagy méretű üzenetek. Azonban kell darabos üzenetek a módon, hogy a Logic Apps vár.

A végpont engedélyezte a letöltések vagy feltöltések adattömbösítő, a HTTP-műveleteket a Logic Apps alkalmazást az automatikusan darabos nagy méretű üzenetek. Ellenkező esetben kell állítania a végpont támogatási adattömbösítő. Ha nem rendelkezhet, és szabályozhatja a végpont vagy az összekötő, nincs lehetőség adattömbösítő beállítása.

Is, ha egy HTTP-művelet már nem engedélyezi az adattömbösítő, is be kell állítania az műveletben adattömbösítő `runTimeConfiguration` tulajdonság.
Ez a tulajdonság a művelet belül közvetlenül a kód nézet szerkesztése később leírtak szerint, vagy a Logic Apps Designer Itt állíthatja be:

1. A HTTP-művelet jobb felső sarokban, kattintson a jobb gombbal a hárompontozást jelző gombra (**...** ), és válassza a **beállítások**.

2. A **tartalom átviteléhez**, beállíthatja **engedélyezése adattömbösítő** való **a**.

3. A letöltések vagy feltöltések adattömbösítő telepítésének folytatásához, folytassa a következő szakaszok.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Az adattömbök tartalom letöltése

Sok végpont automatikusan adattömbök HTTP GET kérelemre keresztül, amikor nagy méretű üzenetek küldése.
A darabolt üzenetek letöltését a végpont HTTP Protokollon keresztül, a végpont támogatnia kell a részleges tartalomkérelmek vagy *letöltések darabolásos*. A Logic Apps alkalmazást egy HTTP GET kérést küld egy végpontra letölti a tartalmat, és a végpont "206" állapotkóddal válaszol, akkor a válasz darabolt tartalom tartalmaz.
A Logic Apps nem szabályozhatja, hogy a végpont részleges kérelmeket támogatja.
A Logic Apps alkalmazást lekérdezi az első "206-os" választ, ha az a logikai alkalmazás automatikusan elküldi kéréseket letölteni a tartalmat.

Ellenőrizze, hogy a végpont támogathatja a részleges tartalom, a HEAD kérelem küldése. Ehhez a kérelemhez segít meghatározni, hogy a válasz tartalmazza a `Accept-Ranges` fejléc.
Ily módon, ha a végpont támogatja a darabolásos letöltések, de nem küldött darabolt tartalom, akkor *javaslat* ezt a beállítást úgy, hogy a `Range` a HTTP GET kérés fejléc.

Ezeket a lépéseket a Logic Apps használ a darabolt tartalom letöltése a végpont a logikai alkalmazáshoz részletes folyamatát írják le:

1. A Logic Apps alkalmazást egy HTTP GET kérést küld a végpont.

   A kérelem fejlécében választhatóan is egy `Range` a tartalom adattömböket a kért bájttartomány a mezőbe.

2. A végpont válaszol, a "206" állapotkódot és azt egy HTTP-üzenet törzsében.

    A válasz jelenik meg a adatrészletben tartalommal kapcsolatos adatokat `Content-Range` határozza meg a kezdő- és a rendszer, valamint a teljes tartalom teljes méretének befejezése fejléc, beleértve a Logic Apps információkat adattömbösítő előtt.

3. A logikai alkalmazás automatikusan elküldi követő HTTP GET kérelmeket.

    A Logic Apps alkalmazást követő GET kérelmek küld, amíg a teljes tartalom lekérése sikertelen.

Például ez a művelet a definíció látható, HTTP GET kérelemre, amely beállítja a `Range` fejléc, *javasolhat* , hogy a végpont válaszolni darabolásos tartalom:

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

A GET kérés értékűre állítja be a "Tartomány" fejléc "bájt = 0-1023", amely az, hogy a tartomány, bájtban kifejezve. A végpont kérelmeket támogatja a részleges tartalom, ha a végpont válaszol, a tartalom rendszer a kért tartományból.
A végpont alapján, az "Tartomány" fejlécmező formátummal eltérőek lehetnek.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Az adattömbök tartalom feltöltése

A HTTP-művelethez darabolt tartalom feltöltése, a művelet a művelet csonkolási támogatását rendelkezik engedélyezve `runtimeConfiguration` tulajdonság.
Ez a beállítás lehetővé teszi a művelettel indítja el a csonkolási protokoll.
A Logic Apps alkalmazást is elküldheti egy kezdeti POST vagy PUT üzenet a cél-végponthoz.
Után a végpont javasolt adattömbméretet válaszol, a logikai alkalmazás következik HTTP javítására, de a tartalom adattömbök tartalmazó kérések küldésével.

Ezeket a lépéseket a Logic Apps használ a végpont a Logic Apps alkalmazást a darabolt tartalom feltöltése részletes folyamatát írják le:

1. A Logic Apps alkalmazást egy üres üzenettörzs kezdeti HTTP POST, PUT vagy kérést küld. A kérelem fejlécében a a Logic Apps alkalmazást szeretne feltölteni az adattömböket a tartalomra vonatkozó információkat tartalmazza:

   | A Logic Apps fejlécmező kérése | Érték | Típus | Leírás |
   |---------------------------------|-------|------|-------------|
   | **x-ms-átviteli-mód** | darabolásos | Karakterlánc | Azt jelzi, hogy a tartalmat az adattömbök feltöltése |
   | **x-ms-content-length** | <*tartalom hosszúságú*> | Egész szám | A teljes tartalom mérete bájtban adattömbösítő előtt |
   ||||

2. A végpont válaszol, "200" sikeres állapotkódot és azt a kiegészítő adatokat:

   | Végpont válasz fejlécmező | Típus | Szükséges | Leírás |
   |--------------------------------|------|----------|-------------|
   | **x-ms-adatrészletméretnek** | Egész szám | Nem | A javasolt adatrészlet mérete bájtban |
   | **Hely** | Karakterlánc | Nem | URL-címét hova küldje a HTTP-javítás üzenetek |
   ||||

3. A Logic Apps alkalmazást hoz létre, és elküldi követő HTTP javítás üzenetek - az ezeket az információkat:

   * A tartalom rendszer alapján **x-ms-adatrészletméretnek** vagy néhány belső számított méretének minden a tartalom összesítés **x-ms-content-length** egymás után feltöltése

   * Az egyes javítás üzenetben küldött tartalom adatrészlet fejléc adatokat:

     | A Logic Apps fejlécmező kérése | Érték | Típus | Leírás |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*tartomány*> | Karakterlánc | Az aktuális tartalom adatrészlet, beleértve a kezdő érték, a befejezési értéket, és a teljes tartalom mérete, például a bájttartomány: "bájt = 0-1023/10100" |
     | **Tartalomtípus** | <*tartalomtípus*> | Karakterlánc | A darabolt tartalomtípushoz |
     | **Content-Length** | <*tartalom hosszúságú*> | Karakterlánc | A jelenlegi adattömegen száma bájtban hossza |
     |||||

4. Minden javítás kérelem után a végpont megerősíti, hogy az egyes adattömbök fogadását által, a "200" állapotkód válaszol.

Ez a művelet a definíció például egy HTTP POST-kérelmet a darabolt tartalom feltöltése a végpont jeleníti meg.
A művelet `runTimeConfiguration` tulajdonság, a `contentTransfer` tulajdonság beállítása `transferMode` való `chunked`:

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