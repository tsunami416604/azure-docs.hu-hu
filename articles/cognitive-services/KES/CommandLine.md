---
title: Parancssori felület – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: A parancssori felület segítségével index-és nyelvhelyesség-fájlokat hozhat létre a strukturált adatokból, majd webszolgáltatásként telepítheti azokat.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/24/2016
ms.author: paulhsu
ms.openlocfilehash: 018552982a8ece3bbbaea2d60e2a6e64f681f822
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385635"
---
# <a name="command-line-interface"></a>Parancssori felület

A Knowledge Exploration Service (KES) parancssori felület lehetővé teszi index-és nyelvhelyességi fájlok összeállítását a strukturált adatokból, valamint webszolgáltatásként való üzembe helyezését.  Az általános szintaxist használja: `kes.exe <command> <required_args> [<optional_args>]`.  Az `kes.exe` argumentumok nélkül is futtathatók a parancsok listájának megjelenítéséhez, vagy `kes.exe <command>` az adott parancshoz elérhető argumentumok listájának megjelenítéséhez.  Alább látható az elérhető parancsok listája:

* build_index
* build_grammar
* host_service
* deploy_service
* describe_index
* describe_grammar

<a name="build_index-command"></a>

## <a name="build_index-command"></a>build_index parancs

Az **build_index** parancs bináris index fájlt hoz létre egy séma-definíciós fájlból és egy indexelt objektumok adatfájlját.  Az eredményül kapott indexfájl felhasználható a strukturált lekérdezési kifejezések kiértékelésére, illetve a természetes nyelvű lekérdezések értelmezésének előállítására egy lefordított nyelvtani fájllal együtt.

`kes.exe build_index <schemaFile> <dataFile> <indexFile> [options]`

| Paraméter      | Leírás               |
|----------------|---------------------------|
| `<schemaFile>` | Bemeneti séma elérési útja |
| `<dataFile>`   | Bemeneti adatok elérési útja   |
| `<indexFile>`  | Kimeneti index elérési útja |
| `--description <description>` | Leírás karakterlánca |
| `--remote <vmSize>`           | A virtuális gép mérete távoli buildhez |

Ezeket a fájlokat a helyi fájlelérési utak vagy az Azure-Blobok URL-elérési útjai adhatják meg.  A sémafájl leírja az indexelt objektumok szerkezetét, valamint a támogatott műveleteket (lásd: [séma formátuma](SchemaFormat.md)).  Az adatfájl enumerálja az indexelni kívánt objektumokat és attribútumokat (lásd [az adatformátumot](DataFormat.md)).  Ha a létrehozás sikeres, a kimeneti index fájl a kívánt műveleteket támogató bemeneti adatok tömörített ábrázolását tartalmazza.  

A leírási karakterláncok opcionálisan megadhatók a bináris index későbbi azonosításához a **describe_index** parancs használatával.  

