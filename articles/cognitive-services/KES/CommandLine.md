---
title: Tudásbázis feltárása szolgáltatás parancssori felülettel |} Microsoft Docs
description: A KES parancssori felület segítségével az index és nyelvtani strukturált adatokból fájlok létrehozása, és telepítheti azokat Microsoft kognitív szolgáltatások webszolgáltatásként.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: ffa42ac73b42a8271004d2d45d7a80f3307ef059
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347151"
---
# <a name="command-line-interface"></a>Parancssori felület
A KES parancssori felület lehetővé teszi a strukturált adatokból index és nyelvtani fájlok létrehozása, és központi telepítésére webszolgáltatásként.  Az általános szintaxist használja azt: `kes.exe <command> <required_args> [<optional_args>]`.  Futtathat `kes.exe` jelenítse meg a parancsok, paraméterek nélkül vagy `kes.exe <command>` a megadott parancs elérhető argumentumok listájának megjelenítéséhez.  Az alábbiakban olvashat egy listát a használható:
* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>
## <a name="buildindex-command"></a>build_index parancs
A **build_index** parancs létrehozza a fájl és az objektumok indexelése adatfájlt bináris indexfájlja.  Az eredményül kapott indexfájlja használható strukturált lekérdezési kifejezések kiértékelése, illetve értelmezéseket a természetes nyelvű lekérdezés készítése együtt egy lefordított nyelvtan fájllal.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Paraméter      | Leírás               |
|----------------|---------------------------|
| `<schemaFile>` | A bemeneti séma elérési útja |
| `<dataFile>`   | A bemeneti elérési útja   |
| `<indexFile>`  | Elérési utat a kimeneti index |
| `--description <description>` | Leíró karakterlánc |
| `--remote <vmSize>`           | Távoli buildjénél a virtuális gép mérete |

Ezek a fájlok helyi elérési utat vagy az Azure BLOB URL-cím elérési útjait adható meg.  A következő sémafájl leíró indexelt, valamint a támogatott műveletek objektumok (lásd: [séma formátum](SchemaFormat.md)).  Az adatfájlban az objektumok és az attribútumértékek indexelése enumerálása (lásd: [adatformátum](DataFormat.md)).  Ha a sikeres fordítás a kimeneti indexfájlja tartalmaz a bemeneti adatok, amely támogatja a kívánt műveletek tömörített képét.  

Leíró karakterlánccal ezt követően egy bináris index használatával azonosításához opcionálisan megadható a **describe_index** parancsot.  

