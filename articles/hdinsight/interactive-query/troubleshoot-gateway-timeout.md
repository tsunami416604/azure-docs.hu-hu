---
title: Kivételt jelent az Apache Ambari kaptár nézet lekérdezésének futtatása az Azure HDInsight
description: Hibaelhárítási lépések Apache Hive lekérdezések futtatásakor az Apache Ambari kaptár nézetben az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 0422cb3e7f04363f0fb3a3706e36e1d465ffd6f8
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553279"
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

Az átjáró időtúllépési értéke 2 perc. A rendszer az átjárón keresztül küldi el az `/hive2` végpontnak küldött lekérdezéseket a Ambari-struktúra nézetből. A lekérdezés sikeres lefordítása és elfogadása után a HiveServer egy `queryid`ad vissza. Az ügyfelek ezután megtartják a lekérdezés állapotát. Ha a folyamat során a HiveServer nem ad vissza HTTP-választ 2 percen belül, a HDI-átjáró 502,3-es átjáró időtúllépési hibát jelez a hívónak. A hiba akkor fordulhat elő, ha a lekérdezés feldolgozása (nagyobb valószínűséggel) és a lekérési állapot hívásakor (kevésbé valószínű) történik. A felhasználók bármelyiket láthatják.

A http-kezelő szálának gyorsnak kell lennie: Készítse elő a feladatot, és térjen vissza egy `queryid`. Számos ok miatt azonban az összes kezelői szálat el lehet foglalni, ami időtúllépést okoz az új lekérdezésekhez és az állapotjelentések beszerzéséhez.

### <a name="responsibilities-of-the-http-handler-thread"></a>A HTTP-kezelő szálának feladatai

Amikor az ügyfél lekérdezést küld a HiveServer-nek, az előtér-szálban a következő műveleteket végzi el:

* A kérelem elemzése, szemantikai ellenőrzés
* Zárolás megszerzése
* Metaadattár-keresés szükség esetén
* A lekérdezés fordítása (DDL vagy DML)
* Lekérdezési terv előkészítése
* Engedélyezés végrehajtása (a megfelelő Ranger-házirendek futtatása a biztonságos fürtökben)

## <a name="resolution"></a>Felbontás

Néhány általános javaslat a helyzet javítására:

* Ha külső kaptár metaadattár használ, ellenőrizze az adatbázis-metrikákat, és győződjön meg arról, hogy az adatbázis nincs túlterhelve. Érdemes lehet méretezni a metaadattár-adatbázis rétegét.

* Győződjön meg arról, hogy a Parallel Ops be van kapcsolva (Ez lehetővé teszi, hogy a HTTP-kezelő szálak párhuzamosan fussanak). Az érték ellenőrzéséhez indítsa el az [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) , és navigáljon a **kaptár** > **konfigurációkhoz** > **Advanced** > **Custom kaptár-site**. `hive.server2.parallel.ops.in.session` értékének `true`nak kell lennie.

* Győződjön meg arról, hogy a fürt virtuális gép SKU-jának nem túl kicsi a terheléshez. Érdemes lehet több fürt között felosztani a munkát. További információ: [fürt típusának kiválasztása](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Ha a Ranger telepítve van a fürtön, ellenőrizze, hogy van-e túl sok olyan Ranger-házirend, amelyet ki kell értékelni az egyes lekérdezésekhez. Ismétlődő vagy szükségtelen házirendek megkeresése.

* Győződjön meg arról, hogy a **HiveServer2 halom mérete** érték a Ambari. Navigáljon a **kaptár** > **konfigurációk** > **Beállítások** > **optimalizálás**lehetőségre. Győződjön meg arról, hogy az érték nagyobb, mint 10 GB. A teljesítmény optimalizálása érdekében szükség szerint módosítsa a teljesítményt.

* Győződjön meg arról, hogy a kaptár lekérdezése megfelelően van-e beállítva. További információ: [Apache Hive lekérdezések optimalizálása az Azure HDInsight-ben](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.