Alapértelmezés szerint az index a helyi gépre épül.  Az Azure-környezeten kívül a helyi buildek legfeljebb 10 000 objektumot tartalmazó adatfájlokra korlátozódnak.  Ha a--Remote jelző meg van adva, az index a megadott méretű, ideiglenesen létrehozott Azure-beli virtuális gépre lesz felépítve.  Ez lehetővé teszi, hogy a nagyméretű indexek hatékonyan legyenek felépítve az Azure-beli virtuális gépek több memóriával való használatával.  Ha el szeretné kerülni a lapozást, amely lelassítja a felépítési folyamatot, javasoljuk, hogy használjon egy virtuális gépet, amely a bemeneti adatfájl méretének háromszorosa.  Elérhető virtuálisgép-méretek listája: [Virtuális gépek méretei](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

> [!TIP] 
> A gyorsabb buildek esetében az adatfájlban lévő objektumokat csökkenő valószínűséggel rendezheti.

<a name="build_grammar-command"></a>

## <a name="build_grammar-command"></a>build_grammar parancs

A **build_grammar** parancs egy XML-fájlban megadott nyelvtant állít össze egy bináris nyelvtani fájlba.  Az eredményül kapott nyelvtani fájl használható egy indexfájl használatával a természetes nyelvi lekérdezések értelmezésének létrehozásához.

`kes.exe build_grammar <xmlFile> <grammarFile>`

| Paraméter       | Leírás               |
|-----------------|---------------------------|
| `<xmlFile>`     | Bemeneti XML nyelvtani specifikációjának elérési útja |
| `<grammarFile>` | Kimenet lefordított nyelvtani elérési útja         |

Ezeket a fájlokat a helyi fájlelérési utak vagy az Azure-Blobok URL-elérési útjai adhatják meg.  A nyelvtani specifikáció a súlyozott természetes nyelvi kifejezések és a szemantikai értelmezések készletét írja le (lásd a [nyelvtan formátumát](GrammarFormat.md)).  Ha a létrehozás sikeres, a kimeneti nyelvhelyességi fájl a nyelvtani specifikáció bináris ábrázolását tartalmazza a gyors dekódolás engedélyezéséhez.

<a name="host_service-command"/>

## <a name="host_service-command"></a>host_service parancs

Az **host_service** parancs a Kes szolgáltatás egy példányát futtatja a helyi gépen.

`kes.exe host_service <grammarFile> <indexFile> [options]`

| Paraméter       | Leírás                |
|-----------------|----------------------------|
| `<grammarFile>` | Bemeneti bináris nyelvtani útvonal         |
| `<indexFile>`   | Bemeneti bináris index elérési útja           |
| `--port <port>` | Helyi portszám.  Alapértelmezett: 8000 |

Ezeket a fájlokat a helyi fájlelérési utak vagy az Azure-Blobok URL-elérési útjai adhatják meg.  A webszolgáltatásokat a rendszer a http://localhost:&lt;p ORT&gt;/.  A támogatott műveletek listáját a [webes API](WebAPI.md) -k részben tekintheti meg.

Az Azure-környezeten kívül a helyileg üzemeltetett szolgáltatások legfeljebb 1 MB méretű, 10 kérés/másodperc és 1000 összes hívást képesek indexelni.  A korlátozások leküzdéséhez futtasson **host_service** egy Azure-beli virtuális gépen, vagy telepítsen egy Azure Cloud Service-be **deploy_service**használatával.

<a name="deploy_service-command"/>

## <a name="deploy_service-command"></a>deploy_service parancs

Az **deploy_service** parancs üzembe helyezi a Kes szolgáltatás egy példányát egy Azure Cloud Service-ben.

`kes.exe deploy_service <grammarFile> <indexFile> <serviceName> <vmSize>[options]`

| Paraméter       | Leírás                  |
|-----------------|------------------------------|
| `<grammarFile>` | Bemeneti bináris nyelvtani útvonal           |
| `<indexFile>`   | Bemeneti bináris index elérési útja             |
| `<serviceName>` | A cél felhőalapú szolgáltatás neve |
| `<vmSize>`      | A Cloud Service virtuális gép mérete     |
| `--slot <slot>` | Cloud Service-tárolóhely: "előkészítés" (alapértelmezett), "éles" |

Ezeket a fájlokat a helyi fájlelérési utak vagy az Azure-Blobok URL-elérési útjai adhatják meg.  A szolgáltatás neve egy előre konfigurált Azure Cloud Service-szolgáltatást határoz meg (lásd: [felhőalapú szolgáltatás létrehozása és üzembe helyezése](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md)).  A parancs automatikusan telepíti a KES szolgáltatást a megadott Azure Cloud Service-be a megadott méretű virtuális gépek használatával.  Ha el szeretné kerülni a lapozást, amely jelentősen csökkenti a teljesítményt, javasoljuk, hogy használjon 1 GB-nál nagyobb méretű virtuális gépet a bemeneti index fájlméretének megfelelően.  A rendelkezésre álló virtuálisgép-méretek listáját lásd: [Cloud Services méretei](../../../articles/cloud-services/cloud-services-sizes-specs.md).

Alapértelmezés szerint a szolgáltatás az átmeneti környezetbe van telepítve, opcionálisan felülbírálva a--slot paraméterrel.  A támogatott műveletek listáját a [webes API](WebAPI.md) -k részben tekintheti meg.

<a name="describe_index-command"/>

## <a name="describe_index-command"></a>describe_index parancs

Az **describe_index** parancs egy indexfájl adatait jeleníti meg, beleértve a sémát és a leírást is.

`kes.exe describe_index <indexFile>`

| Paraméter     | Leírás      |
|---------------|------------------|
| `<indexFile>` | Bemeneti index elérési útja |

Ezt a fájlt egy helyi fájl elérési útja vagy egy Azure-Blob URL-elérési útja is megadhatja.  A kimeneti leírást megadó karakterlánc a **build_index** parancs--Description paraméterrel adható meg.

<a name="describe_grammar-command"/>

## <a name="describe_grammar-command"></a>describe_grammar parancs

A **describe_grammar** parancs kimenete a bináris nyelvtan felépítéséhez használt eredeti nyelvtani specifikáció.

`kes.exe describe_grammar <grammarFile>`

| Paraméter       | Leírás      |
|-----------------|------------------|
| `<grammarFile>` | Bemeneti nyelvtani elérési út |

Ezt a fájlt egy helyi fájl elérési útja vagy egy Azure-Blob URL-elérési útja is megadhatja.

