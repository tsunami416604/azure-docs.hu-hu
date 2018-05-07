---
title: Az adatokat az Azure HDInsight Hive táblák |} Microsoft Docs
description: Lefelé (Hadopop) Azure HDInsight Hive táblák az adatok mintavétele
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: b40aae9d494f3e7ebeae56fcad48f0ff47798bbc
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Adatmintavétel az Azure HDInsight Hive-táblákban
Ez a cikk ismerteti lefelé-minta még kezelhetőbbé teszik elemzéshez méretre csökkenteni a Hive-lekérdezésekkel Azure HDInsight Hive táblákban tárolt adatokat. Azt a három popularly használt mintavételi módszerek terjed ki:

* Egységes véletlenszerű mintavétel
* A csoportok véletlenszerű mintavétel
* Rétegzett mintavétel

A következő **menü** az adatokat a különböző tárolási környezetekben módját leíró témakörök hivatkozásait.

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Miért érdemes az az adatokat?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, akkor általában down kétmintás az adatokat, hogy az kisebb, de reprezentatív és könnyebben kezelhető méretű jó ötlet. Lefelé mintavételi elősegíti a adatok ismertetése, feltárása és a szolgáltatás mérnöki csapathoz. A szerepkör az adatok tudományos folyamatban az adatok feldolgozása funkciók és a gépi tanulási modellek gyors prototípusának engedélyezéséhez.

Ez a mintavételi feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Hogyan lehet elküldeni a Hive-lekérdezések
A parancssori Hadoop-konzolon, a Hadoop-fürt átjárócsomópontjához a Hive-lekérdezések küldheti el. Ehhez jelentkezzen be a Hadoop-fürt átjárócsomópontjához, nyissa meg a Hadoop parancssori konzolt, és küldje el a Hive-lekérdezések ott. A Hadoop parancssori konzolon Hive-lekérdezések elküldése, lásd: [hogyan küldhetnek Hive-lekérdezések](move-hive-tables.md#submit).

## <a name="uniform"></a> Egységes véletlenszerű mintavétel
Egységes véletlenszerű mintavételi jelenti, hogy az adatkészlet egyes soraihoz alatt mintát egyenlő esélyét. Ad hozzá egy kiegészítő mezőt rand() az adatkészletben a belső "select" lekérdezési, és a külső "select" lekérdezési feltételhez véletlenszerű mező hajtható végre.

Itt láthat egy példalekérdezést:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Itt `<sample rate, 0-1>` azt jelzi, hogy a felhasználók mintát szeretne arányát határozza meg.

## <a name="group"></a> A csoportok véletlenszerű mintavétel
Ha mintavételi kategorikus adatok, érdemes lehet akár vagy kizárja a minden egyes kategorikus változó értéke a előfordulása. Az ilyen mintavételi "mintavételi csoport" nevezik. Például, ha egy kategorikus változó "*állapot*", például NY, MA, CA, NJ és PA értékeket tartalmaz, minden állapotot együtt, hogy azok lekérdező vagy nem származó rekordokat szeretne.

Íme példalekérdezést adott minták csoport szerint:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Rétegzett mintavétel
Véletlenszerű mintavételi műanyaggal a rétegezett kategorikus változó tekintetében, ha a kapott minták kategorikus értékek, amelyek egy-egy található, mint a szülő feltöltése a. Az előző példát a fenti, tegyük fel, hogy az adatok a következő megfigyelések rendelkezik államok: NJ van 100 megfigyelések NY van 60 megfigyelések, és WA 300 megfigyelések rendelkezik. Ha 0,5 kell rétegzett mintavételi arány ad meg, majd a mintát a kell rendelkeznie körülbelül 50, 30 és 150 megfigyelések NJ, NY és WA rendre.

Itt láthat egy példalekérdezést:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Információ a speciális mintavételi felhasználható módszerek közül a Hive: [LanguageManual mintavételi](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

