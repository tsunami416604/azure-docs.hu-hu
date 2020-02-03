---
title: Az Apache Ambari elavult riasztások az Azure HDInsight
description: Az elavult Apache Ambari-riasztások lehetséges okainak és megoldásainak megvitatása és elemzése a HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f19d499b5e50fbb5030a0f396296eed46fc6eee3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722810"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Forgatókönyv: az Apache Ambari elavult riasztások az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felületén az alábbi képhez hasonló riasztás jelenhet meg:

![Apache Ambari elavult riasztási példa](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Ok

A Ambari-ügynökök folyamatosan végrehajtják az állapot-ellenőrzéseket a sok erőforrás állapotának figyeléséhez. Minden riasztás úgy van konfigurálva, hogy előre meghatározott időközönként fusson. Az egyes riasztások végrehajtása után a Ambari-ügynökök visszaküldik az állapotot a Ambari-kiszolgálónak. Ezen a ponton, ha a Ambari-kiszolgáló észleli, hogy a riasztások bármelyike nem futott le időben, akkor "Ambari Server-riasztásokat" indít el. Számos oka lehet annak, hogy az állapot-ellenőrzések miért nem hajthatók végre a meghatározott időközönként:

* Ha a gazdagépek nagy kihasználtsággal rendelkeznek (magas CPU), lehetősége van arra, hogy a Ambari-ügynök nem tudott elegendő rendszererőforrást lekérni a riasztások időben történő végrehajtásához.

* A fürt nagy terhelés esetén számos feladatot/szolgáltatást hajt végre.

* A fürt néhány gazdagépe számos összetevőt tartalmazhat, ezért számos riasztás futtatásához szükséges. Ha az összetevők száma nagy, lehetséges, hogy a riasztási feladatok nem tudják lemaradni az ütemezett időközöket

## <a name="resolution"></a>Megoldás:

### <a name="increase-alert-interval-time"></a>Riasztási időköz növelésének időpontja

Megadhatja, hogy egy adott riasztási időköz értékét a fürt válaszideje és a terhelése alapján növelje.

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban válassza a **Szerkesztés**lehetőséget.
1. Módosítsa az **ellenőrzési intervallum** értékét a kívánt értékre, majd válassza a **Mentés**lehetőséget.

### <a name="increase-alert-interval-time-for-ambari-server-alerts"></a>Ambari-kiszolgálói riasztások riasztási időközének növelési ideje

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. A **csoportok** legördülő listában válassza a **AMBARI alapértelmezett**lehetőséget.
1. Válassza a riasztás **Ambari-kiszolgáló riasztások**lehetőséget.
1. A definícióban válassza a **Szerkesztés**lehetőséget.
1. Módosítsa az **ellenőrzési intervallum** értékét igény szerint.
1. Módosítsa az **intervallum szorzó** értékét a kívánt értékre, majd válassza a **Mentés**lehetőséget.

### <a name="disable-and-enable-the-alert"></a>A riasztás letiltása és engedélyezése

Letilthatja, majd ismét engedélyezheti a riasztást az elavult riasztások elvetéséhez.

1. Az Apache Ambari felhasználói felületén válassza a **riasztások** fület.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban kattintson a jobb szélen található **engedélyezve** elemre.
1. A **megerősítő** előugró ablakban válassza a **Letiltás megerősítése**lehetőséget.
1. Várjon néhány másodpercet, amíg az oldalon látható összes riasztás "példánya" törlődik.
1. A definícióban kattintson a jobb szélen található **letiltott** elemre.
1. A **megerősítő** előugró ablakban válassza az **Engedélyezés megerősítése**lehetőséget.

### <a name="increase-alert-grace-time"></a>Riasztási türelmi idő javítása

Mielőtt a Ambari-ügynök jelentést készítsen arról, hogy egy konfigurált riasztás kihagyta az ütemtervét, türelmi idő van alkalmazva. Még akkor is, ha a riasztás kihagyta az ütemezett időpontot, de a riasztás türelmi ideje alatt aktiválva lett, az elavult riasztás nem következik be.

Az alapértelmezett `alert_grace_period` érték 5 másodperc. Ez a `alert_grace_period` beállítás `/etc/ambari-agent/conf/ambari-agent.ini`ban konfigurálható. Azokhoz a gazdagépekhez, amelyekről az elavult riasztásokat rendszeres időközönként elindítják, próbáljon meg 10 értékre emelni. Ezután indítsa újra a Ambari-ügynököt

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
