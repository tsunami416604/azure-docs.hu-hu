---
title: Dokumentumformátumok és elnevezési konvenciók – Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez az útmutató a dokumentumformátumokról és az egyéni fordító névhasználati konvencióiról szól. Ez a koncepció segít a dokumentumok nevének jobb kezeléséhez, elkerülve az elnevezési ütközéseket.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595848"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Dokumentumformátumok és elnevezési konvenciókra vonatkozó útmutatás

Az egyéni fordításhoz használt fájloknak legalább **négy** karakter hosszúságúnak kell lenniük.

Ez a táblázat tartalmazza az összes támogatott fájlformátumot, amelyet a fordítási rendszer felépítéséhez használhat:

| Formátum            | Bővítmények   | Leírás                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | . XLF, . XLIFF | Párhuzamos dokumentumformátum, fordítási memória rendszerek exportálása. A használt nyelvek a fájlon belül vannak definiálva.                                                                                                                                                              |
| Tmx               | . Tmx         | Párhuzamos dokumentumformátum, fordítási memória rendszerek exportálása. A használt nyelvek a fájlon belül vannak definiálva.                                                                                                                                                              |
| Zip               | . Zip         | A ZIP egy archív fájlformátum.                                                                                                                                                                                                        |
| Locstudio között         | . LCL         | Párhuzamos dokumentumok Microsoft-formátuma                                                                                                                                                                                                                                      |
| Microsoft Word    | . Docx        | Microsoft Word dokumentum                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . Pdf         | Adobe Acrobat hordozható dokumentum                                                                                                                                                                                                                                                |
| HTML              | . Html. Htm  | HTML-dokumentum                                                                                                                                                                                                                                                                  |
| Szövegfájl         | . Txt         | UTF-16 vagy UTF-8 kódolású szövegfájlok. A fájlnév nem tartalmazhat japán karaktereket.                                                                                                                                                                                        |
| Igazított szövegfájl | . Igazítása       | A `.ALIGN` kiterjesztés egy speciális kiterjesztés, amelyet akkor használhat, ha tudja, hogy a dokumentumpár mondatai tökéletesen illeszkednek egymáshoz. Ha megad egy `.ALIGN` fájlt, az Egyéni fordító nem fogja összehangolni a mondatokat. |
| Excel-fájl        | . Xlsx        | Excel-fájl (2013-as vagy újabb). A számolótábla első sorának/sorának nyelvkódnak kell lennie.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Szótárformátumok

Szótárak esetén a Custom Translator támogatja a betanítási készletek által támogatott összes fájlformátumot. Excel szótár használata esetén a számolótábla első sorának/sorának nyelvkódnak kell lennie.

## <a name="zip-file-formats"></a>Zip fájlformátumok

A dokumentumok egyetlen zip fájlba csoportosíthatók és feltölthetők. Az egyéni fordító támogatja a zip fájlformátumokat (ZIP, GZ és TGZ).

Minden dokumentum a zip fájl kiterjesztése TXT, HTML, HTM, PDF, DOCX, ALIGN követnie kell ezt az elnevezési konvenció:

{dokumentum név} \_{language code} ha a(z) {document name} a dokumentum neve, a(z) {language code} az ISO LanguageID (két karakter), amely azt jelzi, hogy a dokumentum az adott nyelven lévő mondatokat tartalmaz. A nyelvkód előtt aláhúzásjelnek (_) kell lennie.

Ha például két párhuzamos dokumentumot szeretne feltölteni egy zip-en belül egy angol-spanyol rendszer hez, a fájlokat "data_en" és "data_es" néven kell elnevezni.

FordításI memóriafájlok (TMX, XLF, XLIFF, LCL, XLSX) nem szükségesek az adott nyelv elnevezési konvenciójának követéséhez.  

## <a name="next-steps"></a>További lépések

- A [projektről](workspace-and-project.md#what-is-a-custom-translator-project) a létrehozásukhoz és kezelésükhöz olvashat.
