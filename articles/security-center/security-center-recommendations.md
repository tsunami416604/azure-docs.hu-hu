---
title: Biztonsági javaslatok az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum végigvezeti hogyan javaslatokat az Azure Security Center segítségével megvédheti Azure-erőforrásait, és legyen naprakész, megfelel a biztonsági szabályzatoknak.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064224"
---
# <a name="security-recommendations-in-azure-security-center"></a>Biztonsági javaslatok az Azure Security Centerben 
Ez a témakör ismerteti, hogyan lehet megtekinteni és megérteni a javaslatok az Azure Security Centerben az Azure-erőforrások védelmére.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  Ez a dokumentum nem tartalmaz lépésenkénti útmutatót.
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
Miután beállított egy biztonsági házirendet, a Security Center elemzi az Ön erőforrásainak állapotát, hogy észlelhesse a potenciális biztonsági réseket. A **javaslatok** alatt csempe **áttekintése** javaslatok a Security Center által azonosított teljes számát jeleníti meg.

![Security center – áttekintés](./media/security-center-recommendations/asc-overview.png)

1. Válassza ki a **csempére a javaslatok** alatt **áttekintése**. A **javaslatok** listát.
    
      ![Javaslatok megtekintése](./media/security-center-recommendations/view-recommendations.png)

    Javaslatok szűrheti. A javaslatok szűréséhez válasszon **szűrő** a a **javaslatok** panelen. A **szűrő** panel megnyílik, és szeretne látni a fontossága és állapota értékek választja.

   * **JAVASLATOK**: A javaslat.
   * **PONTSZÁM HATÁS BIZTONSÁGOS**: A Security Center a biztonsági javaslatok használatával, és fejlett algoritmusok alkalmazásával annak megállapításához, hogy kulcsfontosságú minden javaslat által generált pontszámot. További információkért lásd: [biztonságos pontszám számítási](security-center-secure-score.md#secure-score-calculation).
   * **ERŐFORRÁS**: Listázza az erőforrásokat, amelyekre ez a javaslat vonatkozik.
   * **ÁLLAPOTSORAIRÓL**:  Ismerteti, hogy az adott javaslat súlyosságát:
       * **Nagy (piros)** : Biztonsági rés fontos erőforrásnál (például egy alkalmazást, a virtuális gép vagy a hálózati biztonsági csoport) létezik, és beavatkozást igényel.
       * **Közepes (narancs)** : A biztonsági rés, és nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy ezt meg kell szüntetni.
       * **Alacsony (kék)** : A biztonsági rés, amelyek beavatkozást igényel, de nem igényel azonnali beavatkozást. (Alapértelmezés szerint alacsony súlyosságú javaslatok nem jelennek meg, de Ön is szűrővel bekapcsolhatja, ha meg szeretné tekinteni azokat.) 
       * **Kifogástalan (zöld)** :
       * **Nem érhető el (szürke)** :

1. Minden javaslat részleteinek megtekintéséhez kattintson a javaslatot.

    ![Javaslat részletei](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Lásd: [klasszikus és Resource Manager üzembe helyezési modellek](../azure-classic-rm.md) az Azure-erőforrásokhoz.
  
 ### <a name="apply-recommendations"></a>Javaslatok alkalmazása
> Az összes javaslatok áttekintése, után döntse el, melyiket érdemes először a alkalmazni. Azt javasoljuk, hogy használja a biztonságos pontozása hatás kiértékeléséhez, hogy mely ajánlások előbb alkalmazni kell.

1. A listából kattintson a javaslatot.
1. Kövesse az utasításokat a *javítási lépések* szakaszban.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megismerhette a Security Center biztonsági javaslatokat. A Security Centerrel kapcsolatos további tudnivalókért lásd a következő témaköröket:

* [Biztonsági szabályzatok beállítása az Azure Security Center](tutorial-security-policy.md) – ismerje meg, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és -erőforráscsoportok.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) – ismerje meg, hogyan kezelése és válaszadás a biztonsági riasztásokra.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center FAQ](security-center-faq.md) (Azure Security Center: Gyakran ismételt kérdések) – Válaszok a szolgáltatás használatára vonatkozó gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
