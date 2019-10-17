---
title: Azure Data Factory leképezési adatfolyam feltételes felosztásának átalakítása
description: Azure Data Factory adatfolyam feltételes felosztásának átalakítása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d06b5b86737d0940930a3ccea3b6d65be0a802f9
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387889"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Az adatforgalom feltételes felosztásának leképezése



![feltételes felosztási eszközkészlet](media/data-flow/conditionalsplit2.png "feltételes felosztási eszközkészlet")

A feltételes felosztású átalakítás az adatsorokat különböző adatfolyamokra irányíthatja az adattartalomtól függően. A feltételes felosztású átalakítás implementálása hasonló egy programozási nyelvben lévő eset döntési struktúrához. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján a megadott adatfolyamra irányítja az adatsort. Ez a transzformáció egy alapértelmezett kimenetet is biztosít, így ha egy sor nem felel meg a kifejezésnek, az alapértelmezett kimenetre lesz irányítva.

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

## <a name="multiple-paths"></a>Több elérési út

További feltételek hozzáadásához válassza az "adatfolyam hozzáadása" lehetőséget az alsó konfigurációs ablaktáblán, majd a kifejezés létrehozásához kattintson a Kifejezésszerkesztő szövegmezőbe.

![feltételes felosztású többszörös](media/data-flow/conditionalsplit3.png "feltételes felosztású többszörös")

## <a name="next-steps"></a>Következő lépések

Feltételes felosztással használt közös adatfolyam-átalakítások: [összekapcsolási transzformáció](data-flow-join.md), [keresési átalakítás](data-flow-lookup.md), [átalakítás kiválasztása](data-flow-select.md)
