---
title: Adatfolyam-diagramok
description: A adatáramlási diagramok használata
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/04/2019
ms.openlocfilehash: 0d357c4c671070a5c5e9d4587e2f90b6628996f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605358"
---
# <a name="mapping-data-flow-graphs"></a>Adatfolyam-diagramok leképezése

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A leképezési adatfolyamatok tervezési felülete egy "építési" felület, amelyben felülről lefelé, balról jobbra hozhat létre adatfolyamokat. Az egyes átalakításokhoz egy pluszjel (+) szimbólum van csatolva. Koncentráljon az üzleti logikára ahelyett, hogy a csomópontokat a szabad formátumú DAG-környezet szélein keresztül csatlakoztatja.

Az alábbi beépített mechanizmusokkal felügyelheti az adatfolyam gráfját.

## <a name="move-nodes"></a>Csomópontok áthelyezése

![Összesített átalakítási beállítások](media/data-flow/agghead.png "összesítő fejléc")

A fogd és vidd típusú paradigma nélkül az átalakítási csomópontok áthelyezésének módja a bejövő adatfolyam módosítása. Ehelyett a "bejövő adatfolyam" módosításával áthelyezi az átalakításokat.

## <a name="streams-of-data-inside-of-data-flow"></a>Adatfolyamban tárolt adatstreamek

Azure Data Factory adatfolyamban a streamek az adatáramlást jelölik. Az átalakítási beállítások ablaktáblán egy "bejövő adatfolyam" mező jelenik meg. Ez azt mutatja be, hogy mely bejövő adatfolyam táplálja az átalakítást. Az átalakítási csomópont fizikai helyét a diagramon módosíthatja úgy, hogy a bejövő adatfolyam nevére kattint, és kiválasztja egy másik adatfolyamot. A jelenlegi átalakítás az adott adatfolyamon végzett összes további átalakítással együtt az új helyre kerül.

Ha az átalakítást egy vagy több átalakítással helyezi át, a folyamat során az új hely egy új ág használatával lesz csatlakoztatva.

Ha a kiválasztott csomópont után nem végez további átalakítást, akkor csak az adott átalakítás fog áttérni az új helyre.

## <a name="hide-graph-and-show-graph"></a>Gráf elrejtése és grafikon megjelenítése

Az alsó konfigurációs ablaktábla jobb felső sarkában egy gomb látható, ahol az átalakítási konfigurációkon végzett munka során kiterjesztheti az alsó panelt teljes képernyős megjelenítésre. Ez lehetővé teszi, hogy az "előző" és a "Next" gombokkal navigáljon a gráf konfigurációjában. A diagram nézetre való visszalépéshez kattintson a lefelé gombra, és térjen vissza a felosztott képernyőhöz.

## <a name="search-graph"></a>Keresési gráf

A diagramon a tervezési felületen található Keresés gombra kattintva keresheti meg a diagramot.

![Keresés](media/data-flow/search001.png "Keresési gráf")

## <a name="next-steps"></a>További lépések

Miután befejezte az adatfolyam kialakítását, kapcsolja be a hibakeresés gombot a hibakeresési módban, vagy közvetlenül az adatfolyam- [tervezőben](concepts-data-flow-debug-mode.md) vagy a [folyamat hibakeresésében](control-flow-execute-data-flow-activity.md).
