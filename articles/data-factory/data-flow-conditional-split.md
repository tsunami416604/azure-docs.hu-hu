---
title: Azure Data Factory leképezési adatfolyam feltételes felosztásának átalakítása
description: Azure Data Factory adatfolyam feltételes felosztásának átalakítása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fec2b09b9dc471135d9cdd00ac2465728a47bdbf
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026931"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Az adatforgalom feltételes felosztásának leképezése



![feltételes felosztású eszközkészlet](media/data-flow/conditionalsplit2.png "feltételes felosztási eszközkészlete")

A feltételes felosztású átalakítás az adatsorokat különböző adatfolyamokra irányíthatja az adattartalomtól függően. A feltételes felosztású átalakítás implementálása hasonló egy programozási nyelvben lévő eset döntési struktúrához. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján a megadott adatfolyamra irányítja az adatsort. Ez a transzformáció egy alapértelmezett kimenetet is biztosít, így ha egy sor nem felel meg a kifejezésnek, az alapértelmezett kimenetre lesz irányítva.

![feltételes felosztott](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

## <a name="multiple-paths"></a>Több elérési út

További feltételek hozzáadásához válassza az "adatfolyam hozzáadása" lehetőséget az alsó konfigurációs ablaktáblán, majd a kifejezés létrehozásához kattintson a Kifejezésszerkesztő szövegmezőbe.

![feltételes felosztás – többszörös](media/data-flow/conditionalsplit3.png "feltételes felosztású többszörös")

## <a name="next-steps"></a>További lépések

Feltételes felosztással használt közös adatfolyam-átalakítások: [Összekapcsolási átalakítás](data-flow-join.md), [keresési átalakítás](data-flow-lookup.md), [átalakítás kiválasztása](data-flow-select.md)
