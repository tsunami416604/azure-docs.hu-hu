---
title: Biztonsági javaslatok kezelése az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum végigvezeti hogyan javaslatokat az Azure Security Center segítségével megvédheti Azure-erőforrásait, és legyen naprakész, megfelel a biztonsági szabályzatoknak.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: 8535a77c9f44a4dafca34ef24414f90741a9133e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652730"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok kezelése az Azure Security Centerben
Ez a dokumentum végigvezeti javaslatok használatát az Azure Security Centerben az Azure-erőforrások védelmére.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="what-are-security-recommendations"></a>Mik azok a biztonsági javaslatok?
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

## <a name="implementing-security-recommendations"></a>Biztonsági javaslatok alkalmazása
### <a name="set-recommendations"></a>Set-javaslatok
A [biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md), fog megismerkedni:

* Biztonsági szabályzatok konfigurálása.
* Kapcsolja be az adatgyűjtést.
* Válassza ki, melyik javaslatok a biztonsági szabályzat részeként megtekintéséhez.

Aktuális javaslatok szabályzatközpontban Rendszerfrissítések, az alapkonfigurációs szabályok, a kártevőirtó programok, körül [hálózati biztonsági csoportok](../virtual-network/security-overview.md) alhálózatok és a hálózati adapterek, SQL database naplózási szolgáltatása, az SQL database transzparens adattitkosítás, és a webalkalmazás-tűzfalak.  [Biztonsági szabályzatok beállítása](tutorial-security-policy.md) minden javaslat beállítás leírását.

### <a name="monitor-recommendations"></a>A figyelő javaslatok
Miután beállított egy biztonsági házirendet, a Security Center elemzi az Ön erőforrásainak állapotát, hogy észlelhesse a potenciális biztonsági réseket. A **javaslatok** alatt csempe **áttekintése** lehetővé teszi, hogy ismeri a Security Center által azonosított javaslatok teljes száma.

![Javaslatok csempe][1]

Minden javaslat részleteinek megtekintéséhez válassza ki a **csempére a javaslatok** alatt **áttekintése**. **Javaslatok** nyílik meg.

![Szűrő javaslatok][2]

Javaslatok szűrheti. A javaslatok szűréséhez válasszon **szűrő** a a **javaslatok** panelen. A **szűrő** panel megnyílik, és szeretne látni a fontossága és állapota értékek választja.


* **JAVASLATOK**: A javaslat.
* **PONTSZÁM HATÁS BIZTONSÁGOS**:
* **ERŐFORRÁS**: Listázza az erőforrásokat, amelyekre ez a javaslat vonatkozik.
* **ÁLLAPOTSORAIRÓL**:  Ismerteti, hogy az adott javaslat súlyosságát:
   * **Nagy (piros)**: Biztonsági rés fontos erőforrásnál (például egy alkalmazást, a virtuális gép vagy a hálózati biztonsági csoport) létezik, és beavatkozást igényel.
   * **Közepes (narancs)**: A biztonsági rés, és nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy ezt meg kell szüntetni.
   * **Alacsony (kék)**: A biztonsági rés, amelyek beavatkozást igényel, de nem igényel azonnali beavatkozást. (Alapértelmezés szerint alacsony súlyosságú javaslatok nem jelennek meg, de Ön is szűrővel bekapcsolhatja, ha meg szeretné tekinteni azokat.) 
   * **Kifogástalan (zöld)**:
   * **Nem érhető el (szürke)**:
 <!-- * **DESCRIPTION**: Explains the recommendation and what needs to be done to address it.
**STATE**: Describes the current state of the recommendation:
   * **Open**: The recommendation hasn't been addressed yet.
    * **In Progress**: The recommendation is currently being applied to the resources, and no action is required by you.
   * **Resolved**: The recommendation has already been completed (in this case, the line is grayed out).
    -->

Referenciaként az alábbi táblázat segítségével segítenek megérteni a rendelkezésre álló ajánlásokat, és mindegyik funkciója alkalmazásuk esetén.
<!-- There is no table below -->
> [!NOTE]
> Érdemes tudni a [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
>
>
### <a name="apply-recommendations"></a>Javaslatok alkalmazása
Összes javaslat áttekintése után döntse el, amely egy alkalmazza először. Azt javasoljuk, hogy használja a besorolásával, mely ajánlások kiértékelheti, hogy a fő paraméter előbb alkalmazni kell.



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerhette a Security Center biztonsági javaslatokat. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
