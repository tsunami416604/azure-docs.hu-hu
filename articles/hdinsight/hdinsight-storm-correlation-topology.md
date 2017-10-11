---
title: "A Storm és HBase a HDInsight időbeli összefüggésbe események"
description: "Útmutató a HDInsight alatt futó Storm és HBase használatával különböző időpontokban érkező események összefüggéseket."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 17d11479-2d02-4790-8d29-05fb38351479
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/07/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 06630096383601e48e8f69f8553314cee42f5f3e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="correlate-events-that-arrive-at-different-times-using-storm-and-hbase"></a>Az eseményeket, amelyek különböző időpontokban érkeznek összefüggéseket Storm és HBase használatával

Apache Storm egy állandó adattár használatával hozhatók adatok bejegyzéseit, amelyek a kiszolgálófarmban lévő különböző időpontokban. Például létrehozhatja a bejelentkezési és kijelentkezési események kiszámításához, hogy mennyi ideig tartott a a munkamenet felhasználói munkamenet.

Ebből a dokumentumból megismerheti, hogyan egy alapszintű C# Storm-topológia, amely nyomon követi a bejelentkezési és kijelentkezési események a felhasználói munkamenetekben, és kiszámítja az időtartamot, a munkamenet létrehozásához. A topológia állandó adattárként HBase eszközt használja. HBase is lehetővé teszi a korábbi adatok létrehozásához további betekintést nyerjen a kötegelt lekérdezések végrehajtásához. Például hány felhasználói munkamenetek lettek elindítva, vagy egy adott időszakra vonatkozóan befejeződött.

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studio és a HDInsight tools for Visual Studio. További információkért lásd: [első lépései a HDInsight tools for Visual Studio használatával](hdinsight-hadoop-visual-studio-tools-get-started.md).

* A HDInsight alatt futó Apache Storm a fürt (Windows-alapú).

  > [!WARNING]
  > SCP.NET topológiák támogatottak a Linux-alapú Storm-fürtök létrehozása után 10/28/2016, a HBase SDK elérhető 10/28/2016 .NET-csomag nem működik megfelelően a Linux-alapú HDInsight-on

* Apache HBase on HDInsight-fürt (Linux vagy a Windows-alapú).

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Java](https://java.com) 1.7, vagy annál nagyobb a fejlesztési környezetet. Java csomag a topológia a HDInsight-fürthöz elküldésekor szolgál.

  * A **JAVA_HOME** környezeti változó Java tartalmazó könyvtárat kell mutatnia.
  * A **%JAVA_HOME%/bin** könyvtárnak az elérési úton kell lennie.

## <a name="architecture"></a>Architektúra

![Az adatfolyam keresztül a topológia ábrája](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

A forrás egy közös azonosító adatok események igényel. For example egy felhasználói Azonosítót, munkamenet-azonosító, vagy egyéb adat, amely egyedi a), valamint b) megtalálható a Storm küldött minden adathoz. Ez a példa egy GUID-érték képviseli egy munkamenet-azonosítót.

Ebben a példában két HDInsight-fürtök áll:

* HBase: állandó adattár korábbi adatok
* Storm: a bejövő adatok használt

Az adatok véletlenszerűen generálja a Storm-topológia, és az alábbi elemeket tartalmazza:

* Munkamenet-azonosító: egy GUID, amely egyedileg azonosítja mindegyik munkamenethez
* Esemény: egy kezdeti vagy záró esemény. Ebben a példában START mindig várakozni vége előtt
* Idő: az esemény időpontja.

Ezek az adatok feldolgozása és HBase tárolja.

### <a name="storm-topology"></a>Storm-topológia

A munkamenet akkor kezdődik, amikor egy **START** esemény a topológia által fogadott és a HBase a rendszer naplózza. Ha egy **END** esemény érkezik, a topológia lekéri a **START** esemény, és kiszámítja a két esemény között eltelt idő. Ez **időtartam** érték aztán a HBase, valamint a **END** események adatait.

> [!IMPORTANT]
> Ebben a topológiában az alapvető minta mutatja be, amíg egy éles megoldást kell vennie a kialakítás az alábbi helyzetekben:
>
> * Sorrendje nem érkező események
> * Ismétlődő esemény
> * Az eldobott események

A minta topológia a következő összetevőkből áll:

