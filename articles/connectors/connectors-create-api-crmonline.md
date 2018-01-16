---
title: "Csatlakozás az Azure Logic Apps Dynamics 365 (online) |} Microsoft Docs"
description: "Munkafolyamatokat logic app által kezelt Dynamics 365 (online) entitásokat a Dynamics 365-összekötő által nyújtott API-n keresztül"
services: logic-apps
cloud: Azure Stack
author: Mattp123
manager: anneta
documentationcenter: 
tags: connectors
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp; LADocs
ms.openlocfilehash: d35647921ff540167a3a591fb489d3bab031a5c1
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2018
---
# <a name="connect-to-dynamics-365-from-logic-app-workflows"></a>Dynamics 365 csatlakoztatja a logic app munkafolyamatok

A Logic Apps (online) a Dynamics 365 csatlakozhat, és hozzon létre hasznos üzleti flow-rekordok létrehozása, elemek frissítését vagy a rekordok listáját adja vissza. A Dynamics 365-összekötővel a következőket teheti:

* Az üzleti folyamat Dynamics 365 (online) származó adatok alapján történő létrehozása.
* Amely válaszol, és végezze el a kimeneti elérhető egyéb műveletek műveleteket használni. Például frissítésekor elem Dynamics 365 (online), az Office 365-tel e-mailt küldhet.

Ez a témakör bemutatja, hogyan hozzon létre egy logikai alkalmazás, amely Dynamics 365 létrehoz egy feladatot, amikor egy új vezető Dynamics 365 jön létre.

## <a name="prerequisites"></a>Előfeltételek
* Egy Azure-fiók.
* Dynamics 365 (online) fiók.

## <a name="create-a-task-when-a-new-lead-is-created-in-dynamics-365"></a>Hozzon létre egy feladatot, amikor egy új vezető Dynamics 365 hoz létre

