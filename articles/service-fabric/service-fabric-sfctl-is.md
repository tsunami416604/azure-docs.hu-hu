---
title: Az Azure Service Fabric CLI - sfctl van |} Microsoft Docs
description: Ismerteti a Service Fabric CLI sfctl parancsok.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: d3a4c7a308bdf7132cebffc13a5e7214ec73eaf2
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-is"></a>sfctl értéke
Lekérdezi és parancsainak elküldését az infrastruktúra-szolgáltatás.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    a parancs| Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs.|
|    lekérdezés  | Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést.|


## <a name="sfctl-is-command"></a>sfctl parancs
Meghívja az adott infrastruktúra szolgáltatáspéldányon egy felügyeleti parancs.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus parancsokat küldhet az adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot| Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus. |
| --service-id     | Az infrastruktúra-szolgáltatás identitásának. Az azonosító: a teljes nevet, az infrastruktúra-szolgáltatás nélkül a "fabric:" URI-séma. Ez a paraméter csak az infrastruktúra-szolgáltatás fut egynél több példánnyal rendelkező fürtök kötelező.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információk és példák: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-is-query"></a>sfctl Ez a lekérdezés
Meghívja az adott infrastruktúra szolgáltatáspéldányon írásvédett lekérdezést.

Fürtöknél az infrastruktúra-szolgáltatás, konfigurálva egy vagy több példányát Ez az API biztosítja az infrastruktúra-specifikus lekérdezéseket küldjön az infrastruktúra-szolgáltatás adott példányára. Elérhető parancsok és a megfelelő választ formátumukban eltérőek lehetnek attól függően, az infrastruktúra, amelyen fut a fürtön. Ez az API támogatja a Service Fabric-platformról; nem célja, hogy közvetlenül a kódból használni.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – [szükséges] parancsot| Meg kell hívni a parancs szövege. A parancs tartalma infrastruktúra-specifikus.|
| --service-id     | Az infrastruktúra-szolgáltatás identitásának. Az azonosító: a teljes nevet, az infrastruktúra-szolgáltatás nélkül a "fabric:" URI-séma. Ez a paraméter csak több mint egy infrastruktúra-szolgáltatás fut-példánnyal rendelkező fürtök szükség.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --debug          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információkért lásd: http://jmespath.org/.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>További lépések
- [Állítson be](service-fabric-cli.md) a Service Fabric CLI.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).