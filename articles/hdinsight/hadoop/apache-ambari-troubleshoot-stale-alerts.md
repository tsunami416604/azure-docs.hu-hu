---
title: Az Apache Ambari elavult riasztások az Azure HDInsightban
description: Az Apache Ambari elavult riasztásainak lehetséges okainak és megoldásainak megvitatása és elemzése a HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f9dfcb930e3fe4f862f9f51ff00270d0eb0c66ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539110"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Eset: Apache Ambari elavult riasztások az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Az Apache Ambari felhasználói felületén az ilyen riasztás jelenhet meg:

![Például az Apache Ambari elavult riasztási](./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png)

## <a name="cause"></a>Ok

Az Ambari ügynökök folyamatosan figyelik számos erőforrás állapotát. *Riasztások* konfigurálható, hogy tájékoztassa, hogy bizonyos fürttulajdonságok belül vannak előre meghatározott küszöbértékeket. Minden erőforrás-ellenőrzés futtatása után, ha a riasztási feltétel teljesül, az Ambari ügynökök jelentik az állapotot az Ambari kiszolgálónak, és riasztást váltanak ki. Ha egy riasztás nincs ellenőrizve a riasztási profilban lévő időköznek megfelelően, a kiszolgáló *ambari kiszolgáló elavult riasztást* indít el.

Számos oka lehet annak, hogy az állapotfelmérés nem fut a megadott időközönként:

* A gazdagépek nagy használat (magas CPU-használat) alatt állnak, így az Ambari ügynök nem tud elegendő rendszererőforrást szerezni a riasztások időben történő futtatásához.

* A fürt sok feladat vagy szolgáltatás végrehajtása a nagy terhelés alatt.

* A fürt néhány állomása sok összetevőt üzemeltet, ezért sok riasztás futtatásához szükséges. Ha az összetevők száma nagy, a riasztási feladatok elmulaszthatják az ütemezett időközöket.

## <a name="resolution"></a>Megoldás:

Próbálkozzon az alábbi módszerekkel az Ambari elavult riasztásokkal kapcsolatos problémák megoldásához.

### <a name="increase-the-alert-interval-time"></a>A riasztási időköz növelése

Az egyes riasztási időközök értékét a fürt válaszideje és betöltése alapján növelheti:

1. Az Apache Ambari felhasználói felületén válassza a **Riasztások** lapot.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban válassza a **Szerkesztés**lehetőséget.
1. Növelje az **Ellenőrzési időköz** értékét, majd válassza a **Mentés gombot.**

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Az Ambari Server-riasztások riasztási időidejének növelése

1. Az Apache Ambari felhasználói felületén válassza a **Riasztások** lapot.
1. A **Csoportok** legördülő listában válassza az **AMBARI Alapértelmezett lehetőséget.**
1. Válassza ki az **Ambari Server Alerts riasztást.**
1. A definícióban válassza a **Szerkesztés**lehetőséget.
1. Növelje az **Ellenőrzési időköz** értékét.
1. Növelje az **Intervallumszorzó** értékét, majd válassza a **Mentés**lehetőséget.

### <a name="disable-and-reenable-the-alert"></a>A riasztás letiltása és újbóli engedélyezése

Elavult riasztás elvetéséhez tiltsa le, majd engedélyezze újra:

1. Az Apache Ambari felhasználói felületén válassza a **Riasztások** lapot.
1. Válassza ki a kívánt riasztási definíció nevét.
1. A definícióban válassza az **Engedélyezve lehetőséget** a felhasználói felület jobb szélső részén.
1. A **Megerősítés** előugró ablakban válassza a **Letiltás megerősítése**lehetőséget.
1. Várjon néhány másodpercet, amíg az oldalon megjelenő összes riasztási "példány" törlődik.
1. A definícióban válassza a **Letiltva** lehetőséget a felhasználói felület jobb szélső részén.
1. A **Megerősítés** előugró ablakban válassza az **Engedélyezés megerősítése**lehetőséget.

### <a name="increase-the-alert-grace-period"></a>A riasztási türelmi idő növelése

Van egy türelmi időszak, mielőtt egy Ambari ügynök jelenti, hogy egy konfigurált riasztás nem megfelelő ütemezését. Ha a riasztás nem fogadott el az ütemezett időpontot, de a türelmi időszakon belül futott, az elavult riasztás nem jön létre.

Az `alert_grace_period` alapértelmezett érték 5 másodperc. Ezt a beállítást az /etc/ambari-agent/conf/ambari-agent.ini fájlban állíthatja be. Azon állomások esetében, amelyeken az elavult riasztások rendszeres időközönként előfordulnak, próbálja meg az értéket 10-re növelni. Ezután indítsa újra az Ambari ügynököt.

## <a name="next-steps"></a>További lépések

Ha a problémát itt nem említették, vagy nem tudja megoldani, látogasson el az alábbi csatornák egyikébe további támogatásért:

* Válaszokat kaphat az Azure szakértőitől az [Azure community support szolgáltatásában.](https://azure.microsoft.com/support/community/)

* Kapcsolatba [@AzureSupport](https://twitter.com/azuresupport) a Twitter. Ez a hivatalos Microsoft Azure-fiók az ügyfélélmény javítása érdekében. Összeköti az Azure-közösséget a megfelelő erőforrásokkal: válaszokkal, támogatással és szakértőkkel.

* Ha további segítségre van szüksége, nyújtson be támogatási kérelmet az [Azure Portalon.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Az odavezetőhöz válassza a portál menü Súgó (**?**) parancsát, vagy nyissa meg a **Súgó + támogatás** ablaktáblát. További információ: [Azure-támogatási kérelem létrehozása.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) 

  Az előfizetés-kezelés és a számlázás támogatása a Microsoft Azure-előfizetés részét képezi. A technikai támogatás az [Azure támogatási csomagjain](https://azure.microsoft.com/support/plans/)keresztül érhető el.
