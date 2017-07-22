---
title: "Biztonsági riasztások kezelése az Azure Security Centerben | Microsoft Docs"
description: "Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: bf3aa51b4b1224840ae6897ff7df86cf3d2b3477
ms.contentlocale: hu-hu
ms.lasthandoff: 04/18/2017

---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben
Ez a dokumentum segít az Azure Security Center biztonsági riasztások kezelésére és a riasztásokra való válaszadásra szolgáló funkcióinak használatában.

> [!NOTE]
> A speciális észlelések eléréséhez frissítsen az Azure Security Center Standard verzióra. A 60 napos próbaverzió ingyenes. A frissítéshez a [Biztonsági szabályzat](security-center-policies.md) beállításnál válassza ki a kívánt tarifacsomagot. További információkért lásd: [Az Azure Security Center díjszabása](security-center-pricing.md).
>
>

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?
A Security Center automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfalak és a végpontvédelmi megoldások naplóadatait a valós fenyegetések észlelése és a téves riasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.


> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, olvassa el [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md) című cikket.
>
>

## <a name="managing-security-alerts"></a>Biztonsági riasztások kezelése
A **Biztonsági riasztások** csempén áttekintheti az aktuális riasztásokat. Nyissa meg az Azure Portal webhelyet, és az egyes riasztásokkal kapcsolatos további részletek megjelenítéséhez kövesse az alábbi lépéseket:

1. A Security Center irányítópultján található a **Biztonsági riasztások** csempe.

    ![A Biztonsági riasztások csempe a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. A csempére kattintva nyissa meg a **Biztonsági riasztások** panelt, amely a lentiek szerint további információkat tartalmaz a riasztásról.

   ![A Biztonsági riasztások panel a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

A panel alsó részén találhatók az egyes riasztások részletei. Rendezésükhöz kattintson arra az oszlopra, amely szerint rendezni szeretné a riasztásokat. Az oszlopok meghatározása:

* **Leírás**: a riasztás rövid magyarázata.
* **Szám:** az adott típusú riasztások listája egy adott napra vonatkozóan.
* **Észlelte:** a riasztás kiváltásáért felelős szolgáltatás.
* **Dátum:** Az a dátum, amelyen az esemény történt.
* **Állapot:** A riasztás aktuális állapota. Kétféle állapot létezik:
  * **Aktív:** A biztonsági riasztást észlelték.
* **Súlyosság:** A súlyosság szintje lehet magas, közepes vagy alacsony.

### <a name="filtering-alerts"></a>A riasztások szűrése
A riasztások dátum, állapot és súlyosság alapján szűrhetők. A riasztások szűrése olyan esetekben lehet hasznos, amikor szűkíteni kell a megjelenített biztonsági riasztások körét. Például olyankor, ha az elmúlt 24 órában történt biztonsági riasztásokat szeretné kezelni, mert egy, a rendszerbe történő lehetséges behatolást vizsgál.

1. Kattintson a **Szűrő** lehetőségre a **Biztonsági riasztások** panelen. A **Szűrő** panel megnyílása után válassza ki azokat a dátumra, állapotra és súlyosságra vonatkozó értékeket, amelyeket meg kíván tekinteni.

    ![A riasztások szűrése a Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Válaszadás a biztonsági riasztásokra
Válasszon ki egy biztonsági riasztást, ha többet szeretne megtudni a riasztást kiváltó esemény(ek)ről, és arról, hogy milyen lépéseket kell tennie a támadás elhárítása érdekében. A biztonsági riasztások típus és dátum szerint vannak csoportosítva. Ha valamelyik biztonsági riasztásra kattint, megnyílik egy panel, amely a csoportosított riasztások listáját tartalmazza.

![Válaszadás a biztonsági riasztásokra az Azure Security Centerben](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

Ebben az esetben a riasztásokat gyanús RDP-tevékenység váltotta ki. Az első oszlopban a támadásnak kitett erőforrások jelennek meg, a másodikban az erőforrást érő támadások száma szerepel, a harmadikban a támadás időpontja látható, a negyedik a riasztás állapotát mutatja, az ötödik pedig a riasztás súlyosságát. Az információk áttekintése után kattintson a támadásnak kitett erőforrásra, és megjelenik egy új panel.

![Javaslatok az Azure Security Centerben: mi a teendő a biztonsági riasztással kapcsolatban](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

A panel **Leírás** mezőjében további információkat talál az eseményről. Ezek az információk áttekintést nyújtanak a biztonsági riasztás kiváltó okáról, a célerőforrásról, (adott esetben) a forrás IP-címéről, valamint a fenyegetés elhárítására vonatkozó javaslatokról.  Bizonyos esetekben a forrás IP-címe üres lesz (nem érhető el), mert a Windows nem minden biztonsági eseménynaplója tartalmazza az IP-címet.

A Security Center által javasolt elhárítási műveletek a biztonsági riasztástól függően különbözők lehetnek. Bizonyos esetekben előfordulhat, hogy az Azure egyéb funkcióit kell használnia a javasolt elhárítási művelet végrehajtásához. A példánkban a támadás elhárításának javasolt módja az, hogy a támadást végrehajtó IP-címet tiltólistára kell helyezni egy [hálózati hozzáférés-vezérlési lista](../virtual-network/virtual-networks-acl.md) vagy egy [hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md) típusú szabály használatával.

> [!NOTE]
> A különböző típusú riasztásokkal kapcsolatos további információkért olvassa el [Security Alerts by Type in Azure Security Center](security-center-alerts-type.md) (Az Azure Security Centerben használható biztonsági riasztások típus szerint) című cikket.
>
>

## <a name="see-also"></a>Lásd még:
Ebből a dokumentumból megismerte a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési funkciói](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

