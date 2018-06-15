---
title: Az ingyenes szolgáltatásokhoz - Azure figyelő, és nyomon követése használata |} Microsoft Docs
description: Ismerje meg, ellenőrizze az ingyenes szolgáltatásokhoz használatát. Az Azure portál és a használati csv használ.
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
ms.author: amberb
ms.openlocfilehash: 560137d92491da46fda950dec84a0a7caa3ed720
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205890"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Ellenőrizze használatát, az ingyenes Azure-fiókot az ingyenes szolgáltatásokat 

Van nem szó, a szabad az szolgáltatásait ingyenes Azure-fiókot, kivéve, ha az túllépi a szolgáltatások határain. Továbbra is a határértékeket, használhatja az Azure-portálon vagy a használati fájl figyeléséhez és nyomon követéséhez az ingyenes szolgáltatások használatát. 

## <a name="check-usage-on-the-azure-portal"></a>Ellenőrizze az Azure portál használata

1.  Jelentkezzen be az [Azure portálra]( http://portal.azure.com).

2.  A bal oldali navigációs területen válassza ki a **minden szolgáltatás**.

3.  Válassza ki **előfizetések**.

4.  Válassza ki a létrehozott regisztrálta az ingyenes fiók előfizetést.

    ![Az előfizetések képernyőkép](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  A áttekintése szakasz bemutatja, például az előfizetés-azonosító, az előfizetés kapcsolatos alapvető információkat kínálnak, típusa és az előfizetés nevét. Ha ingyenes fiók kreditje lejár volna információk is megtalálhatók.

    ![Képernyőkép a előfizetési alapvető információk](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Görgessen le a jelenlegi és előre jelzett költség található információkat. A költségek szolgáltatások nem találhatók meg az ingyenes fiók használatát és a szabad szolgáltatások meghaladó használati tartalmazza. 

    ![Képernyőkép a előfizetés költségadatok](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Az Áttekintés szakaszban utolsó része az ingyenes szolgáltatásokhoz használatát egy tábla rendelkezik. 

    ![Az ingyenes szolgáltatásokhoz használatát bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A tábla a következő oszlopokat tartalmazza:

* **A mérési Name:** azonosítja a mérő feldolgozottként mértékegysége. A mérési leképezési szolgáltatással kapcsolatos további tudnivalókért lásd: [mérő leképezési szabad szolgáltatás megismeréséhez](billing-understand-free-service-meter-mapping.md). 
* **/ Korlátja:** aktuális hónap használati és a mérési korlátot. Ez az információ az állapotsorban is található.
* **Állapot:** a mérési használati állapotát. Alapján a használati mód, akkor ezek a törvények egyikét.
  * **Nincsenek használatban:** nem használta a mérő vagy a mérő használati nem érte el a számlázási rendszerhez.
  * **Túllépte a \<dátum >:** túllépte a korlátot, a mérő az \<dátum >.
  * **Valószínű, hogy Exceed:** valószínű, hogy a mérő korláton áll.
  * **A nagyobb \<dátum >:** valószínű, hogy korláton a mérő az \<dátum >.


## <a name="check-usage-through-the-usage-file"></a>A használati fájl jelölőnégyzet-használat

A használati fájl az Azure-előfizetéshez tartozó részletes információkat tartalmaz. Azure Account Center a havi és a napi használat fájl is letölthető. Töltse le a használati fájlt, és megismerheti a szükséges hozzáférést, lásd: [számla beolvasása és a használati](billing-download-azure-invoice-daily-usage-date.md). A használati fájlban oszlopok, lásd: [a a használati feltételek megismeréséhez](billing-understand-your-usage.md). 

A használati fájl is ingyenes és fizetős szolgáltatások használati adatait tartalmazza. Ingyenes szolgáltatás mérőszámok kellene **szabad** hozzáfűzi a mérési nevének végén. Szabad mérőszámok, nyissa meg a fájlt az excel-és szűrő a **mérő kategória oszlop** szöveget tartalmazó cellák **- szabad** (szöveges szűrési &rarr; Contains szűrő) &nbsp;

![Az ingyenes szolgáltatásokhoz használatát bemutató képernyőkép](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)


## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha segítségre van szüksége, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
