---
title: A dokumentum-formátumok és elnevezési konvenciók – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Ez az útmutató, dokumentum-formátumok és egyéni a fordítót az elnevezési konvenciót. A fogalom segítségével kezelheti a dokumentumok nevek jobb abd elnevezések ütközésének elkerülése érdekében.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: afd3192117bd22c62fd8e36752515166e4c6e043
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225482"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Dokumentum-formátumok és -elnevezési konvenciót útmutató

Bármilyen egyéni fordítási használt fájlt kell, hogy **négy** karakternél hosszabb.

Ez a táblázat összes támogatott fájlformátum segítségével hozhat létre a fordítási rendszerét tartalmazza:

| Formátum            | Bővítmények   | Leírás                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .XLF, .XLIFF | Egy párhuzamos formát, exportálás fordítási memória rendszerek. A használt nyelvek a fájlban vannak definiálva.                                                                                                                                                              |
| TMX               | .TMX         | Egy párhuzamos formát, exportálás fordítási memória rendszerek. A használt nyelvek a fájlban vannak definiálva.                                                                                                                                                              |
| ZIP               | .ZIP         | ZIP-archívum fájlformátum.                                                                                                                                                                                                        |
| Locstudio         | .LCL         | A Microsoft formátuma párhuzamos dokumentumok                                                                                                                                                                                                                                      |
| Microsoft Word    | .DOCX        | A Microsoft Word-dokumentum                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .PDF         | Az Adobe Acrobat portable dokumentum                                                                                                                                                                                                                                                |
| HTML              | .HTML, .HTM  | HTML-dokumentum                                                                                                                                                                                                                                                                  |
| Szövegfájl         | .TXT         | UTF-16 vagy UTF-8 kódolású szöveges fájlok                                                                                                                                                                                                                                             |
| Igazított szövegfájl | . IGAZÍTÁS       | A bővítmény `.ALIGN` egy speciális bővítmény, amelyet használhat, ha tudja, hogy a dokumentum pár a mondatok tökéletesen igazított. Ha megad egy `.ALIGN` fájlban, egyéni a fordítót nem igazodnak a mondatok az Ön számára. |
| Excel-fájl        | .XLSX        | Excel-fájlt (2013 vagy újabb). Az első sor / sorát a táblázatban nyelvkód kell lennie.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Szótár formátumok

A szótárak egyéni a fordítót támogatja a minden fájlformátum azok támogatottak, a betanítási készlete. Ha Excel szótár használ, győződjön meg arról, hogy az első sor / sorát a táblában kell lennie a nyelvkódokról.

## <a name="zip-file-formats"></a>Zip-formátumok

Dokumentumok egyetlen zip-fájlban vannak csoportosítva, és feltöltött is. Az egyéni Translator által támogatott formátumok (ZIP GZ és TGZ) zip.

Minden egyes beküldött dokumentumhoz a zip-fájl kiterjesztése a TXT, HTML, HTM-, PDF-, DOCX, IGAZÍTÁS ezt az elnevezési konvenciót kell követnie:

{a dokumentum neve} \_{nyelvkód} ahol a dokumentum neve {dokumentum neve}, {nyelvkód}-e az ISO LanguageID (kettőt), amely azt jelzi, hogy a dokumentum tartalmaz-e azon a nyelven mondatokat. A nyelvi kód előtt aláhúzásjelet (_) kell lennie.

Ha például egy zip belül két párhuzamos dokumentumok feltöltése egy angol, spanyol rendszerre, a fájlok neve "data_en" és "data_es".

Fordítási memória fájlok (TMX, XLF, XLIFF, LCL, XLSX) nem szükségesek az adott nyelv az elnevezési konvenciót követi.  

## <a name="next-steps"></a>További lépések

- Olvassa el a [projekt](workspace-and-project.md#what-is-a-custom-translator-project) hozhat létre és kezelheti azokat.
