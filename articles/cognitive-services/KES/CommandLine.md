---
title: Parancssori felület – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: A parancssori felület használatával hozhat létre a strukturált adatok index és a nyelvi fájlokat, és telepítheti őket a webszolgáltatásként.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 7ec2282317019275b15a8e506753408c75a68561
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127149"
---
# <a name="command-line-interface"></a>Parancssori felület

A Knowledge Exploration Service (KES) parancssori felület lehetővé teszi a strukturált adatok index és a nyelvi fájlokat készíthet és helyezhet üzembe a őket webszolgáltatásként.  Az általános szintaxist használ: `kes.exe <command> <required_args> [<optional_args>]`.  Futtathat `kes.exe` listázza a parancsok, argumentumok nélkül, vagy `kes.exe <command>` a megadott parancs elérhető argumentumok listájának megjelenítéséhez.  Az alábbiakban az elérhető parancsok listája:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="buildindex-command"></a>a parancs build_index

A **build_index** a parancs létrehozza a fájl és a egy adatfájlt indexelendő objektumok egy bináris indexfájl.  Az eredményül kapott indexfájl használható strukturált lekérdezési kifejezések kiértékelése, vagy létrehozhat egy természetes nyelvű lekérdezések értelmezések együtt egy lefordított nyelvtani fájlt.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Paraméter      | Leírás               |
|----------------|---------------------------|
| `<schemaFile>` | A bemeneti séma elérési útja |
| `<dataFile>`   | A bemeneti adatok elérési útja   |
| `<indexFile>`  | Kimeneti index elérési útja |
| `--description <description>` | Leíró karakterlánc |
| `--remote <vmSize>`           | Protokol vzdáleného buildu. a virtuális gép mérete |

Ezek a fájlok helyi fájlok elérési útja vagy URL-elérési útvonal az Azure-blobok adható meg.  A sémafájl struktúráját az objektumok indexelt, valamint a támogatott műveleteket ismerteti (lásd: [sémaformátum](SchemaFormat.md)).  Az adatfájl számba veszi azokat az objektumokat és indexelése attribútumértékek (lásd: [adatformátum](DataFormat.md)).  Ha a build sikeres, a kimeneti index fájl tartalmazza a bemeneti adatokat a kívánt műveleteket támogató tömörített reprezentációját.  

A leíró karakterlánc igény szerint megadható ezt követően a egy bináris index használatával azonosíthatja a **describe_index** parancsot.  

