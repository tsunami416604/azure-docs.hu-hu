---
title: Dokumentumformátum importálására vonatkozó irányelvek – QnA Maker
description: Ismerje meg, hogyan használják az URL-ek típusait a QnA-készletek importálásához és létrehozásához.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 1e006520ce5c61f2da3f425af2332b0d41cc3881
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754909"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Az importált dokumentumok és URL-címek formázási irányelvei

Tekintse át ezeket a formázási irányelveket, hogy a legjobb eredményt kapja a tartalomhoz.

## <a name="formatting-considerations"></a>Formázási szempontok

Fájl vagy URL importálása után a QnA Maker a tartalmat [markdown formátumban](https://en.wikipedia.org/wiki/Markdown)konvertálja és tárolja. Az átalakítási folyamat új sorokat ad hozzá a szöveghez, például `\n\n`a . A markdown formátum ismerete segít megérteni a konvertált tartalmat és kezelni a tudásbázis tartalmát.

Ha a tartalmat közvetlenül a tudásbázisban adja hozzá vagy szerkeszti, **a markdown formázással** rich text tartalmat hozhat létre, vagy módosíthatja a válaszban már meglévő markdown formátumú tartalmat. A QnA Maker a markdown formátum nagy részét támogatja, hogy rich text funkciókat nyújtson a tartalomhoz. Előfordulhat azonban, hogy az ügyfélalkalmazás, például egy csevegőrobot nem támogatja ugyanazokat a markdown-formátumokat. Fontos, hogy tesztelje az ügyfélalkalmazás a válaszok megjelenítését.

Tekintse meg a [tartalomtípusok és példák](./Concepts/content-types.md#file-and-url-data-types)teljes listáját.

## <a name="basic-document-formatting"></a>Egyszerű dokumentumformázás

A QnA Maker vizuális nyomok alapján azonosítja a fájlokban található szakaszokat, alszakaszokat és kapcsolatokat:

* Betűméret
* Betűstílus
* Számozás
* Színek

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

**Mintadokumentumok**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso előnyei (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso előnyei (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>További lépések

A [tartalomtípusok és példák](./Concepts/content-types.md#file-and-url-data-types) teljes listájának megtekintése