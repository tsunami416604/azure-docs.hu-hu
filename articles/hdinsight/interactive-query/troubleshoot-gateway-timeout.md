---
title: Kivétel az Apache Ambari Hive View szolgáltatásból az Azure HDInsightban történő futtatásakor
description: Hibaelhárítási lépések az Apache Hive-lekérdezések Apache Ambari Hive nézeten keresztül az Azure HDInsightban történő futtatásakor.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895048"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Kivétel az Apache Ambari Hive View szolgáltatásból az Azure HDInsightban történő futtatásakor

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Ha Apache Ambari Hive nézetből futtat egy Apache Hive-lekérdezést, időnként a következő hibaüzenet jelenik meg:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Ok

Átjáró időeltere.

Az átjáró időtúlértéke 2 perc. Az Ambari Hive nézetlekérdezései `/hive2` az átjárón keresztül kerülnek elküldésre a végpontra. A lekérdezés sikeres fordítása és elfogadása után a `queryid`HiveServer egy . Az ügyfelek ezután továbbra is lekérdezik a lekérdezés állapotát. A folyamat során, ha a HiveServer nem ad vissza HTTP-választ 2 percen belül, a HDI-átjáró 502.3 Átjáró időtúlírási hibát ad a hívónak. A hibák akkor fordulhatnak elő, ha a lekérdezés feldolgozásra kerül elküldésre (valószínűbb) és a get status call-ban is (kevésbé valószínű). A felhasználók bármelyiküket láthatták.

A http handler szál állítólag gyors: előkészíti `queryid`a feladatot, és visszatér a . Több okból is kifolyólag azonban az összes kezelőszál foglalt lehet, ami időkéréseket eredményezhet az új lekérdezések és a get status calls esetén.

### <a name="responsibilities-of-the-http-handler-thread"></a>A HTTP-kezelő szálának felelőssége

Amikor az ügyfél lekérdezést küld a HiveServernek, a következőműveleteket végzi az előtér-szálban:

* Elemezd a kérelmet, ne szemantikai ellenőrzés
* Zárolás beszerzése
* Metastore-keresése, ha szükséges
* A lekérdezés fordítása (DDL vagy DML)
* Lekérdezési terv előkészítése
* Engedélyezés végrehajtása (Az összes vonatkozó ranger-házirend futtatása biztonságos fürtökben)

## <a name="resolution"></a>Megoldás:

Néhány általános ajánlás a helyzet javítására:

* Ha egy külső struktúra metastore, ellenőrizze a DB metrikák, és győződjön meg arról, hogy az adatbázis nem túlterhelt. Fontolja meg a metatár-adatbázisréteg méretezését.

* Győződjön meg arról, hogy a párhuzamos műveletek be vannak kapcsolva (ez lehetővé teszi a HTTP-kezelő szálainak párhuzamos futtatását). Az érték ellenőrzéséhez indítsa el az [Apache Ambari-t,](../hdinsight-hadoop-manage-ambari.md) és keresse meg a **Hive** > **Configs** > **Advanced** > **Custom struktúrawebhelyet.** Az értéknek `hive.server2.parallel.ops.in.session` `true`a legyen .

* Győződjön meg arról, hogy a fürt virtuálisgép-termékváltozat nem túl kicsi a terheléshez. Fontolja meg a munka több fürt közötti felosztását. További információt a [Fürttípus kiválasztása](../hdinsight-capacity-planning.md#choose-a-cluster-type)című témakörben talál.

* Ha a Ranger telepítve van a fürtön, ellenőrizze, hogy túl sok Ranger-házirend van-e, amelyet minden egyes lekérdezéshez ki kell értékelni. Keressen ismétlődő vagy szükségtelen házirendeket.

* Ellenőrizze a **HiveServer2 halommemória-méret** értékét az Ambari-ból. Keresse meg a Hive Configs Settings Optimization **(Hive** > **Configs** > Settings**Optimization)****lapot.** >  Győződjön meg arról, hogy az érték nagyobb, mint 10 GB. A teljesítmény optimalizálása érdekében szükség szerint módosíthatja.

* Győződjön meg arról, hogy a Hive-lekérdezés jól van hangolva. További információ: [Apache Hive-lekérdezések optimalizálása az Azure HDInsightban.](../hdinsight-hadoop-optimize-hive-query.md)

## <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében. Az Azure-közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
