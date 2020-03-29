---
title: Dokumentumformátum importálására vonatkozó irányelvek – QnA Maker
description: Ismerje meg, hogyan használják az URL-ek típusait a QnA-készletek importálásához és létrehozásához.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651838"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Az importált dokumentumok és URL-címek formázási irányelvei

Tekintse át ezeket a formázási irányelveket, hogy a legjobb eredményt kapja a tartalomhoz.

## <a name="formatting-considerations"></a>Formázási szempontok

Fájl vagy URL importálása után a QnA Maker a tartalmat [markdown formátumban](https://en.wikipedia.org/wiki/Markdown)konvertálja és tárolja. Az átalakítási folyamat új sorokat ad hozzá a szöveghez, például `\n\n`a . A markdown formátum ismerete segít megérteni a konvertált tartalmat és kezelni a tudásbázis tartalmát.

Ha a tartalmat közvetlenül a tudásbázisban adja hozzá vagy szerkeszti, **a markdown formázással** rich text tartalmat hozhat létre, vagy módosíthatja a válaszban már meglévő markdown formátumú tartalmat. A QnA Maker a markdown formátum nagy részét támogatja, hogy rich text funkciókat nyújtson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például egy csevegőrobot nem támogatja ugyanazokat a markdown-formátumokat. Fontos, hogy tesztelje az ügyfélalkalmazás a válaszok megjelenítését.

## <a name="basic-document-formatting"></a>Egyszerű dokumentumformázás

A QnA Maker vizuális nyomok alapján azonosítja a fájlokban található szakaszokat, alszakaszokat és kapcsolatokat:

* Betűméret
* Betűstílus
* Számozás
* Színek

|Dokumentumpéldák|
|--|
||



## <a name="product-manuals"></a>Termékkézikönyvek

A kézikönyv általában egy termékhez kapcsolódó útmutató anyag. Segít a felhasználónak a termék beállítását, használatát, karbantartását és hibaelhárítását. Amikor a QnA Maker feldolgoz egy kézikönyvet, a címsorokat és alcímeket kérdésként, az azt követő tartalmat pedig válaszként kinyeri. Lásd a példát [itt](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Az alábbiakban egy indexlappal és hierarchikus tartalommal rendelkező kézikönyvet mutatunk be

 ![Példa egy tudásbázis termékkézikönyvére](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> A kibontás a tartalomjegyzékkel és/vagy indexlappal, valamint a hierarchikus fejlécekkel rendelkező, tiszta struktúrával rendelkező kézikönyveken működik a legjobban.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brosúrák, irányelvek, papírok és egyéb fájlok

Sok más típusú dokumentumok is feldolgozhatók, hogy létrehoz qa pár, feltéve, hogy van egy világos szerkezet és elrendezés. Ezek a következők: brosúrák, iránymutatások, jelentések, tanulmányok, tudományos könyvek, politikák, könyvek stb. Lásd a példát [itt](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Az alábbiakban egy félszerkezetes dokumentum ravandál, index nélkül:

 ![Félig strukturált Azure Blob storage](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturált minőségbiztosítási dokumentum

A doc-fájlokban a strukturált kérdések és válaszok formátuma soronként váltakozó kérdések és válaszok formájában jelenik meg, soronként egy kérdés, majd a következő sorban található válasz, az alábbiak szerint:

```text
Question1

Answer1

Question2

Answer2
```

Az alábbiakban egy strukturált QnA szódokumentum látható:

 ![Strukturált minőségbiztosítási dokumentum példa egy tudásbázishoz](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturált *TXT*, *TSV* és *XLS* fájlok

A qna-k strukturált *.txt*, *.tsv* vagy *.xls* fájlok formájában is feltölthetők a QnA Maker programba, hogy tudásbázist hozzanak létre vagy bővítsenek.  Ezek lehetnek egyszerű szöveg, vagy rtf-ben vagy HTML-ben lévő tartalom.

| Kérdés  | Válasz  | Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|-------------------------|
| 1. kérdés | Válasz1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 2. kérdés | 2. válasz |      `Key:Value`           |

A forrásfájl további oszlopait a program figyelmen kívül hagyja.

### <a name="example-of-structured-excel-file"></a>Példa strukturált Excel-fájlra

Az alábbiakban egy strukturált QnA *.xls* fájl látható, HTML-tartalommal:

 ![Strukturált minőségbiztosítási hiba például egy tudásbázishoz](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Példa alternatív kérdésekre az egyválaszhoz az Excel-fájlban

Az alábbiakban egy strukturált QnA *.xls* fájlra mutat be példát, amely egyetlen válaszhoz több alternatív kérdést is felvet:

 ![Példa alternatív kérdésekre az egyválaszhoz az Excel-fájlban](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

A fájl importálása után a kérdés-válasz pár a tudásbázisban található az alábbiak szerint:

 ![Képernyőkép a tudásbázisba importált egyválaszos kérdésekre](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturált adatformátum importálás útján

A tudásbázis importálása lecseréli a meglévő tudásbázis tartalmát. Az importáláshoz olyan strukturált .tsv fájlra van szükség, amely adatforrásadatokat tartalmaz. Ez az információ segít a QnA Maker-nek a kérdés-válasz párok csoportosítását és egy adott adatforráshoz való hozzáadását.

| Kérdés  | Válasz  | Forrás| Metaadatok (1 kulcs: 1 érték) |
|-----------|---------|----|---------------------|
| 1. kérdés | Válasz1 | URL1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 2. kérdés | 2. válasz | Szerkesztői|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Többfordulatos dokumentumformázás

* A hierarchiát a címsorok és alfejlécek segítségével jelölje. Például h1 jelöli a szülő QnA és h2 jelöli a QnA, hogy kell venni, mint a prompt. A későbbi hierarchia kis címsorméretével jelölheti a későbbi hierarchiát. Ne használjon stílust, színt vagy más mechanizmust a dokumentum szerkezetének sugallására, a QnA Maker nem bontja ki a többszörös fordulatos utasításokat.
* A címsor első karakterét nagybetűvel kell elévíteni.
* Ne hagyjon kérdőjellel egy `?`címsort.


|Dokumentumpéldák|
|--|
||