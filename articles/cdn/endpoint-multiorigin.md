---
title: Azure CDN Endpoint multi-Origin
description: Ismerkedjen meg Azure CDN Endpoint több eredetével.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: how-to
ms.date: 8/20/2020
ms.author: allensu
ms.openlocfilehash: ed6c60b4f66361e87f67f3c64bb60846b2df757b
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817579"
---
# <a name="azure-cdn-endpoint-multi-origin"></a>Azure CDN Endpoint multi-Origin

A többszörös eredetű támogatás kiküszöböli az állásidőt, és létrehozza a globális redundanciát. 

Ha több kezdőpontot választ egy Azure CDN végponton belül, a redundancia a kockázatot az egyes források állapotának kiszűrésével, és szükség esetén feladatátvételsel terjeszti ki.

Állítson be egy vagy több származási csoportot, és válasszon ki egy alapértelmezett Origó csoportot. Az egyes Origin csoportok egy vagy több olyan gyűjteményből állnak, amelyek hasonló számítási feladatokat végezhetnek.

> [!NOTE]
> Ez a funkció jelenleg csak a Microsoft Azure CDNból érhető el. 

## <a name="create-the-origin-group"></a>A Origin csoport létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

2. Válassza ki a Azure CDN profilt, majd válassza ki a többszörös forráshoz konfigurálni kívánt végpontot.

3. A végpont konfigurációjának **Beállítások** területén válassza a **forrás** lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-1.png" alt-text="CDN-végpont" border="true":::

4. A többszörös származás engedélyezéséhez szükség van legalább egy származási csoportra. Válassza ki a **Forrás létrehozása csoportot**:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-2.png" alt-text="Forrás beállításai" border="true":::

5. A **forrás csoport hozzáadása** konfiguráció területen adja meg vagy válassza ki a következő adatokat:

   | Beállítás           | Érték                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Forrás csoport neve | Adja meg a forrás csoport nevét.                                   |
   | Mintavételi állapot      | Válassza az **Engedélyezve** lehetőséget. </br> A Azure CDN a világ különböző pontjain található állapot-mintavételeket fogja futtatni a forrás állapotának meghatározásához. Ne engedélyezze, ha az aktuális Origó csoport nem aktív a további díjak elkerülése érdekében.
   | Mintavételi útvonal        | Az állapot meghatározásához használt forrás elérési útja. |
   | Mintavételi időköz    | Válasszon egy 1, 2 vagy 4 perces mintavételi időközt.                        |
   | Mintavételi protokoll    | Válassza a **http** vagy a **https**lehetőséget.                                         |
   | Mintavételi módszer      | Válassza a **fej** vagy a **beolvasás**lehetőséget.                                           |
   | Alapértelmezett Origó | Válassza ki az alapértelmezett origóként beállítani kívánt mezőt.
    
   :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-3.png" alt-text="Forrás csoport hozzáadása" border="true":::

6. Válassza a **Hozzáadás** elemet.

## <a name="add-multiple-origins"></a>Több eredet hozzáadása

1. A végpont forrás beállításainál válassza a **+ Forrás létrehozása**elemet:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-5.png" alt-text="Forrás létrehozása" border="true":::

2. Adja meg vagy válassza ki az alábbi adatokat a **forrás hozzáadása** konfigurációban:

   | Beállítás           | Érték                                                                 |
   |-------------------|-----------------------------------------------------------------------|
   | Forrás típusa | Válassza a **Storage**, a **Cloud Service**, a **Web App**vagy az **Egyéni forrás**lehetőséget.                                   |
   | Forrás gazdaneve        | Válassza ki vagy adja meg a forrás állomásnevét.  A legördülő lista felsorolja az előző beállításban megadott típusú összes rendelkezésre álló forrást. Ha az **Egyéni forrás** lehetőséget választotta a forrás típusaként, adja meg az ügyfél-forráskiszolgáló tartományát. |
   | Forrás állomásfejléce    | Adja meg az egyes kérésekhez küldendő Azure CDN, vagy hagyja meg az alapértelmezett értéket.                        |
   | HTTP-port   | Adja meg a HTTP-portot.                                         |
   | HTTPS-port     | Adja meg a HTTPS-portot.                                           |
   | Prioritás    | Adjon meg egy 1 és 5 közötti számot.       |
   | Tömeg      | 1 és 1000 közötti számot adjon meg.   |

    > [!NOTE]
    > Ha egy forrást egy Origin csoporton belül hoznak létre, azt prioritásnak és súlyozásnak kell megfelelnie. Ha egy származási csoportnak csak egy forrása van, akkor az alapértelmezett prioritás és a súlyozás értéke 1. Ha a forrás kifogástalan, a rendszer átirányítja a forgalmat a legmagasabb prioritási állapotba. Ha a forrás megállapítása nem kifogástalan állapotú, akkor a kapcsolatok a prioritás sorrendjében egy másik forráshoz lesznek kiválasztva. Ha két eredet azonos prioritással rendelkezik, akkor a forgalom elosztása a forráshoz megadott súlyozás szerint történik. 

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-6.png" alt-text="További forrás hozzáadása" border="true":::

3. Válassza a **Hozzáadás** elemet.

4. Válassza a **forrás beállítása** lehetőséget a forrás elérési útjának az összes eredethez beállításához:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-7.png" alt-text="Forrás elérési útjának konfigurálása" border="true":::

5. Kattintson az **OK** gombra.

## <a name="configure-origins-and-origin-group-settings"></a>Az Origins és a származási csoport beállításainak konfigurálása

Ha több eredete és a származási csoport is van, hozzáadhat vagy eltávolíthat különböző csoportokba tartozó eredeteket. Az azonos csoportba tartozó származásoknak hasonló számítási feladatokat kell kiszolgálni. A forgalmat a rendszer a megfelelő állapot, a prioritás és a súlyozási érték alapján osztja szét ezekbe az eredetbe. 

1. Az Azure CDN végpont forrás beállításainál válassza ki a konfigurálni kívánt származási csoport nevét:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Az Origins és a származási csoport beállításainak konfigurálása" border="true":::

2. A **frissítés forrása csoportban**válassza a **+ forrás kiválasztása**lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-9.png" alt-text="Frissítési forrás csoport" border="true":::

4. Válassza ki a csoportba felvenni kívánt forrást a legördülő mezőben, majd kattintson az **OK gombra**.

5. Győződjön meg arról, hogy a forrás hozzá lett adva a csoporthoz, majd válassza a **Mentés**lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-10.png" alt-text="A csoportba felvett további forrás ellenőrzése" border="true":::

## <a name="remove-origin-from-origin-group"></a>Forrás eltávolítása a forrás csoportjából

1. Az Azure CDN végpont forrás beállításainál válassza ki a forrás csoport nevét:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-8.png" alt-text="Forrás eltávolítása a csoportból" border="true":::

2. Ha el szeretne távolítani egy forrást a forrás csoportból, jelölje be a forrás melletti Kuka ikont, majd válassza a **Mentés**lehetőséget:

    :::image type="content" source="./media/endpoint-multiorigin/endpoint-multiorigin-11.png" alt-text="A Origin csoport törlési forrásának frissítése" border="true":::

## <a name="next-steps"></a>További lépések
Ebben a cikkben engedélyezte Azure CDN Endpoint multi-Origin használatát.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Azure CDN](./cdn-overview.md)
* [Azure CDN termék funkcióinak összehasonlítása](./cdn-features.md)