Alapértelmezés szerint az index épül, a helyi számítógépen.  Az Azure környezetbe kívül helyi buildek korlátozódnak. legfeljebb 10 000 objektumokat tartalmazó adatfájlok.  Ha a--távoli jelző van megadva, az indexet a megadott méretű ideiglenesen létrehozott Azure virtuális gép épül.  Ez lehetővé teszi, hogy a nagy indexek kialakítani, hatékonyan végez az Azure virtuális gépek több memória.  Lapozás, ami lelassítja az összeállítási folyamat elkerülése érdekében azt javasoljuk, egy virtuális Gépet a 3-szor a RAM mennyisége, a bemeneti adatok méretét.  Elérhető Virtuálisgép-méretek listáját lásd: [virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> A gyorsabb buildek presort valószínűség csökkentik az adatok fájlban található objektumok.

<a name="build_grammar-command"></a>
## <a name="buildgrammar-command"></a>build_grammar parancs
A **build_grammar** parancs lefordítja a bináris nyelvtan fájlba az XML-Fájlban megadott nyelvtan.  Az eredményül kapott nyelvtan fájl használható együtt egy index fájllal értelmezéseket a természetes nyelvű lekérdezés létrehozásához.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Paraméter       | Leírás               |
|-----------------|---------------------------|
| `<xmlFile>`     | A bemeneti XML nyelvtan specification elérési útja |
| `<grammarFile>` | Kimeneti lefordított nyelvtan elérési útja         |

Ezek a fájlok helyi elérési utat vagy az Azure BLOB URL-cím elérési útjait adható meg.  A nyelvtan megadását ismerteti a súlyozott természetes nyelvű kifejezések és a szemantikai értelmezéseket (lásd: [Nyelvtanformátum](GrammarFormat.md)).  Sikeres fordítás, ha a kimeneti nyelvtan fájl tartalmazza a bináris megjelenítése a nyelvtan megadását gyors dekódolás engedélyezéséhez.

<a name="host_service-command"/>
## <a name="hostservice-command"></a>host_service parancs
A **host_service** parancs a helyi számítógépen a KES szolgáltatás példányt üzemeltetnek.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Paraméter       | Leírás                |
|-----------------|----------------------------|
| `<grammarFile>` | A bemeneti bináris nyelvtan elérési útja         |
| `<indexFile>`   | A bemeneti bináris index elérési útja           |
| `--port <port>` | Helyi port számát.  Alapértelmezett: 8000 |

Ezek a fájlok helyi elérési utat vagy az Azure BLOB URL-cím elérési útjait adható meg.  Egy webszolgáltatás üzemeltetett http://localhost:&lt; port&gt;/.  Lásd: [webes API-k](WebAPI.md) támogatott műveletek listáját.

Az Azure-on kívüli környezet, helyileg üzemeltetett szolgáltatások korlátozódnak index fájlok mérete, 10 kérelmek / másodperc és az összes hívás 1000 legfeljebb 1 MB.  Ezek a korlátozások megoldásához, futtassa **host_service** egy Azure virtuális Gépen belül, vagy telepítse az Azure cloud service használatával **deploy_service**.

<a name="deploy_service-command"/>
## <a name="deployservice-command"></a>deploy_service parancs
A **deploy_service** parancs a KES szolgáltatás egy példányát telepíti egy Azure felhőszolgáltatást.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Paraméter       | Leírás                  |
|-----------------|------------------------------|
| `<grammarFile>` | A bemeneti bináris nyelvtan elérési útja           |
| `<indexFile>`   | A bemeneti bináris index elérési útja             |
| `<serviceName>` | Cél felhőalapú szolgáltatás neve |
| `<vmSize>`      | A felhőalapú szolgáltatás virtuális gép mérete     |
| `--slot <slot>` | Cloud service tárolóhely: "tesztelés" (alapértelmezett), "éles" |

Ezek a fájlok helyi elérési utat vagy az Azure BLOB URL-cím elérési útjait adható meg.  Szolgáltatás nevét megadja egy előre konfigurált Azure-felhőszolgáltatásban (lásd: [létrehozásáról és központi telepítése egy felhőalapú szolgáltatás](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  A parancs a megadott Azure-felhőszolgáltatásban, a megadott méretű virtuális gépek használata a KES szolgáltatás automatikusan telepíti.  Lapozófájl, ami jelentősen csökkenti a teljesítmény elkerülése érdekében azt javasoljuk, a virtuális gépek 1 GB több RAM Memóriát, mint a bemeneti index méretét.  Elérhető Virtuálisgép-méretek listáját lásd: [Felhőszolgáltatások mérete](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Alapértelmezés szerint a szolgáltatás központi telepítése az átmeneti opcionálisan felülbírált a--tárolóhely paraméteren keresztül.  Lásd: [webes API-k](WebAPI.md) támogatott műveletek listáját.

<a name="describe_index-command"/>
## <a name="describeindex-command"></a>describe_index parancs
A **describe_index** parancs kimenetében információkat egy index, beleértve a séma és a leírást.

`kes.exe describe_index <indexFile>`

| Paraméter     | Leírás      |
|---------------|------------------|
| `<indexFile>` | Bemeneti index elérési útja |

Ezt a fájlt egy helyi fájl elérési útját, vagy egy Azure-blobba egy URL-címe adható meg.  A kimeneti leíró karakterlánc adható--leírás paraméterének a **build_index** parancsot.

<a name="describe_grammar-command"/>
## <a name="describegrammar-command"></a>describe_grammar parancs
A **describe_grammar** parancs kimenete az eredeti nyelvtan specifikáció alapján állítja össze a bináris nyelvtan.

`kes.exe describe_grammar <grammarFile>`

| Paraméter       | Leírás      |
|-----------------|------------------|
| `<grammarFile>` | Bemeneti nyelvtan elérési útja |

Ezt a fájlt egy helyi fájl elérési útját, vagy egy Azure-blobba egy URL-címe adható meg.

