---
title: Mintaadatok az Azure HDInsight-struktúra tábláiban – csoportos adatelemzési folyamat
description: Az Azure HDInsight-struktúra tábláiban tárolt, struktúra-lekérdezéseket használó adatmennyiség, amely az adatmennyiséget az elemzéshez könnyebben kezelhető méretre csökkenti.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719993"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Adatmintavétel az Azure HDInsight Hive-táblákban
Ez a cikk azt ismerteti, hogyan lehet lekérdezni az Azure HDInsight-struktúra tábláiban tárolt adatmintákat a kaptár-lekérdezések használatával, hogy az elemzéshez könnyebben kezelhető méretre csökkentse. Három népszerű használatú mintavételi módszert foglal magában:

* Egységes véletlenszerű mintavétel
* Véletlenszerű mintavételezés csoportok szerint
* Rétegzett mintavételezés

**Miért érdemes felvenni az adatait?**
Ha az elemezni kívánt adatkészlet nagy méretű, általában egy jó ötlet, hogy lerövidítse az adatokat, hogy csökkentse azt kisebb, de reprezentatív és felügyelhető méretre. A leállási mintavételezés megkönnyíti az adatfelismerést, a feltárást és a szolgáltatások fejlesztését. A csapat adatelemzési folyamatának szerepe az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

## <a name="how-to-submit-hive-queries"></a>Struktúra-lekérdezések beküldése
A kaptár lekérdezéseit a Hadoop parancssori konzolról lehet elküldeni a Hadoop-fürt fő csomópontján.  Jelentkezzen be a Hadoop-fürt fő csomópontjára, nyissa meg a Hadoop parancssori konzolt, és küldje el innen a kaptár-lekérdezéseket. A kaptár-lekérdezések a Hadoop parancssori konzolon való elküldésével kapcsolatos utasításokért lásd: [a struktúra-lekérdezések elküldése](move-hive-tables.md#submit).

## <a name="uniform"></a>Egységes véletlenszerű mintavétel
Az egységes véletlenszerű mintavételezés azt jelenti, hogy az adatkészletben lévő minden egyes sor a mintavétel során egyenlő eséllyel szerepel. Ezt egy extra mező () a belső "Select" lekérdezésben megadott adatkészletbe, a külső "Select" lekérdezésen belül, az adott véletlenszerű mezőre való felvételével lehet megvalósítani.

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

Itt `<sample rate, 0-1>` megadja a rekordok azon hányadát, amelyet a felhasználók szeretne felvenni.

## <a name="group"></a>Véletlenszerű mintavételezés csoportok szerint
A kategorikus azonosítók mintavétele során érdemes lehet bevenni vagy kizárni az összes példányt a kategorikus változó egyes értékeinél. Ezt a fajta mintavételezést "mintavételezés csoportonként" nevezzük. Ha például egy "*State*" kategorikus változóval rendelkezik, amely olyan értékeket tartalmaz, mint például a NY, a ma, a CA, a NJ és a PA, akkor az egyes állapotokból származó rekordokat össze kell állítani, függetlenül attól, hogy mintát vesznek-e.

Íme egy példa a Group:

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

## <a name="stratified"></a>Rétegzett mintavételezés
A véletlenszerű mintavételezés egy kategorikus változóra vonatkozik, ha a beszerzett minták olyan kategorikus értékekkel rendelkeznek, amelyek ugyanabban az arányban találhatók, mint a szülő populációban. Ha ugyanezt a példát használja, tegyük fel, hogy az adatai a következő állapotok szerint jelennek meg: NJ 100 észrevételt tartalmaz, a NY 60 megfigyelésekkel rendelkezik, és a WA rendelkezik 300-megjegyzésekkel. Ha azt adja meg, hogy a rétegzett mintavételezés sebessége 0,5, akkor a beszerzett minta a NJ, NY és WA megközelítőleg 50, 30 és 150 megfigyeléssel rendelkezik.

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


További információ a kaptárban elérhető fejlettebb mintavételi módszerekről: [LanguageManual mintavétel](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