Alapértelmezés szerint az index felépítését a helyi gépen.  Azure-környezeten kívül helyi buildek korlátozva, legfeljebb 10 000 objektumokat tartalmazó adatfájlokat.  Ha a--távoli jelző van megadva, az index a megadott méretű ideiglenesen létrehozott Azure virtuális gép a lesz felépítve.  Ez lehetővé teszi az Azure virtuális gépek hatékony használata több memóriát kell létrehozni nagy indexek.  Lapozófájl, ami lelassítja az összeállítási folyamat elkerülése érdekében javasoljuk, egy virtuális gép használata a 3-szor a RAM mennyisége, a bemeneti adatok mérete.  Elérhető Virtuálisgép-méretek listáját lásd: [virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> A gyorsabb buildek presort az adatfájl található objektumok valószínűség csökkentésével.

<a name="build_grammar-command"></a>

## <a name="buildgrammar-command"></a>a parancs build_grammar

A **build_grammar** parancs lefordítja a megadott XML-fájlba binární gramatika je nyelvtan.  Az eredményül kapott szintaxis-fájl használható együtt egy index fájllal értelmezéseket a természetes nyelvű lekérdezések létrehozásához.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Paraméter       | Leírás               |
|-----------------|---------------------------|
| `<xmlFile>`     | Bemeneti XML nyelvi specifikáció elérési útja |
| `<grammarFile>` | Kimeneti lefordított nyelvtani elérési útja         |

Ezek a fájlok helyi fájlok elérési útja vagy URL-elérési útvonal az Azure-blobok adható meg.  A nyelvi specifikáció súlyozott természetes nyelvi kifejezéseket és azok szemantikai értelmezések ismerteti (lásd: [Szintaxisformátum](GrammarFormat.md)).  A build sikeres, ha a kimeneti nyelvtani fájl tartalmazza a nyelvi specifikáció gyors dekódolás engedélyezése bináris kódolású karakterláncként.

<a name="host_service-command"/>

## <a name="hostservice-command"></a>a parancs host_service

A **host_service** parancsot a helyi gépen a KES szolgáltatás példányát futtatja.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Paraméter       | Leírás                |
|-----------------|----------------------------|
| `<grammarFile>` | A bemeneti binární gramatika je elérési útja         |
| `<indexFile>`   | A bemeneti bináris index elérési útja           |
| `--port <port>` | Helyi port száma.  Alapértelmezett: 8000-es |

Ezek a fájlok helyi fájlok elérési útja vagy URL-elérési útvonal az Azure-blobok adható meg.  Egy webszolgáltatás üzemeltetett http://localhost:&ltportot;&gt;/.  Lásd: [webes API-k](WebAPI.md) támogatott műveletek listáját.

Az Azure-on kívül a környezetben, helyileg üzemeltetett szolgáltatások korlátozva, index legfeljebb 1 MB méretű, 10 kérelmek / másodperc és 1000 teljes hívás fájlok.  Futtassa, hogy ezek a korlátozások, **host_service** egy Azure virtuális gépen, vagy helyezze üzembe az Azure cloud service használatával **deploy_service**.

<a name="deploy_service-command"/>

## <a name="deployservice-command"></a>a parancs deploy_service

A **deploy_service** a parancs üzembe helyezi a KES service egy példányát az Azure felhőszolgáltatás.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Paraméter       | Leírás                  |
|-----------------|------------------------------|
| `<grammarFile>` | A bemeneti binární gramatika je elérési útja           |
| `<indexFile>`   | A bemeneti bináris index elérési útja             |
| `<serviceName>` | Cél felhőszolgáltatás neve |
| `<vmSize>`      | Felhőszolgáltatás virtuális gép mérete     |
| `--slot <slot>` | Felhőszolgáltatási tárolóhely: "átmeneti" (alapértelmezett), az "éles" |

Ezek a fájlok helyi fájlok elérési útja vagy URL-elérési útvonal az Azure-blobok adható meg.  Szolgáltatás nevét megadja egy előre konfigurált Azure-felhőszolgáltatásban (lásd: [hogyan hozhat létre és telepíthet egy Felhőszolgáltatást](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  A parancs a megadott Azure cloud Services, a megadott méretű virtuális gépeket használ, automatikusan KES szolgáltatás üzembe helyezése.  Lapozófájl, ami jelentősen csökkenti a teljesítmény elkerülése érdekében javasoljuk egy virtuális Gépet 1 GB több RAM Memóriát, mint az index a bemeneti fájl mérete.  Elérhető Virtuálisgép-méretek listáját lásd: [méretű felhőszolgáltatások](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Alapértelmezés szerint a szolgáltatás üzembe helyezése az átmeneti környezetben keresztül a--céltárolóhely paramétere igény szerint felülbírált.  Lásd: [webes API-k](WebAPI.md) támogatott műveletek listáját.

<a name="describe_index-command"/>

## <a name="describeindex-command"></a>describe_index parancs

A **describe_index** paranccsal kiadathatja információkat egy index, beleértve a séma és a leírást.

`kes.exe describe_index <indexFile>`

| Paraméter     | Leírás      |
|---------------|------------------|
| `<indexFile>` | Index a bemeneti elérési út |

Ezt a fájlt egy helyi fájlelérési út vagy egy Azure-blobba URL-cím adható meg.  A kimeneti leíró karakterlánc segítségével adható meg, a--leíró paraméter a **build_index** parancsot.

<a name="describe_grammar-command"/>

## <a name="describegrammar-command"></a>describe_grammar parancs

A **describe_grammar** paranccsal kiadathatja az eredeti szintaxis-specifikáció a binární gramatika je összeállításához.

`kes.exe describe_grammar <grammarFile>`

| Paraméter       | Leírás      |
|-----------------|------------------|
| `<grammarFile>` | A bemeneti nyelvtani elérési útja |

Ezt a fájlt egy helyi fájlelérési út vagy egy Azure-blobba URL-cím adható meg.

