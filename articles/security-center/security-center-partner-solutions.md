---
title: Partnermegoldások kezelése az Azure Security Centerben | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan tekintheti át az Azure Security Centerben az Azure-előfizetésében integrált partnermegoldások biztonsági állapotát.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/22/2016
ms.author: terrylan

---
# Partnermegoldások figyelése az Azure Security Centerrel
Ebből a dokumentumból megtudhatja, hogyan figyelhető meg az Azure Security Centerben a partnermegoldások biztonsági állapota.

> [!NOTE]
> A dokumentumban szereplő információk az Azure Security Center előzetes verziójára vonatkoznak. Ez a dokumentum egy mintatelepítés segítségével mutatja be a szolgáltatást. Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
> 
> 

## Mi az a Security Center?
 A Security Center a láthatóság növelésével segít a fenyegetések megelőzésében, észlelésében és kezelésében, és hozzájárul az Azure-erőforrások biztonságának felügyeletéhez. A biztonsági megoldások átfogó ökoszisztémájának alkalmazásával az összes előfizetésére vonatkozóan integrált biztonságfelügyeletet és szabályzatkezelést biztosít, és segít felderíteni az egyébként nehezen észlelhető fenyegetéseket.

## Partnermegoldások figyelése
A **Partnermegoldások** csempén a **Security Center** panelen áttekintheti az Azure-előfizetésében integrált partnermegoldások biztonsági állapotát.
![Partnermegoldások csempe][1]

A **Partnermegoldások** csempén a partnermegoldások száma és a partnermegoldások állapotösszegzése látható.

Egy partnermegoldás **ÁLLAPOTA** a következő lehet:

* Védett (zöld) – nincs az állapottal kapcsolatos probléma.
* Nem megfelelő (piros) – azonnali figyelmet igénylő állapottal kapcsolatos probléma.
* Jelentéskészítés leállt (narancs) – a megoldás állapotára vonatkozó jelentések készítése leállt.
* Ismeretlen védelmi állapot (narancs) – a megoldás állapota jelenleg ismeretlen, mert egy új erőforrás hozzáadása a meglévő megoldáshoz nem sikerült.
* Nem jelentett (szürke) – a megoldás még nem jelentett semmit, a csak most csatlakoztatott és még telepítés alatt lévő megoldás állapota lehet nem jelentett.

Ha előfizetésében nincsenek partnermegoldások integrálva, akkor a csempén ez az információ jelenik meg. A **Partnermegoldások** csempe kiválasztása után megnyithatja a **Javaslatok** panelt a biztonsági partnermegoldások telepítéséhez.
![Nincsenek partnermegoldások][2]

Partnermegoldások állapotának megtekintése:

1. Válassza a **Partnermegoldások** csempét. Ekkor megnyílik egy panel, amely a Security Centerrel összekapcsolt partnermegoldások listáját tartalmazza.
   ![Partnermegoldások][3]
2. Válasszon ki egy partnermegoldást. Ebben a példában az **F5-WAF2** megoldást választjuk ki.  Ekkor megnyílik egy panel, amely az adott partnermegoldás és a megoldáshoz kapcsolódó erőforrások állapotát tartalmazza. A **Megoldás konzol** kiválasztásával megnyílik a partnernek az adott megoldásra vonatkozó felügyeleti felülete.
   ![Partnermegoldás részletei][4]
3. Lépjen vissza az **F5-WAF2** panelre, és válassza az **Összekapcsolás** lehetőséget. Ekkor megnyílik az **Összekapcsolás** panel. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   ![Erőforrásokat hozzákapcsolása a partnermegoldáshoz][5]

## Következő lépések
Ebben a dokumentumban megismerhette a Security Center **Partnermegoldások** csempéjét. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-policies.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Annak bemutatása, hogy miként segítik a javaslatok az Azure-erőforrások védelmét.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Annak bemutatása, hogy miként figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Annak bemutatása, hogy miként kezelheti a biztonsági riasztásokat, és hogyan reagálhat rájuk.
* [Azure Security Center: GYIK](security-center-faq.md) – Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) –  Értesüljön az Azure-ral kapcsolatos legfrissebb  biztonsági hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Jun16_HO2-->


