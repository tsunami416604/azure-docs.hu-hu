---
title: Mintaadatok az Azure HDInsight Hive-táblákban – Csapatadatelemzési folyamat
description: Az Azure HDInsight Hive-táblákban tárolt, a Hive-lekérdezések használatával az adatok elemzésre jobban kezelhető méretére csökkenthető késedelmi adatok.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719993"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Adatmintavétel az Azure HDInsight Hive-táblákban
Ez a cikk ismerteti, hogyan csökkentheti az Azure HDInsight Hive-táblákban tárolt adatok at Hive-lekérdezések használatával, hogy csökkentse azt egy méret jobban kezelhető elemzésre. Három, népszerűen használt mintavételi módszert foglal magában:

* Egységes szúrópróbaszerű mintavétel
* Véletlenszerű mintavétel csoportok szerint
* Rétegzett mintavétel

**Miért érdemes mintát venni az adataiból?**
Ha az elemezni kívánt adatkészlet nagy, általában érdemes levenni az adatokat, hogy csökkentse azokat egy kisebb, de reprezentatívabb és kezelhetőbb méretre. A lemintavételezés megkönnyíti az adatok megértését, feltárását és a szolgáltatástervezést. A csapat adatelemzési folyamatának szerepe az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípus-készítése.

Ez a mintavételezési feladat egy lépés a [csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="how-to-submit-hive-queries"></a>Hive-lekérdezések küldése
Hive-lekérdezések küldhetők a Hadoop parancssori konzolról a Hadoop-fürt főcsomópontján.  Jelentkezzen be a Hadoop-fürt fő csomópontjába, nyissa meg a Hadoop parancssori konzolt, és küldje el onnan a Hive-lekérdezéseket. A Hive-lekérdezések Hadoop parancssori konzolon történő elküldésével kapcsolatos tudnivalókért olvassa [el a Hive-lekérdezések küldése című témakört.](move-hive-tables.md#submit)

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Egységes szúrópróbaszerű mintavétel
Az egységes véletlenszerű mintavétel azt jelenti, hogy az adatkészlet minden egyes sorának egyenlő esélye van a mintavételre. Ez úgy valósítható meg, hogy egy további mező rand() a belső "select" lekérdezés adatkészletéhez, és a külső "select" lekérdezés, hogy a feltétel, hogy a véletlen mezőben.

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

Itt `<sample rate, 0-1>` adja meg a felhasználók által mintát venni kívánt rekordok arányát.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Véletlenszerű mintavétel csoportok szerint
Kategorikus adatok mintavételezésekor érdemes lehet vagy a kategorikus változó bizonyos értékének összes példányát. Ezt a fajta mintavételt "csoportonkénti mintavételnek" nevezik. Ha például van egy "*State*" kategorikus változója, amely például NY, MA, CA, NJ és PA értékekkel rendelkezik, azt szeretné, hogy az egyes államok rekordjai együtt legyenek, függetlenül attól, hogy mintavételre kerülnek-e vagy sem.

Íme egy példa lekérdezés, amely csoportonként mintát vesz:

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

## <a name="stratified-sampling"></a><a name="stratified"></a>Rétegzett mintavétel
A véletlenszerű mintavételt egy kategorikus változó tekintetében rétegezik, ha a kapott minták kategorikus értékei ugyanolyan arányban vannak jelen, mint a szülő populációban. Ugyanazt a példát használva, mint fent, tegyük fel, hogy az adatok a következő megfigyelések államok: NJ 100 megfigyelések, NY 60 megfigyelések, és wa már 300 megfigyelések. Ha a rétegzett mintavétel sebességét 0,5-re adja meg, akkor a kapott mintának körülbelül 50, 30 és 150 megfigyeléssel kell rendelkeznie NJ, NY, illetve WA tekintetében.

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


A Hive-ban elérhető speciális mintavételi módszerekről a [LanguageManual mintavételezéscímű](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)témakörben olvashat.

