---
title: Biztonsági események támogatási jegyének naplózása – Azure | Microsoft Docs
description: Ha az Azure piactéren értékesítőként egy potenciális biztonsági eseményt azonosítottak, tudnia kell, hogyan kell naplózni a megfelelő jegyet.
services: security
documentationcenter: na
author: DavidBosland
manager: lakoch
editor: v-dabosl
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2017
ms.author: barclayn
ms.openlocfilehash: 80ce352a9033b2da1db16643d07eab9eff70eb0a
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875682"
---
# <a name="how-to-log-a-security-event-support-ticket"></a>Biztonsági események támogatási jegyének naplózása

1. Navigáljon a [közzétevő támogatásához](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&ccsid=636450758943226673) , és jelentkezzen be a Microsoft hitelesítő adataival.
2. A probléma típusaként válassza a "biztonsági esemény" lehetőséget, és válassza a "biztonsági incidens" és a "sebezhetőség" kategóriát.

    ![Esemény típusa és definíciója][1]

3. A probléma típusának és kategóriájának kiválasztása után kattintson a**kérelem indítása**gombra. A probléma jobb megismeréséhez adja meg a következő információkat.

    i. Mi a probléma és/vagy a biztonsági rés?

    ii. Biztonsági rések esetén adja meg a következőt: CVE (mitre.org) vagy kitöltött CVSS3 https://www.first.org/cvss/calculator/3.0) v3 kalkulátor (.

    III. Van megoldás vagy mérséklés? Ha igen, adja meg a szervizelés lépéseit.

    IV. Van olyan üzenete, amelyet el szeretne küldeni az ügyfeleknek? Önnel együttműködve megfelelő üzenetet készítünk, ha van ilyen.

4. Beküldési megerősítés – ha elküldte a problémát, a rendszer egy munkanapon belül visszaigazolja a beérkezést, és prioritást és súlyosságot rendel a probléma megoldásához.

    - Ha kommunikálni szeretne velünk a problémával kapcsolatban, használja a megerősítő számot minden levelezésben.
    - Bármikor megtekintheti a probléma előrehaladását.

5. Mi történik a következő lépésekkel? A probléma és a súlyosság függvényében a következő lépések hajthatók végre:

    - Az értékelésünk eredményét tájékoztatjuk Önnek. Az eredménytől függően törölheti vagy megkérheti az ajánlat módosítását. Ebben az esetben Önnel együttműködve biztosítjuk, hogy az érintett ügyfelek megszakadását a lehető legkisebbre csökkentse.
    - Együttműködünk Önnel, hogy enyhítse az incidensek és a biztonsági rések hatását a kölcsönös ügyfeleink számára.


[1]: ./media/azure-security-event-support-ticket/chart.png
