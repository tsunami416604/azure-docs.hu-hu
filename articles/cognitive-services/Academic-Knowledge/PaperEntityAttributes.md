---
title: Az Academic Knowledge API a tevékenységentitás-attribútumok papír |} A Microsoft Docs
description: Ismerje meg, hogy az attribútumok a papír entitás az Academic Knowledge API, a Cognitive Services használata.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 75efba2c2ce8842f233f766876ca00844338fb25
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35623953"
---
# <a name="paper-entity"></a>A tanulmány entitás

<sub> * Attribútumok az alábbiakban meghatározott tanulmány entitáshoz. (Folyó évi = "0") </sub>


Name (Név)    |Leírás                                        |Típus       | Műveletek
------- | ------------------------------------------------- | --------- | ----------------------------
Azonosító      |Entitásazonosító                                          |Int64      |Egyenlő
Ti      |A tanulmány címe                                        |Sztring     |Egyenlő,<br/>StartsWith
L       |A tanulmány nyelvi kód kötőjellel elválasztva "\@@@"            |Sztring     |Egyenlő
I       |A tanulmány év                                         |Int32      |Egyenlő,<br/>IsBetween
D       |A tanulmány dátuma                                         |Dátum       |Egyenlő,<br/>IsBetween
Másolat      |Idézetet száma                                     |Int32      |nincs  
ECC     |Becsült idézetet száma                           |Int32      |nincs
AA. AuN  |Szerző neve                                        |Sztring     |Egyenlő,<br/>StartsWith
AA. AuId |Szerző azonosítója                                          |Int64      |Egyenlő
AA. AfN  |Szerző kapcsolat neve                            |Sztring     |Egyenlő,<br/>StartsWith
AA. AfId |Szerző kapcsolatot azonosítója                              |Int64      |Egyenlő
AA. S    |Szerző ahhoz, hogy a tanulmány                         |Int32      |Egyenlő
F.FN    |Tanulmány név mező                                |Sztring     |Egyenlő,<br/>StartsWith
F.FId   |Tanulmány azonosító mezője                                  |Int64      |Egyenlő
J.JN    |Napló neve                                       |Sztring     |Egyenlő,<br/>StartsWith
J.JId   |Napló azonosítója                                         |Int64      |Egyenlő
C.CN    |Konferencia az adatsorozat neve                             |Sztring     |Egyenlő,<br/>StartsWith
C.CId   |Konferencia sorozat azonosítója                               |Int64      |Egyenlő
A relatív azonosítók     |Hivatkozott tanulmányok azonosítója                              |Int64]    |Egyenlő
W       |A tanulmány címe és absztrakt szavakat                |String]   |Egyenlő
E       |Kiterjesztett metaadatok (lásd az alábbi táblázatot)                |Sztring     |nincs  
        


## <a name="extended-metadata-attributes"></a>Kiterjesztett attribútumok metaadatok ##

Name (Név)    | Leírás               
--------|---------------------------    
DN      | A tanulmány megjelenített neve 
S       | Források – webes források a papír statikus rang szerint rendezett listája
S.Ty    | Adatforrás típusa (1:HTML, 2:Text, 3:PDF, 4:DOC, 5:PPT, 6:XLS, 7:PS)
S.U     | Forrás URL-címe
VFN     | Helyszín teljes neve – teljes név, a napló vagy konferencián
VSN     | Helyszín rövid neve – a napló vagy konferencián rövid neve
V       | Kötet - napló kötet
BV      | Napló neve
BT      | 
PB      | Napló Abreviations
I       | Probléma - napló probléma
PI      | FirstPage - tanulmány első oldalán
LP      | LastPage - tanulmány utolsó oldalán
DOI     | Digitális objektum azonosítója
Másolat      | Idézetet környezetek – hivatkozott tanulmány listája azonosítóhoz tartozó és a megfelelő környezettel a tanulmány (például: [{123: ["a hivatkozott tanulmány 123 lépésközt barna foxes ismert", "a Lusta kutyák egy korábbi misnomer, ahogyan a papír 123"]})
IA      | Fordított absztrakt
VONATKOZÓ IndexLength| Az index (absztrakt a word száma) lévő elemek száma
VONATKOZÓ InvertedIndex| Absztrakt szavak és az eredeti absztrakt megfelelő pozícióját (például: [{"a": [0, 15, 30]}, {"barna": [1]}, {"fox":[2]}])
