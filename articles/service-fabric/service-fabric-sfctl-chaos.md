---
title: Az Azure Service Fabric CLI - sfctl choas |} Microsoft Docs
description: A Service Fabric CLI sfctl chaos parancsok ismerteti.
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
ms.openlocfilehash: 336e74d8f69cb04e6bd0e85fc68ba38b218fabae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-chaos"></a>sfctl chaos
Indítás, Leállítás, és a chaos teszt szolgáltatás jelentést.

## <a name="commands"></a>Parancsok

|Parancs|Leírás|
| --- | --- |
|    jelentés| A következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése.|
|    start | Ha Chaos még nem fut. a fürtben, elkezd futni Chaos és a megadott Chaos paraméterek.|
|    állj  | Chaos leáll a fürtben már fut, ha más módon azt nincs semmi hatása.|


## <a name="sfctl-chaos-report"></a>sfctl chaos jelentés
A következő szegmensnél az átadott-a folytatási kód vagy átadott-időtartománya alapján Chaos jelentés lekérése.

Megadhatja a következő szegmens Chaos jelentés beolvasandó ContinuationToken StartTimeUtc és EndTimeUtc időtartományt adhat meg, de nem adható meg mind a ContinuationToken, és az időtartományt az adott hívásban. Ha több mint 100 Chaos események, a Chaos jelentés eredmény abban az esetben szegmensek Ha a szegmens ne legyen nagyobb 100 Chaos események tartalmaz. 

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| ---folytatási| A folytatási támogatójogkivonat-paramétere a következő set eredmények beszerzésére szolgál. Az eredményeket a rendszer egyetlen válasz nem férnek el a folytatási kód nem üres érték szerepel az API-t adott válaszokat. Ha ez az érték átadása a következő API-hívás, hogy az API-t az eredmények következő készletet ad vissza. Ha további eredménytelen majd az a folytatási kód nem tartalmaz értéket. Ez a paraméter értéke nem lehet URL-kódolású.|
| --end-ideje-(UTC)   | Az időtartomány, amelyek Chaos jelentés esetében generálása jelképező ticks száma. További részleteket [DateTime.Ticks tulajdonság](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) osztásjelek vonatkozó további információért.|
| --start-ideje-(UTC) | Amelyek Chaos jelentés esetében generálása az időtartományt a kezdési idejét jelző ticks száma. További részleteket [DateTime.Ticks tulajdonság](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29) osztásjelek vonatkozó további információért.|
| – időtúllépés -t     | Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése          | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h        | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o      | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés          | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes        | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-chaos-start"></a>sfctl chaos indítása
Ha Chaos még nem fut. a fürtben, elkezd futni Chaos és a megadott Chaos paraméterek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – alkalmazás-típus-rendszerállapot-házirend-leképezés  | JSON kódolású listában megadott alkalmazás esetében maximális százalékos aránya a nem megfelelő alkalmazásokkal. A leírásokban lesz a kulcs az alkalmazás neve, és értékeként egész számot, amely a megadott alkalmazás típusú alkalmazások értékeléséhez használt MaxPercentUnhealthyApplications százalékban adja meg.|
| --disable-move-replika-hibák | Letiltja az áthelyezés elsődleges és másodlagos hibák áthelyezése.|
| --maximális-fürt-stabilizációs| Az összes fürt, miközben stabillá és kifogástalan állapotú entitások várakozási idő maximális mennyisége.  Alapértelmezett: 60.|
| --maximális-egyidejű-hibák    | Maximális száma párhuzamos hibák száma az iteráció előidézett.           Alapértelmezett: 1.|
| --maximális-százalék-sérült-alkalmazások  | Fürt állapotfigyelő Chaos során kiértékelésekor az engedélyezett maximális százalékos aránya a nem megfelelő alkalmazások mielőtt hibajelzést.|
| --maximális-százalék-sérült-csomópontok | Fürt állapotfigyelő Chaos során kiértékelésekor az engedélyezett maximális százalékos aránya nem kifogástalan csomópontokat mielőtt hibajelzést.|
| --idő futtatásra              | Teljes időtartam (másodpercben), amelynek Chaos automatikusan leállítása előtt fog futni. A maximális megengedett értéke 4 294 967 295 (System.UInt32.MaxValue).  Alapértelmezett: 4294967295.|
| – időtúllépés -t               | Időtúllépését másodpercben.  Alapértelmezett: 60.|
| --várakozási-idő-közötti-hibák | Várakozási idő (másodpercben) belül egy egyetlen munkamenetben egymást követő hibák között.  Alapértelmezett: 20.|
| --várakozási-idő-közötti-ismétlés| Idő-elkülönítése (másodpercben) Chaos két egymást követő ismétlésének.  Alapértelmezett: 30.|
| – Figyelmeztetés-,-hiba         | Fürt állapotfigyelő Chaos során kiértékelésekor figyelmeztetések hibaként kiegészített az azonos.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése                    | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h                  | Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o                | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.           Alapértelmezett: JSON-ná.|
| --lekérdezés                    | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes                  | Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos leállítása
Chaos leáll a fürtben már fut, ha más módon azt nincs semmi hatása.

Leállítja Chaos ütemezési további hibák; de a üzenetsoroktól hibák, nem érintettek.

### <a name="arguments"></a>Argumentumok

|Argumentum|Leírás|
| --- | --- |
| – időtúllépés -t| Időtúllépését másodpercben.  Alapértelmezett: 60.|

### <a name="global-arguments"></a>Globális argumentumok

|Argumentum|Leírás|
| --- | --- |
| --hibakeresése  | Naplózási növelése az összes hibakeresési naplók megjelenítése.|
| – Súgó -h| Ez egy súgóüzenet és kilépési megjelenítése.|
| – a kimeneti -o | Kimeneti formátum.  Megengedett értékek: json, jsonc, tábla, tsv.  Alapértelmezett: JSON-ná.|
| --lekérdezés  | JMESPath lekérdezési karakterlánc. További információt és példákat http://jmespath.org/ talál.|
| – részletes| Naplózási növelése. Használatát – a teljes hibakeresési naplók hibakeresési.|

## <a name="next-steps"></a>Következő lépések
- [A telepítő](service-fabric-cli.md) a Service Fabric CLI-t.
- A Service Fabric parancssori felület használatával használata a [minta parancsfájlok](/azure/service-fabric/scripts/sfctl-upgrade-application).