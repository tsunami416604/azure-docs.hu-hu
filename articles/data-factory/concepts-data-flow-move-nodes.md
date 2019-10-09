---
title: Azure Data Factory adatfolyam-áthelyezési csomópontok
description: Csomópontok áthelyezése egy Azure Data Factory leképezési adatfolyamati diagramon
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 853fff955a953052b94ad4e7e1dd55371069c30b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030186"
---
# <a name="mapping-data-flow-move-nodes"></a>Adatfolyam-áthelyezési csomópontok leképezése



![Összesített transzformációs beállítások](media/data-flow/agghead.png "összesítési fejléce")

A Azure Data Factory adatáramlás tervezési felülete egy "építési" felület, amelyben felülről lefelé, balról jobbra hozhat létre adatfolyamokat. Az egyes átalakításokhoz egy pluszjel (+) szimbólum van csatolva. Koncentráljon az üzleti logikára ahelyett, hogy a csomópontokat a szabad formátumú DAG-környezet szélein keresztül csatlakoztatja.

Így a fogd és vidd típusú paradigma nélkül az átalakítási csomópontok áthelyezésének módja a bejövő adatfolyam módosítása. Ehelyett a "bejövő adatfolyam" módosításával áthelyezi az átalakításokat.

## <a name="streams-of-data-inside-of-data-flow"></a>Adatfolyamban tárolt adatstreamek

Azure Data Factory adatfolyamban a streamek az adatáramlást jelölik. Az átalakítási beállítások panelen megjelenik egy "bejövő gőz" mező. Ez azt mutatja be, hogy mely bejövő adatfolyam táplálja az átalakítást. Az átalakítási csomópont fizikai helyét a diagramon módosíthatja úgy, hogy a bejövő adatfolyam nevére kattint, és kiválasztja egy másik adatfolyamot. A jelenlegi átalakítás az adott adatfolyamon végzett összes további átalakítással együtt az új helyre kerül.

Ha az átalakítást egy vagy több átalakítással helyezi át, a folyamat során az új hely egy új ág használatával lesz csatlakoztatva.

Ha a kiválasztott csomópont után nem végez további átalakítást, akkor csak az adott átalakítás fog áttérni az új helyre.

## <a name="next-steps"></a>További lépések

Miután befejezte az adatfolyam kialakítását, kapcsolja be a hibakeresés gombot a hibakeresési módban, vagy közvetlenül az adatfolyam- [tervezőben](concepts-data-flow-debug-mode.md) vagy a [folyamat hibakeresésében](control-flow-execute-data-flow-activity.md).
