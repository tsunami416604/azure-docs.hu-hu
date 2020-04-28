---
title: Kivételt jelent az Apache Ambari kaptár nézet lekérdezésének futtatása az Azure HDInsight
description: Hibaelhárítási lépések Apache Hive lekérdezések futtatásakor az Apache Ambari kaptár nézetben az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895048"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Kivételt jelent az Apache Ambari kaptár nézet lekérdezésének futtatása az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Ha Apache Hive lekérdezést futtat az Apache Ambari kaptár nézetből, a következő hibaüzenet jelenik meg időnként:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Ok

Átjáró időtúllépése.

Az átjáró időtúllépési értéke 2 perc. A Ambari struktúra nézetből érkező lekérdezések a `/hive2` végponton keresztül lesznek elküldve az átjárón keresztül. A lekérdezés sikeres lefordítása és elfogadása után a HiveServer a értéket `queryid`adja vissza. Az ügyfelek ezután megtartják a lekérdezés állapotát. Ha a folyamat során a HiveServer nem ad vissza HTTP-választ 2 percen belül, a HDI-átjáró 502,3-es átjáró időtúllépési hibát jelez a hívónak. A hiba akkor fordulhat elő, ha a lekérdezés feldolgozása (nagyobb valószínűséggel) és a lekérési állapot hívásakor (kevésbé valószínű) történik. A felhasználók bármelyiket láthatják.

A http-kezelő szálának gyorsnak kell lennie: Készítse elő a `queryid`feladatot, és térjen vissza. Számos ok miatt azonban az összes kezelői szálat el lehet foglalni, ami időtúllépést okoz az új lekérdezésekhez és az állapotjelentések beszerzéséhez.

### <a name="responsibilities-of-the-http-handler-thread"></a>A HTTP-kezelő szálának feladatai

Amikor az ügyfél lekérdezést küld a HiveServer-nek, az előtér-szálban a következő műveleteket végzi el:

* A kérelem elemzése, szemantikai ellenőrzés
* Zárolás megszerzése
* Metaadattár-keresés szükség esetén
* A lekérdezés fordítása (DDL vagy DML)
* Lekérdezési terv előkészítése
* Engedélyezés végrehajtása (a megfelelő Ranger-házirendek futtatása a biztonságos fürtökben)

## <a name="resolution"></a>Megoldás:

Néhány általános javaslat a helyzet javítására:

* Ha külső kaptár metaadattár használ, ellenőrizze az adatbázis-metrikákat, és győződjön meg arról, hogy az adatbázis nincs túlterhelve. Érdemes lehet méretezni a metaadattár-adatbázis rétegét.

* Győződjön meg arról, hogy a Parallel Ops be van kapcsolva (Ez lehetővé teszi, hogy a HTTP-kezelő szálak párhuzamosan fussanak). Az érték ellenőrzéséhez indítsa el az [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) , és navigáljon a **kaptár** > -**konfigurációk** > **speciális** > **Egyéni struktúra-helyéhez**. A értékének `hive.server2.parallel.ops.in.session` a következőnek kell lennie `true`:.

* Győződjön meg arról, hogy a fürt virtuális gép SKU-jának nem túl kicsi a terheléshez. Érdemes lehet több fürt között felosztani a munkát. További információ: [fürt típusának kiválasztása](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Ha a Ranger telepítve van a fürtön, ellenőrizze, hogy van-e túl sok olyan Ranger-házirend, amelyet ki kell értékelni az egyes lekérdezésekhez. Ismétlődő vagy szükségtelen házirendek megkeresése.

* Győződjön meg arról, hogy a **HiveServer2 halom mérete** érték a Ambari. Navigáljon a **struktúra** > -**konfigurációk** > **beállításainak** > **optimalizálása**elemre. Győződjön meg arról, hogy az érték nagyobb, mint 10 GB. A teljesítmény optimalizálása érdekében szükség szerint módosítsa a teljesítményt.

* Győződjön meg arról, hogy a kaptár lekérdezése megfelelően van-e beállítva. További információ: [Apache Hive lekérdezések optimalizálása az Azure HDInsight-ben](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
