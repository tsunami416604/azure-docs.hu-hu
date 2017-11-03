---
title: "Az Azure Service Fabric CLI - sfctl tároló |} Microsoft Docs"
description: "A Service Fabric CLI sfctl tároló parancsok ismerteti."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-store"></a>sfctl tároló
Alapszintű fájl szint a végre műveleteket a fürt lemezképtárolóhoz.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    törlése| Törli a meglévő lemezkép tartalom tárolásához.|
|    gyökér-adatai| Lekérdezi a tartalommal kapcsolatos információkat az image store gyökérmappájában.|
|    stat  | Lekérdezi a lemezképet tároló tartalommal kapcsolatos információkat.|


## <a name="sfctl-store-delete"></a>sfctl tároló törlése
Törli a meglévő lemezkép tartalom tárolásához.

Törli a meglévő lemezkép store tartalmát a megadott lemezképben talált relatív elérési út tárolja. Ez a parancs segítségével törölje a feltöltött alkalmazáscsomagok kiépítésüket követően.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --tartalom-path [szükséges]| Relatív elérési útja a fájl vagy mappa az az image store saját gyökere.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-store-stat"></a>sfctl tároló stat
Lekérdezi a lemezképet tároló tartalommal kapcsolatos információkat.

Az image store, a lemezképet tároló tartalmat a megadott contentpath tulajdonsághoz megadott érték a gyökeréhez viszonyítva információt ad vissza.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| --tartalom-path [szükséges]| Relatív elérési útja a fájl vagy mappa az az image store saját gyökere.|
| – időtúllépés -t          | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése               | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h             | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o           | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés               | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes             | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).