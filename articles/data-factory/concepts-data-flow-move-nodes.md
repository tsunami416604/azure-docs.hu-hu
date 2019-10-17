---
title: Azure Data Factory adatfolyam-áthelyezési csomópontok
description: Csomópontok áthelyezése egy Azure Data Factory leképezési adatfolyamati diagramon
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 2031820843342fb7e6b115865297e08cbee28a0a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387334"
---
# <a name="mapping-data-flow-move-nodes"></a>Adatfolyam-áthelyezési csomópontok leképezése



![Összesített átalakítási beállítások](media/data-flow/agghead.png "összesítő fejléc")

A Azure Data Factory adatáramlás tervezési felülete egy "építési" felület, amelyben felülről lefelé, balról jobbra hozhat létre adatfolyamokat. Az egyes átalakításokhoz egy pluszjel (+) szimbólum van csatolva. Koncentráljon az üzleti logikára ahelyett, hogy a csomópontokat a szabad formátumú DAG-környezet szélein keresztül csatlakoztatja.

Így a fogd és vidd típusú paradigma nélkül az átalakítási csomópontok áthelyezésének módja a bejövő adatfolyam módosítása. Ehelyett a "bejövő adatfolyam" módosításával áthelyezi az átalakításokat.

## <a name="streams-of-data-inside-of-data-flow"></a>Adatfolyamban tárolt adatstreamek

Azure Data Factory adatfolyamban a streamek az adatáramlást jelölik. Az átalakítási beállítások panelen megjelenik egy "bejövő gőz" mező. Ez azt mutatja be, hogy mely bejövő adatfolyam táplálja az átalakítást. Az átalakítási csomópont fizikai helyét a diagramon módosíthatja úgy, hogy a bejövő adatfolyam nevére kattint, és kiválasztja egy másik adatfolyamot. A jelenlegi átalakítás az adott adatfolyamon végzett összes további átalakítással együtt az új helyre kerül.

Ha az átalakítást egy vagy több átalakítással helyezi át, a folyamat során az új hely egy új ág használatával lesz csatlakoztatva.

Ha a kiválasztott csomópont után nem végez további átalakítást, akkor csak az adott átalakítás fog áttérni az új helyre.

## <a name="next-steps"></a>Következő lépések

Miután befejezte az adatfolyam kialakítását, kapcsolja be a hibakeresés gombot a hibakeresési módban, vagy közvetlenül az adatfolyam- [tervezőben](concepts-data-flow-debug-mode.md) vagy a [folyamat hibakeresésében](control-flow-execute-data-flow-activity.md).