1.  [Jelentkezzen be Azure](https://portal.azure.com).

2.  Az Azure search mezőbe írja be `Logic apps`, és nyomja le az ENTER BILLENTYŰT.

      ![Logic Apps alkalmazások keresése](./media/connectors-create-api-crmonline/find-logic-apps.png)

3.  A **a Logic apps**, kattintson a **Hozzáadás**.

      ![LogicApp hozzáadása](./media/connectors-create-api-crmonline/add-logic-app.png)

4.  A logikai alkalmazás létrehozása, végezze el a **neve**, **előfizetés**, **erőforráscsoport**, és **hely** mezőket, és kattintson **létrehozása**.

5.  Válassza ki az új logikai alkalmazás. Amikor megjelenik a **sikeres telepítés** értesítést, kattintson a **frissítése**.

6.  A **Fejlesztőeszközök**, kattintson a **Logic App Designer**. Sablon, kattintson a **üres logikai alkalmazás**.

7.  Írja be a keresőmezőbe, `Dynamics 365`. Válassza ki a Dynamics 365 eseményindítók listáról **Dynamics 365 – amikor létrejön egy bejegyzés**.

8.  Ha jelentkezzen be a Dynamics 365 kéri, tegye meg.

9.  Az eseményindító részleteit adja meg a következő adatokat:

  * **Szervezet neve**. Válassza ki a Dynamics 365 példányt, amelyet a logikai alkalmazás figyelésére.

  * **Entitás neve**. Jelölje ki a figyelni kívánt entitás. Ez az esemény úgy működik, mint egy eseményindító indítsa el a logikai alkalmazást. 
  Ebben a bemutatóban **érdeklődők** van kiválasztva.

  * **Milyen gyakran kívánt elemek kereséséhez?** Ezeket az értékeket be, milyen gyakran kérdezze le a logikai alkalmazást az eseményindító kapcsolódó frissítések. Az alapértelmezett érték három percenként frissítések kereséséhez.

    * **Gyakoriság**. Válassza ki a másodperc, perc, órák vagy napok.

    * **Időköz**. Adja meg másodpercben, perc, óra vagy előtt a következő ellenőrzés átadni kívánt napok számát.

      ![Logic App eseményindító részletei](./media/connectors-create-api-crmonline/trigger-details.png)

10. Kattintson a **új lépés**, és kattintson a **művelet hozzáadása**.

11. Írja be a keresőmezőbe, `Dynamics 365`. Válassza a műveletek listájának **Dynamics 365 – új rekord létrehozása**.

12. Adja meg a következő információkat:

    * **Szervezet neve**. Válassza ki a Dynamics 365-példányt, amelyen a folyamatot a rekord létrehozására. 
    Figyelje meg, hogy ez a példány nem feltétlenül a példányt, amelyen az esemény akkor váltódik ki, a.

    * **Entitás neve**. Jelölje ki az esemény kiváltásakor rekordot kell létrehozni kívánt entitás. 
    Ebben a bemutatóban **feladatok** van kiválasztva.

13. Kattintson a **tulajdonos** meg. A dinamikus tartalom megjelenő listában kiválaszthatja azt a mezők valamelyikét:

    * **Vezetéknév**. Ez a mező kiválasztása szúr be a tevékenység, a tulajdonos mezőben a vezetéknevet az érdeklődési, a feladat rekord létrehozásakor.
    * **A témakör**. A témakör az érdeklődési mező kiválasztása ebben a mezőben szúr be a tevékenység, a tulajdonos mezőben, a feladat rekord létrehozásakor. 
    Kattintson a **témakör** azt, hogy a **tulajdonos** mezőbe.

      ![Logic App hozzon létre új rekord részletei](./media/connectors-create-api-crmonline/create-record-details.png)

14. A Logic App Designer eszköztáron kattintson **mentése**.

    ![Logic App Designer eszköztáron menteni](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15. A logikai alkalmazás elindításához kattintson **futtatása**.

    ![Logic App Designer eszköztáron menteni](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Most hozzon létre rendszer Dynamics 365 az értékesítés, és tekintse meg a folyamat a művelet!

## <a name="set-advanced-options-for-a-logic-app-step"></a>A logic app lépés speciális beállításainak megadása

Adja meg a logic app lépésben adatok szűrése, kattintson a **speciális beállítások megjelenítése** a lépés, majd adja hozzá egy szűrőt, vagy az order lekérdezésével.

A szűrő lekérdezés segítségével például a fiók neve csak az aktív fiókok és a sorrend beolvasása. Ez a feladat végrehajtásához adja meg az OData-szűrő lekérdezés `statuscode eq 1`, és válassza ki **fióknév** a dinamikus tartalom listából. További információ: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) és [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

![Speciális beállítások logikai alkalmazás](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Gyakorlati tanácsok a speciális beállításokkal

Érték mező hozzáadásakor meg kell egyeznie a mező típusa, írjon be egy értéket, vagy kiválaszthat egy értéket a dinamikus tartalom listából.

Mezőtípus  |A használat módja  |Hol található  |Name (Név)  |Adattípus  
---------|---------|---------|---------|---------
Szövegmező|Szövegmezők egyetlen sor szöveget vagy a dinamikus tartalmat, amely szöveges típusú mező szükséges. Például a kategória és alkategória mező.|Beállítások > testreszabások > a rendszer testreszabását > entitások > Feladat > mezők |category |Egysoros szövegmező        
Egész mezők | Egyes mezők egész szám vagy a dinamikus tartalmat a mezőnek egész típus szükséges. Például készültségi szint és időtartama. |Beállítások > testreszabások > a rendszer testreszabását > entitások > Feladat > mezők |KészültségiSzint |Egész szám         
Dátummezők | Egyes mezők dátummal hh/nn/éééé formátumban vagy dinamikus tartalmat a dátum típusú mező szükséges. Ilyen például létrehozva, kezdő dátum, Tényleges kezdés utolsó tartsa idő, a tényleges befejezési és a határidő. | Beállítások > testreszabások > a rendszer testreszabását > entitások > Feladat > mezők |createdon |Dátum és idő
Írja be a szükséges mind a rekord azonosítója és a keresési mezők |Néhány egy másik entitás bejegyzés hivatkozó mező kötelező, a Rekordazonosító és a keresési típus. |Beállítások > testreszabások > a rendszer testreszabását > entitások > fiók > mezők  | accountid  | Elsődleges kulcs

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>További példák a mezőket, amelyeknek szükséges a rekord azonosítója és a keresési írja be.
Az előző táblázatban kibővítve, az alábbiakban további példák a mezőket, amelyeknek nem működnek a dinamikus tartalom listán kijelölt értékekkel. Ehelyett ezeket a mezőket kell mindkét egy Azonosítót és a keresési rekordtípus a mezőkben a powerapps segítségével.  
* Tulajdonos és a tulajdonos típusa. A tulajdonos mezőben kell lennie egy érvényes felhasználó vagy csoport rekord. A tulajdonos típusúnak kell lennie, vagy **systemusers** vagy **csapatok**.
* Ügyfél és az ügyfél típusa. Az ügyfél mező kell egy érvényes fiókot vagy lépjen kapcsolatba a rekordazonosító. A tulajdonos típusúnak kell lennie, vagy **fiókok** vagy **névjegyek**.
* Valamint típus tekintetében. A kapcsolódó elemek mezőt kell egy érvényes Rekordazonosítója, például egy fiók vagy lépjen kapcsolatba a rekordazonosító. A vonatkozó típusnak kell lennie a Keresés a rekord, például a **fiókok** vagy **névjegyek**.

Ez a feladat létrehozása művelet példa ad hozzá egy fiók bejegyzést, amely megfelel a hozzáadná a vonatkozó mező a feladat azonosítója.

![Attribútumfolyam recordId, és írja be a fiók](./media/connectors-create-api-crmonline/recordid-type-account.png)

Ebben a példában is hozzárendeli a feladat egy adott felhasználó az a felhasználói rekord azonosítója alapján.

![Attribútumfolyam recordId, és írja be a fiók](./media/connectors-create-api-crmonline/recordid-type-user.png)

A Rekordazonosító található, a következő részben: *található a rekord azonosítója*

## <a name="find-the-record-id"></a>Található a rekord azonosítója

1. Nyissa meg a rekord, például egy fiók rekordot.

2. Kattintson a Műveletek eszköztár **kiugró** ![felbukkanó rekord](./media/connectors-create-api-crmonline/popout-record.png).
Azt is megteheti, a műveletek a teljes URL-címet az alapértelmezett e-mail program történő másolásához kattintson eszköztár **e-MAILT A hivatkozás**.

   A Rekordazonosító Between az URL-címének karakterek kódolása %d 7b és %7 jelenik meg.

   ![Attribútumfolyam recordId, és írja be a fiók](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Hibaelhárítás
A logikai alkalmazás egy hibás lépés elhárításához az esemény részletes állapotjelzés megjelenítéséhez.

1. A **Logic Apps**, válassza ki a Logic Apps alkalmazást, és kattintson a **áttekintése**. 

   Az összegzési területen látható, és a futási állapotát biztosít a logikai alkalmazást. 

   ![Logikai alkalmazás futtatási állapot](./media/connectors-create-api-crmonline/tshoot1.png)

2. Összes sikertelen futtatása kapcsolatos további információk megtekintéséhez kattintson a hibás esemény. Bontsa ki a hibás lépést, kattintson a lépés.

   ![Bontsa ki a hibás lépést](./media/connectors-create-api-crmonline/tshoot2.png)

   A lépés részletei jelennek meg, és segíthet a hiba okának elhárítása.

   ![Nem sikerült lépés részletei](./media/connectors-create-api-crmonline/tshoot3.png)

A logic apps hibaelhárítással kapcsolatos további információkért lásd: [logic app hibák diagnosztizálása](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-specific-details"></a>Összekötő-specifikus részletei

Bármely eseményindítók és a swagger definiált műveletek megtekintése, és semmilyen határnak a Lásd még: a [connector részleteket](/connectors/crm/). 

## <a name="next-steps"></a>További lépések
Az egyéb rendelkezésre álló összekötők Logic Apps, megismerkedhet a [API-k lista](apis-list.md).