* Session.cs: hozzon létre egy véletlenszerű munkamenet-Azonosítót, kezdési időt és mennyi ideig tart a munkamenet felhasználói munkamenet szimulálja.

* Spout.cs: 100 munkamenetek hoz létre, megfelelően kibocsát egy indítási esemény, véletlenszerű időkorlát megvárja, mindegyik munkamenethez, és majd bocsát ki a záró eseményt. Újrahasznosítja azt majd újakat létrehozni munkamenet véget ért.

* HBaseLookupBolt.cs: a munkamenet-Azonosítót használja a munkamenet-információk a HBase kereséséhez. ZÁRÓ eseményt dolgoz fel, ha a megfelelő KEZDŐ esemény megkeresése, és kiszámítja a munkamenet időtartama alatt.

* HBaseBolt.cs: HBase adatait tárolja.

* TypeHelper.cs: Elősegíti a típuskonverziós olvasása / írása HBase.

### <a name="hbase-schema"></a>A HBase séma

Az adatok a HBase, a következő séma beállításokat tartalmazó tábla tárolja:

* Sorkulcsa: a munkamenet-Azonosítót az ebben a táblázatban szereplő sorok kulcsként használja.

* Oszlop család: a családi név megadása "cf". A családban tárolt oszlopok a következők:

  * esemény: KEZDŐ vagy záró.

  * idő: az idő, ezredmásodpercben, amelyet az esemény történt.

  * időtartam: közötti KEZDÉSI és BEFEJEZÉSI esemény.

* VERZIÓK: a "cf" termékcsalád tartsa meg az egyes sorok 5 verziók értéke.

  > [!NOTE]
  > Azok az egy adott sortól kulcs tárolt előző értékek naplózása verziók. A HBase alapértelmezés szerint csak a legfrissebb sor értékét adja vissza. Ebben az esetben a ugyanabban a sorban az összes esemény (KEZDŐ, záró.) a timestamp érték azonosít egy sor minden verziója szolgál. Verziók, adjon meg egy egyedi azonosítót. a naplózott események ügyfélállapotainak

## <a name="download-the-project"></a>A projekt letöltése

