---
title: Paramétereket létrehozó Azure Monitor-munkafüzetek
description: Az összetett jelentések egyszerűsítése előre összeállított és egyéni paraméterezett munkafüzetekkel
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658217"
---
# <a name="workbook-parameters"></a>Munkafüzet paraméterei

A paraméterek lehetővé teszik a munkafüzet-szerzők számára, hogy adatokat gyűjtsenek a fogyasztóktól, és a munkafüzet más részeiben hivatkozhassanak rá – általában az eredményhalmaz hatókörére vagy a megfelelő vizualizáció beállítására. Ez egy kulcsfontosságú képesség, amely lehetővé teszi a szerzők számára, hogy interaktív jelentéseket és tapasztalatokat építsenek. 

A munkafüzetek segítségével szabályozhatja, hogy a paramétervezérlők hogyan jelenjenek meg a fogyasztók számára – szövegmező vs. legördülő, egy- és többválasztó, szöveges értékek, JSON, KQL vagy Azure Resource Graph stb.  

A támogatott paramétertípusok a következők:
* [Idő](workbooks-time.md) - lehetővé teszi a felhasználó számára, hogy válasszon az előre kitöltött időtartományok közül, vagy válasszon egy egyéni tartományt
* [Legördülő menü](workbooks-dropdowns.md) lehetővé teszi, hogy a felhasználó válasszon egy értékvagy értékhalmaz közül
* [Szöveg](workbooks-text.md) - lehetővé teszi a felhasználó számára, hogy tetszőleges szöveget adjon meg
* [Erőforrás](workbooks-resources.md) – lehetővé teszi, hogy a felhasználó egy vagy több Azure-erőforrást válasszon ki
* [Előfizetés](workbooks-resources.md) – lehetővé teszi a felhasználó számára egy vagy több Azure-előfizetési erőforrás kiválasztását
* Erőforrástípusa – lehetővé teszi, hogy a felhasználó egy vagy több Azure-erőforrástípus-értéket válasszon ki
* Hely – lehetővé teszi, hogy a felhasználó egy vagy több Azure-helyértéket válasszon ki

Ezekre a paraméterértékekre a munkafüzetek más részeiben is hivatkozhatnak kötések vagy értékbővítések segítségével.

## <a name="creating-a-parameter"></a>Paraméter létrehozása
1. Kezdje üres munkafüzettel szerkesztési módban.
2. Válassza a _Paraméterek hozzáadása_ lehetőséget a munkafüzetben lévő hivatkozásokból.
3. Kattintson a kék _Add Parameter_ gombra.
4. Az új paraméter ablaktáblán, amely megjelenik adja meg a következőt:
    1. Paraméter neve: `TimeRange` *(vegye figyelembe, hogy a __paraméternevek__ **nem** tartalmazhatnak szóközt vagy speciális karaktereket)*
    2. Megjelenítendő `Time Range`név: *(azonban a __megjelenítendő nevek__ szóközöket, speciális karaktereket, emojikat stb.)*  
    2. Paraméter típusa:`Time range picker`
    3. Szükséges:`checked`
    4. Elérhető időtartományok: Utolsó óra, Utolsó 12 óra, Utolsó 24 óra, Utolsó 48 óra, Utolsó 3 nap, Utolsó 7 nap és Egyéni időtartomány-kijelölés engedélyezése
5. A paraméter létrehozásához válassza a "Mentés" lehetőséget az eszköztáron.

   ![Időtartomány-paraméter létrehozásáról kép](./media/workbooks-parameters/time-settings.png)

Így fog kinézni a munkafüzet olvasási módban, a "Pills" stílusban.

   ![Időtartomány-paramétert ábrázoló kép olvasási módban](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Hivatkozás egy paraméterre
### <a name="via-bindings"></a>Keresztül kötések
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. Nyissa meg az _Időtartomány_ `Time Range` legördülő menüt, és válassza ki a beállítást az alsó Paraméterek szakaszban.
3. Ez köti az időtartomány paraméterét a diagram időtartományához. A mintalekérdezés időhatóköre most már 24 óra.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![Kötéseken keresztül hivatkozott időtartomány-paramétert ábrázoló kép](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>A kql-ban
1. Vegyen fel egy lekérdezésvezérlőt a munkafüzetbe, és jelöljön ki egy Application Insights-erőforrást.
2. A KQL-ben adjon meg egy időhatókör-szűrőt a következő paraméter használatával:`| where timestamp {TimeRange}`
3. Ez kibővíti a `| where timestamp > ago(1d)`lekérdezés kiértékelési idejét , amely a paraméter időtartományának értéke.
4. Lekérdezés futtatása az eredmények megtekintéséhez

    ![KQL-ben hivatkozott időtartományt ábrázoló kép](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>Szövegben 
1. Szövegvezérlő hozzáadása a munkafüzethez.
2. A jelölés lefelé mezőbe írja be a`The chosen time range is {TimeRange:label}`
3. Válassza a _Kész szerkesztés lehetőséget_
4. A szövegvezérlő szöveget jelenít meg: _A kiválasztott időtartomány az elmúlt 24 óra_

## <a name="parameter-options"></a>Paraméterbeállítások
A _Szövegben_ szakasz `label` a paraméter értékét használta az értéke helyett. A paraméterek a típusuktól függően különböző beállításokat fednek fel - például az időtartomány-választók engedélyezik az értéket, a címkét, a lekérdezést, a kezdést, a befejezést és a szemsémét.

A `Previews` _Paraméter szerkesztése_ ablaktábla szakaszában megtekintheti a paraméter bővítési beállításait:

![Időtartomány-paraméter beállításait ábrázoló kép](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>További lépések

* [Ismerkedés a](workbooks-visualizations.md) munkafüzetekkel, számos gazdag vizualizációs lehetőséggel.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
