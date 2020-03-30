---
title: Adatfolyamok – problémamegoldás
description: Ismerje meg, hogyan háríthatja el az azure Data Factory adatáramlási problémáit.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472128"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Adatfolyamok – problémamegoldás az Azure Data Factoryban

Ez a cikk az Azure Data Factory adatfolyamainak gyakori hibaelhárítási módszereit ismerteti.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Hibakód: DF-Executor-SourceInvalidPayload
- **Üzenet**: Az adatelőnézet, a hibakeresés és a folyamatadatfolyam végrehajtása nem sikerült, mert a tároló nem létezik
- **Okok**: Ha az adatkészlet olyan tárolót tartalmaz, amely nem létezik a tárolóban
- **Javaslat:** Győződjön meg arról, hogy az adatkészletben hivatkozott tároló létezik vagy elérhető.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Hibakód: DF-Executor-SystemImplicitCartesian

- **Üzenet:** Implicit descartes-i termék INNER illesztés nem támogatott, használja CROSS JOIN helyett. Az illesztésben használt oszlopoknak egyedi kulcsot kell létrehozniuk a sorokhoz.
- **Okok**: Implicit descartes-i termék inner illesztés logikai tervek között nem támogatott. Ha az illesztésben használt oszlopok hozják létre az egyedi kulcsot, a kapcsolat mindkét oldaláról legalább egy oszlopra van szükség.
- **Ajánlás**: A nem egyenlőség alapú illesztések esetén a CUSTOM CROSS JOIN-ot kell választania.

### <a name="error-code-df-executor-systeminvalidjson"></a>Hibakód: DF-Executor-SystemInvalidJson

- **Üzenet**: JSON elemzési hiba, nem támogatott kódolás vagy többsoros
- **Okok**: Lehetséges problémák a JSON-fájllal kapcsolatban: nem támogatott kódolás, sérült bájtok, vagy JSON-forrás használata egyetlen dokumentumként számos beágyazott vonalon
- **Javaslat**: Ellenőrizze, hogy a JSON-fájl kódolása támogatott-e. A JSON-adatkészletet használó Forrás-átalakításon bontsa ki a "JSON-beállítások" csomópontot, és kapcsolja be az "Egydokumentum" beállítást.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Hibakód: DF-Executor-BroadcastTimeout

- **Üzenet**: Broadcast csatlakozás időtúllépési hiba, győződjön meg arról, hogy a szórásos adatfolyam 60 másodpercen belül adatokat hoz létre a hibakeresési futtatásokés és 300 mp feladatfuttatások esetén
- **Okok**: A szórás alapértelmezett időtúllépése 60 másodperc a hibakeresési futtatásokés és 300 mp a feladatfuttatások esetén. A közvetítésre kiválasztott adatfolyam nagynak tűnik, hogy ezen a korláton belül adatokat állítson elő.
- **Javaslat**: Kerülje a nagy adatfolyamok sugárzását, ahol a feldolgozás több mint 60 másodpercet vehet igénybe. Válasszon inkább egy kisebb adatfolyamot, amelyet közvetíteni szeretne. A nagy MÉRETŰ SQL/DW táblák és forrásfájlok általában rossz jelöltek.

### <a name="error-code-df-executor-conversion"></a>Hibakód: DF-Executor-Conversion

- **Üzenet:** Érvénytelen karakter miatt nem sikerült dátummá vagy időpontra konvertálni
- **Okok**: Az adatok formátuma nem a várt
- **Ajánlás**: A megfelelő adattípus használata

### <a name="error-code-df-executor-invalidcolumn"></a>Hibakód: DF-Executor-InvalidColumn

- **Üzenet**: Az oszlopnevet meg kell adni a lekérdezésben, állítson be aliast, ha SQL függvényt használ
- **Okok**: Nincs megadva oszlopnév
- **Javaslat**: Állítson be egy aliast, ha sql függvényt használ, például min()/max(), stb.

## <a name="general-troubleshooting-guidance"></a>Általános hibaelhárítási útmutató

1. Ellenőrizze az adatkészlet-kapcsolatok állapotát. Az egyes forrás- és fogadóátalakítások során keresse fel a csatolt szolgáltatást minden egyes használt adatkészlethez, és tesztelje a kapcsolatokat.
1. Ellenőrizze a fájl- és táblakapcsolatok állapotát az adatfolyam-tervezőtől. Kapcsolja be a Debug ot, és kattintson az Adatelőnézet gombra a forrásátalakításokon, hogy biztosan hozzáférhessen az adatokhoz.
1. Ha minden jól néz ki az adatok előnézetéből, lépjen be a Folyamattervezőbe, és helyezze az adatfolyamot egy folyamattevékenységbe. Hibakeresés a folyamat egy végpontok között teszt.

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálkozzon az alábbi forrásokkal:
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatáskérések](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN fórum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack túlcsordulás fórum Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter információk a Data Factory-ról](https://twitter.com/hashtag/DataFactory)
*  [ADF-hozzárendelési adatfolyamok Teljesítményútmutató](concepts-data-flow-performance.md)
