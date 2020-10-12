---
title: Azure-támogatáskérés kezelése
description: Ismerteti a támogatási kérések megtekintését, az üzenetek küldését, a kérés súlyossági szintjének megváltoztatását, a diagnosztikai adatok megosztását az Azure-támogatással, a lezárt támogatási kérelem ismételt megnyitását és a fájlok feltöltését.
author: mgblythe
tags: billing
ms.assetid: 86697fdf-3499-4cab-ab3f-10d40d3c1f70
ms.service: azure-supportability
ms.topic: how-to
ms.date: 06/30/2020
ms.author: mblythe
ms.openlocfilehash: f3b4806bf46750d74a54f68bd2ab58e402e75091
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85852296"
---
# <a name="manage-an-azure-support-request"></a>Azure-támogatáskérés kezelése

Miután [létrehozta az Azure-támogatási kérést](how-to-create-azure-support-request.md), felügyelheti azt a [Azure Portalban](https://portal.azure.com), amely ebben a cikkben is szerepel. A kéréseket programozott módon is létrehozhatja és kezelheti az [Azure támogatási jegy REST API](/rest/api/support)használatával.

## <a name="view-support-requests"></a>Támogatási kérelmek megtekintése

Tekintse meg a támogatási kérések részleteit és állapotát a **Súgó + támogatási kérelmek támogatásával**  >   **All support requests**.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Minden támogatási kérelem":::

Ezen a lapon megkeresheti, szűrheti és rendezheti a támogatási kérelmeket. Válasszon ki egy támogatási kérést a részletek megtekintéséhez, beleértve annak súlyosságát és a kéréshez kapcsolódó üzeneteket.

## <a name="send-a-message"></a>Üzenet küldése

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** lapon válassza az **új üzenet**elemet.

1. Adja meg az üzenetet, és válassza a **Küldés**lehetőséget.

## <a name="change-the-severity-level"></a>Súlyossági szint módosítása

> [!NOTE]
> A maximális súlyossági szint a [támogatási csomagtól](https://azure.microsoft.com/support/plans)függ.
>

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** lapon válassza a **módosítás**lehetőséget.

    :::image type="content" source="media/how-to-manage-azure-support-request/change-severity.png" alt-text="Minden támogatási kérelem":::

1. A Azure Portal a két képernyő egyikét jeleníti meg attól függően, hogy a kérés már hozzá van-e rendelve egy támogatási szakemberhez:

    - Ha a kérelem még nincs hozzárendelve, a következőhöz hasonló képernyő jelenik meg. Válasszon új súlyossági szintet, majd kattintson a **módosítás**gombra.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Minden támogatási kérelem":::

    - Ha a kérelem hozzá van rendelve, a következőhöz hasonló képernyő jelenik meg. Kattintson **az OK gombra**, majd hozzon létre egy [új üzenetet](#send-a-message) a súlyossági szint módosításának kéréséhez.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Minden támogatási kérelem":::

## <a name="share-diagnostic-information-with-azure-support"></a>Diagnosztikai információk megosztása az Azure-támogatással

Ha támogatási kérelmet hoz létre, alapértelmezés szerint a **megosztás diagnosztikai információi** beállítás van kiválasztva. Ez lehetővé teszi, hogy az Azure-támogatás [diagnosztikai adatokat](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) gyűjtsön az Azure-erőforrásokból:

* A kérés létrehozása után ezt a beállítást nem lehet törölni.

* Ha a kérés létrehozásakor törölte a beállítást, akkor a kérés létrehozása után kiválaszthatja azt.

    1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.
    
    1. A **támogatási kérelem** lapon válassza az **engedély megadása**lehetőséget, majd válassza az **Igen** , majd **az OK gombot**.
    
        :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Minden támogatási kérelem":::

## <a name="upload-files"></a>Fájlok feltöltése

A file upload (fájlfeltöltés) lehetőséggel feltölthet olyan diagnosztikai fájlokat vagy más fájlokat, amelyekre a támogatási kérelem vonatkozik.

1. A **minden támogatási kérelem** lapon válassza ki a támogatási kérelmet.

1. A **támogatási kérelem** oldalon tallózással keresse meg a fájlt, majd válassza a **feltöltés**lehetőséget. Ha több fájllal rendelkezik, ismételje meg a folyamatot.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Minden támogatási kérelem":::

### <a name="file-upload-guidelines"></a>Fájlfeltöltés-irányelvek

A fájlfeltöltés lehetőség használata esetén kövesse az alábbi irányelveket:

* Az adatvédelem védelme érdekében ne adjon meg személyes adatokat a feltöltéshez.
* A fájl neve nem lehet hosszabb 110 karakternél.
* Legfeljebb egy fájlt tölthet fel.
* A fájlok nem lehetnek nagyobbak 4 MB-nál.
* Minden fájlnak rendelkeznie kell fájlnévkiterjesztés (például *. docx* vagy *. xlsx*). A következő táblázat a feltöltésre jogosult filename-bővítményeket tartalmazza.

| 0-9, A – C    | D-G   | H-M         | N – P   | R-T      | U-W        | X – Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | . dat  | .hwl        | . ODX  | . rar     | . TDB       | .xlam   |
| . a          | . db   | . ics        | . gyakran  | . rdl     | . TDF       | . XLR    |
| . ABC        | . Eszközkezelő pont –  | . ini        | . régi  | . RDLC    | . Text      | .xls    |
| . adm        | .do_  | .java       | . egy  | .re_     | .thmx      | .xlsb   |
| . aspx       | .doc  | .jpg        | . OSD  | . reg     | .tif       | .xlsm   |
| . ATF        | .docm | . LDF        | . KI  | . Eltávolítás  | . TRC       | .xlsx   |
| . b          | .docx | . levélpapírfejléc | . P1   | . ren     | . TTD       | .xlt    |
| .ba_        | .dotm | . lnk        | . Pcap | . Átnevezés  | .tx_       | .xltx   |
| . bak        | .dotx | .lo_        | . pdb  | . RFT     | .txt       | .xml    |
| .bat        | .dtsx | . log        | .pdf  | . RPT     | .uccapilog | . XMLA   |
| . blg        | . EDS  | . LPK        | . Piz  | . RTE     | .uccplog   | .xps    |
| .CA_        | . EMF  | . manifest   | .pmls | .rtf     | . udcx      | . xsd    |
| . CAB        | . EML  | . Master     | .png  | . Run     | .vb_       | . xsn    |
| . Cap        | . a. a  | . mdmp       | .potx | . saz     | .vbs_      | . xxx    |
| .catx       | . err  | . MOF        | .ppt  | .sql     | . vcf       | .z_     |
| . CFG        | . etl  | . mp3        | .pptm | .sqlplan | . vsd       | . Z01    |
| . tömörített | . evt  | . mpg        | .pptx | . stp     | . wdb       | .z02    |
| . Config     | . evtx | .ms_        | . prn  | .svclog  | . WKS       | . zi     |
| . CPK        | . PÉLDÁUL   | . msg        | . PSF  |   -       | . WMA       | .zi_    |
| . cpp        | .ex_  | .msi        | . pst  |  -        | . wmv       | .zip    |
| .cs         | .ex0  | . MSO        | . pub  | -         | . WMZ       | .zip_   |
| . CSV        | . FRD  | . msu        | -      |-          | . wps       | .zipp   |
| . CVR        | .gif  | . nfo        | -      |-          | . WPT       | . zip |
| -            | . GUID | -            | -      | -         | . WSDL      | . villámgyors  |
| -            | . gz   | -            | -      | -         | . wsp       | . ZIPX   |
| -            | -      | -            | -      | -         | .wtl       | . zit    |
| -            | -      | -            | -      | -         |     -       | .zix    |
| -            | -      | -            | -      | -         |  -          | . ZZZ    |

## <a name="reopen-a-closed-request"></a>Lezárt kérelem újbóli megnyitása

Ha újra meg kell nyitnia egy lezárt támogatási kérelmet, hozzon létre egy [új üzenetet](#send-a-message), amely automatikusan újra megnyitja a kérést.

## <a name="next-steps"></a>További lépések

[Azure-támogatáskérések létrehozása](how-to-create-azure-support-request.md)

[Azure-beli támogatási jegy REST API](/rest/api/support)
