---
title: Adatfolyam-grafikonok
description: Az adat-előállító adatfolyam-diagramok hogyan működik
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605358"
---
# <a name="mapping-data-flow-graphs"></a>Adatfolyam-diagramok leképezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A leképezési adatfolyamok tervezési felülete egy "építési" felület, ahol az adatfolyamokat felülről lefelé, balról jobbra építheti. Minden átalakításhoz egy plusz (+) szimbólummal ellátott eszköztár van csatolva. Koncentráljon az üzleti logikára ahelyett, hogy a csomópontokat éleken keresztül csatlakoztatna egy szabad formájú DAG környezetben.

Az alábbiakban beépített mechanizmusok az adatfolyam-diagram kezelésére.

## <a name="move-nodes"></a>Csomópontok áthelyezése

![Összesítő átalakítási beállítások](media/data-flow/agghead.png "aggregátor fejléce")

A húzási paradigma nélkül az átalakítási csomópont "mozgatásának" módja a bejövő adatfolyam módosítása. Ehelyett a "bejövő adatfolyam" módosításával mozgatja az átalakításokat.

## <a name="streams-of-data-inside-of-data-flow"></a>Adatfolyamok az adatfolyamon belül

Az Azure Data Factory Data Flow-ban az adatfolyamok az adatfolyamot jelölik. Az átalakítási beállítások ablaktáblán megjelenik egy "Bejövő adatfolyam" mező. Ez jelzi, hogy melyik bejövő adatfolyam táplálja az átalakítást. Az átalakító csomópont fizikai helyét a grafikonon úgy módosíthatja, hogy a Bejövő adatfolyam nevére kattint, és kijelöl egy másik adatfolyamot. Az aktuális átalakítás az adatfolyam on-val együtt az új helyre kerül.

Ha egy átalakítást egy vagy több átalakítással helyez át utána, akkor az adatfolyam új helye egy új ágon keresztül lesz összekapcsolva.

Ha a kijelölt csomópont után nincsenek további átalakítások, akkor csak az átalakítás kerül az új helyre.

## <a name="hide-graph-and-show-graph"></a>Diagram és diagram megjelenítése

Az alsó konfigurációs ablaktábla jobb szélén található egy gomb, ahol az alsó ablaktáblát teljes képernyőre bonthatja, amikor átalakítási konfigurációkon dolgozik. Ez lehetővé teszi, hogy az "előző" és a "következő" gombokat használja a grafikon konfigurációiban való navigáláshoz. Ha vissza szeretne térni a grafikonnézethez, kattintson a lefelé gombra, és térjen vissza az osztott képernyőre.

## <a name="search-graph"></a>Keresési grafikon

A diagramon a tervezőfelületen lévő keresés gombbal kereshet.

![Keresés](media/data-flow/search001.png "Keresési grafikon")

## <a name="next-steps"></a>További lépések

Az adatfolyam-tervezés befejezése után kapcsolja be a hibakeresési gombot, és tesztelje hibakeresési módban, akár közvetlenül az [adatfolyam-tervezőben,](concepts-data-flow-debug-mode.md) akár a [folyamathiba-keresésben.](control-flow-execute-data-flow-activity.md)
