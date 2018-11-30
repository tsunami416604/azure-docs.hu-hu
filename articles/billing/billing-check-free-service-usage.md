---
title: Az ingyenes Azure-szolgáltatások figyelése és nyomon követésére használati |} A Microsoft Docs
description: Ismerkedjen meg az ingyenes szolgáltatások használatának ellenőrzése. Az Azure portal és a használati csv használ.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: cwatson
ms.openlocfilehash: cb3584101dea4dc8d8d888632175415480a6a1b3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581528"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Az ingyenes Azure-fiókkal ingyenes szolgáltatásokat használatának ellenőrzése 

Nem díjkötelesek szolgáltatásokért díjmentesen ingyenes Azure-fiókkal, kivéve, ha ezek a szolgáltatások korlátozásait meghaladja. Továbbra is a korlátokat, vagy használhatja az Azure portal vagy a használati fájlban figyeléséhez és ingyenes szolgáltatások használatának nyomon követéséhez. 

## <a name="check-usage-on-the-azure-portal"></a>Ellenőrizze a használatot az Azure Portalon

1.  Jelentkezzen be az [Azure Portalra]( http://portal.azure.com).

2.  Válassza ki a bal oldali navigációs területen **minden szolgáltatás**.

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki a létrehozott való regisztráció során az ingyenes fiók előfizetést.

    ![Előfizetések bemutató képernyőkép](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Az Áttekintés szakasz bemutatja, például az előfizetés-Azonosítójára, az előfizetéssel kapcsolatos alapvető információkat kínálnak, típusa és az előfizetés nevét. Ha lenne jár le a fiókjához tartozó ingyenes kreditekkel információkat is talál.

    ![Előfizetés alapvető információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Görgessen le a jelenlegi és az előre jelzett költség található információkat. A költségek az ingyenes fiókkal nem szereplő szolgáltatások használatát és a ingyenesen használhatja a szolgáltatásokat meghaladó használat tartalmazza. 

    ![Előfizetési költség információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Az Áttekintés szakasz utolsó része belefoglalja rendelkezik a ingyenes szolgáltatások használatát. 

    ![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tábla a következő oszlopokat tartalmazza:

* **Fogyasztásmérő neve:** azonosítja a rendszer, elérheti mérő mértékegysége. Fogyasztásmérő leképezés szolgáltatás kapcsolatos további információkért lásd: [mérőszám leképezés ingyenes szolgáltatás ismertetése](billing-understand-free-service-meter-mapping.md). 
* **Használat/Limit:** aktuális havi használat és a mértékegységek korlátot. Ezeket az adatokat az állapotsorban is megkeresheti.
* **Állapot:** mérőszám használati állapotát. A használati minta alapján, akkor az alapszabály egyikét.
  * **Nincs használatban:** még nem használta a mérőszám, vagy a mérőszám a használata nem érte el a számlázási rendszerhez.
  * **Túllépés időpontja \<dátum >:** a túllépte a korlátot, a mértékegységek \<dátum >.
  * **Nem valószínű, hogy Exceed:** valószínűleg nem lépi túl a korlátot, az a fogyasztásmérő áll.
  * **Túllépés \<dátum >:** valószínűleg meghaladja a korlátot, a mérő az \<dátum >.


## <a name="check-usage-through-the-usage-file"></a>Ellenőrizze a használatot a használatot részletező fájl segítségével

A használati fájlban az Azure-előfizetéshez a részletes információkat szolgáltat. A havi vagy a napi használati fájlt tölthet le Azure fiókkezelési központjába. Ismerje meg, hogyan a használatot részletező fájl letöltése és megismerése a hozzáférés szükséges, lásd: [első számlák és használati adatok](billing-download-azure-invoice-daily-usage-date.md). A használatot részletező fájl oszlopai kapcsolatos további információkért lásd: [a használat feltételeinek értelmezése](billing-understand-your-usage.md). 

A használatot részletező fájl egyaránt ingyenes vagy fizetős szolgáltatás használati adatait tartalmazza. Ingyenes szolgáltatás mérőszámok kellene **ingyenes** hozzáfűzi a végén a fogyasztásmérő nevét. Ingyenes mérőszámok, nyissa meg a fájlt az excel- és szűrő keresése a **mérőszám kategóriája oszlop** szöveget tartalmazó cellák **– ingyenes** (használata Szövegszűrők &rarr; tartalmazza szűrő) &nbsp;

![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).