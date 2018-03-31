---
title: Stream Analytics Data Lake Store kimeneti |} Microsoft Docs
description: Hitelesítési és engedélyezési egy Azure Data Lake store a Stream Analytics-feladatok konfigurálása
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ea5baafa-0054-4c70-973a-6a3a8c6eaffc
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 802193b9f15cbc35bec32191ccc63df3329b96f9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-data-lake-store-output"></a>Stream Analytics Data Lake Store kimeneti
Stream Analytics-feladatok támogatja több kimeneti módszerek közül az egyik egy [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/). Az Azure Data Lake Store egy vállalati szintű, nagy kapacitású adattár a big data koncepción alapuló adatelemzési célokra. Data Lake Store lehetővé teszi, hogy a műveleti és felderítési jellegű bármilyen méretű, típusú és feldolgozási sebességű adatok.

## <a name="authorize-a-data-lake-store-account"></a>Data Lake Store-fiók engedélyezése
1. Data Lake Store az Azure portálon kimenetként kiválasztásakor kérni fogja a meglévő Data Lake Store használatának engedélyezéséhez, vagy kérjen hozzáférést a Data Lake Store.
   
   ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.png)  
   
2. Ha már van hozzáférésük Data Lake Store-ba, kattintson a "Most engedélyezése", és egy rövid ideig lap jelenik meg "Engedély átirányítása" jelző. A lap automatikusan bezárul, és lehetővé teszi a Data Lake Store kimeneti konfigurálása lap jelennek meg.

Ha nem feliratkozott a Data Lake Store, kövesse a "Feliratkozás most" hivatkozásra kattintva kezdeményezheti a kérelmet, vagy hajtsa végre a [elindított útmutatást](../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="configure-the-data-lake-store-output-properties"></a>A Data Lake Store kimeneti tulajdonságainak konfigurálása
Miután a Data Lake Store-fiók hitelesítését, a Data Lake Store kimeneti megadhatja a tulajdonságait. Az alábbi táblázat pedig a tulajdonság nevét és azok leírását a Data Lake Store kimeneti konfigurálása.

<table>
<tbody>
<tr>
<td><B>TULAJDONSÁG NEVE</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>A kimeneti Alias</td>
<td>Ez az a lekérdezés kimenete a Data Lake Store a lekérdezésekben használt rövid nevét.</td>
</tr>
<tr>
<td>Data Lake Store-fiók</td>
<td>A tárfiók, ahol küldendő a kimeneti neve. Meg fog jelenni a bejelentkezett felhasználó hozzáféréssel rendelkezik Data Lake Store-fiókok listáját.</td>
</tr>
<tr>
<td>Elérési út előtag mintája [<I>választható</I>]</td>
<td>A megadott Data Lake Store-fiók található a fájl írásához használt elérési. <BR>{date}, {time}<BR>1. példa: mappa1/logs / {date} / {time}<BR>2. példa: mappa1/logs / {date}</td>
</tr>
<tr>
<td>Dátum formátumban [<I>választható</I>]</td>
<td>Ha a dátum jogkivonat a előtag elérési útját, válassza a dátumformátum, amelyben a fájlok vannak rendezve. . Példa: Éééé/hh/nn</td>
</tr>
<tr>
<td>Idő formátumot [<I>választható</I>]</td>
<td>Ha a idő jogkivonat előtag elérési, adja meg az időformátum, amelyben a fájlok vannak rendezve. Jelenleg az egyetlen támogatott érték HH.</td>
</tr>
<tr>
<td>Esemény szerializálási formátum</td>
<td>A kimeneti adatok szerializálási formátum. JSON, CSV és az avro-hoz támogatott.</td>
</tr>
<tr>
<td>Kódolás</td>
<td>Ha a fürt megosztott kötetei szolgáltatás- vagy JSON formátumú, kódolással meg kell adni. Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.</td>
</tr>
<tr>
<td>Elválasztó karakter</td>
<td>Csak a fürt megosztott kötetei szolgáltatás szerializálási alkalmazható. A Stream Analytics számos általánosan használt elválasztó karaktert támogatja a CSV-adatok szerializálása során. Támogatott értékei vesszővel, a pontosvesszővel válassza el, a terület, a lapon és a függőleges vonal.</td>
</tr>
<tr>
<td>Formátum</td>
<td>Csak a JSON-szerializálás alkalmazható. Sorral elválasztott beállítás megadja, hogy a kimeneti azzal, hogy minden JSON-objektum sortöréssel elválasztva kell formázni. A tömb határozza meg, hogy a kimeneti lesznek formázva, egy JSON-objektumok tömbjét.</td>
</tr>
</tbody>
</table>

## <a name="renew-data-lake-store-authorization"></a>Újítsa meg a Data Lake Store engedélyezési
Jelenleg egy korlátozás amikor a hitelesítési jogkivonat kell manuálisan frissíteni kell a Data Lake Store kimenet összes feladat 90 naponta. Szüksége lesz is, ha sikeresen módosította jelszavát, mert a feladat meg lett létrehozva, vagy utolsó hitelesített újra hitelesíteni a Data Lake Store-fiókját. A probléma tünete nincs feladat kimenet és a művelet újbóli engedélyezése szükségességét jelző naplókban hiba.

A probléma megoldásához állítsa le a futó feladat, és nyissa meg a Data Lake Store a kimenetbe. Kattintson az "Újra a portálon" hivatkozásra, és egy rövid ideig lap jelenik meg "Engedély átirányítása..." jelző. A lap automatikusan bezáródik, és ha sikeres, fogja jelezni, "Engedélyezési sikeresen megújítva". Később, majd az oldal alján kattintson a "Mentés" kell, és indítsa újra a feladatot a feladat utolsó befejezési időpontja adatvesztés elkerülése érdekében csak ezután folytatható.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.png)