A minta projekt letölthető [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Ez a letöltés a következő C# projektet tartalmaz:

* CorrelationTopology: C# Storm-topológia, hogy véletlenszerűen bocsát ki a kezdő és záró események a felhasználói munkamenetekben. Minden munkamenet érvényes, 1 és 5 perc között.

* SessionInfo: C# konzolalkalmazást, amely a HBase táblát hoz létre, és információt szolgáltat tárolt munkamenetadatok mintalekérdezéseket biztosít.

## <a name="create-the-table"></a>A tábla létrehozása

1. Nyissa meg a **SessionInfo** projektre a Visual Studióban.

2. A **Megoldáskezelőben**, kattintson a jobb gombbal a **SessionInfo** projektre, és válassza ki **tulajdonságok**.

    ![A kijelölt tulajdonság menü képernyőképe](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Válassza ki **beállítások**, majd állítsa be a következő értékeket:

   * HBaseClusterURL: az URL-címe a HBase-fürtöt. Például https://myhbasecluster.azurehdinsight.net.

   * HBaseClusterUserName: a felügyelet/HTTP felhasználói fiók a fürt

   * HBaseClusterPassword: a rendszergazda/HTTP felhasználói fiók jelszavát

   * HBaseTableName: Ez a példa használata a tábla neve

   * HBaseTableColumnFamily: A családja oszlopnév

     ![Beállítások párbeszédpanel képe](./media/hdinsight-storm-correlation-topology/settings.png)

4. Futtassa a megoldást. Amikor a rendszer kéri, válassza ki a "c" kulcsot, a HBase-fürtöt a tábla létrehozásához.

## <a name="build-and-deploy-the-storm-topology"></a>Hozza létre, és a Storm-topológia központi telepítéséhez

1. Nyissa meg a **CorrelationTopology** a Visual Studio megoldás.

2. A **Megoldáskezelőben**, kattintson a jobb gombbal a **CorrelationTopology** projektre, és válassza a tulajdonságok.

3. Válassza a Tulajdonságok ablak **beállítások** , és írja be a konfigurációs értékeket ebben a projektben. Az első 5 rendszer által használt ugyanazokat az értékeket a **SessionInfo** projekt:

   * HBaseClusterURL: az URL-címe a HBase-fürtöt. Például https://myhbasecluster.azurehdinsight.net.

   * HBaseClusterUserName: a felügyelet/HTTP felhasználói fiók a fürt számára.

   * HBaseClusterPassword: a felügyelet/HTTP felhasználói fiók jelszavát.

   * HBaseTableName: használata ebben a példában a tábla neve. Ez az érték a tábla neve megegyezik a SessionInfo projektben használt.

   * HBaseTableColumnFamily: Az oszlop csomagcsalád nevét. Ez az érték oszlop család neve megegyezik a SessionInfo projektben használt.

   > [!IMPORTANT]
   > Nem módosítható a HBaseTableColumnNames, mert az azok által használt nevek **SessionInfo** beolvasni az adatokat.

4. A tulajdonságok mentéséhez, majd a projekt felépítéséhez.

5. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **Submit a HDInsight alatt futó Storm**. Ha a rendszer kéri, adja meg a hitelesítő adatait az Azure-előfizetéshez.

   ![A küldés alatt futó storm menüelemre képe](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6. Az a **nyújt topológia** párbeszédpanelen válassza ki a kívánt Storm-fürt központi telepítése a topológiát.

   > [!NOTE]
   > Az első alkalommal, amikor elküld a topológia eltarthat néhány másodpercet a HDInsight-fürtök nevének beolvasása.

7. Amennyiben a topológia feltöltött, és elküldi a fürt a **Storm-topológia nézet** megnyílik, és megjeleníti a futó topológiát. Az adatok frissítéséhez válassza ki a **CorrelationTopology** , és használja a frissítés gombra az oldal tetején sarkában a lap.

   ![A topológia e nézetében képe](./media/hdinsight-storm-correlation-topology/topologyview.png)

   Ha a topológia kezdődik, ami adatokat, az értéket a **Emitted** oszlop növekmények esetén.

   > [!NOTE]
   > Ha a **Storm-topológia nézet** nem automatikusan, nyissa meg a következő lépések segítségével:
   >
   > 1. A **Megoldáskezelőben**, bontsa ki a **Azure**, majd bontsa ki a **HDInsight**.
   > 2. Kattintson a jobb gombbal a topológia a futó Storm-fürt, majd válassza ki **nézet Storm-topológiák**

## <a name="query-the-data"></a>Az adatok lekérdezése

Amennyiben az adatok kibocsátott tegye a következőket az adatok lekérdezéséhez.

1. Lépjen vissza a **SessionInfo** projekt. Ha nem fut, indítsa el egy új példányát.

2. Amikor a rendszer kéri, válassza ki a **s** START esemény kereséséhez. Adjon meg egy időtartományt - meghatározásához kezdési és befejezési időt kéri ezek kétszer csak események adja vissza.

    A következő formátumot használja, ha a kezdő és záró időpont megadása: óó: PP és "vagyok" vagy "pm". Ha például 23:20 óra.

    Naplózott események visszaállításához használja a kezdő időpont előtt a Storm-topológia telepítve lett, és most a befejezési ideje. A visszatérési adatai az alábbihoz hasonló bejegyzést tartalmaz:

        Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

ZÁRÓ eseményeket keres ugyanúgy működik, mint START események. Azt jelzi, hogy az indítási esemény után 1 és 5 perc között véletlenszerűen jönnek-azonban záró eseményeket. Előfordulhat, hogy néhány időtartományhoz segítségével megkeresheti a záró kipróbálásához. ZÁRÓ eseményeket is tartalmazza az a munkamenet időtartama alatt - a KEZDÉSI időpontnál és befejezésének eseményt közötti különbség. Itt látható egy példa az END eseményekre vonatkozó adatokat:

    Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [!NOTE]
> Amíg megadott idő értékek nem a helyi idő, az a lekérdezés által visszaadott ideje UTC formátumban.

## <a name="stop-the-topology"></a>A topológia leállítása

Ha készen áll a topológia leállítása, térjen vissza a **CorrelationTopology** projektre a Visual Studióban. Az a **Storm-topológia nézet**, válassza ki a topológiát, majd a **Kill** gomb a topológia e nézetében tetején.

## <a name="delete-your-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Következő lépések

További Storm, tekintse meg a [a HDInsight alatt futó Storm](hdinsight-storm-example-topology.md).
