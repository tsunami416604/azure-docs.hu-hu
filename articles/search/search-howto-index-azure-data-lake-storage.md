---
title: Keresés az Azure Data Lake Storage Gen2 webhelyen (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelhet tartalmakat és metaadatokat az Azure Data Lake Storage Gen2 szolgáltatásban. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905659"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Dokumentumok indexelése az Azure Data Lake Storage Gen2 szolgáltatásban

> [!IMPORTANT] 
> Az Azure Data Lake Storage Gen2 támogatása jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével hozzáférést kérhet az előnézetekhez. A [REST API 2019-05-06-Preview verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál- vagy .NET SDK-támogatás.


Az Azure storage-fiók beállításakor engedélyezheti a [hierarchikus névteret.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) Ez lehetővé teszi, hogy egy fiók ban lévő tartalom gyűjteménye könyvtárak és beágyazott alkönyvtárak hierarchiájába szerveződjön. A hierarchikus névtér engedélyezésével engedélyezi [az Azure Data Lake Storage Gen2 szolgáltatást.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)

Ez a cikk ismerteti, hogyan ismerkedhet meg az Azure Data Lake Storage Gen2-ben található indexelési dokumentumokkal.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Az Azure Data Lake Storage Gen2 indexelő beállítása

A Data Lake Storage Gen2 tartalomindexeléséhez néhány lépést kell végrehajtania.

### <a name="step-1-sign-up-for-the-preview"></a>1. lépés: Regisztráljon az előzetes verzióra

Regisztráljon a Data Lake Storage Gen2 indexelő előzetes verziójára [az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével. A visszaigazoló e-mailt akkor kapja meg, ha már elfogadta az előzetes verziót.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>2. lépés: Kövesse az Azure Blob storage indexelésének beállítási lépéseit

Miután megkapta a megerősítést arról, hogy az előzetes verzióra való regisztráció sikeres volt, készen áll az indexelési folyamat létrehozására.

A Data Lake Storage Gen2 tartalmát és metaadatait a [REST API 2019-05-06-preview verziójával](search-api-preview.md)indexelheti. Jelenleg nincs portál- vagy .NET SDK-támogatás.

A Data Lake Storage Gen2 tartalom indexelése megegyezik az Azure Blob storage-ban lévő tartalom indexelésével. A Data Lake Storage Gen2 adatforrás, index és indexelő beállításának megismeréséhez tekintse meg [a Dokumentumok indexelése](search-howto-indexing-azure-blob-storage.md)az Azure Cognitive Search szolgáltatással című dokumentumindexet az Azure Blob Storage ban című dokumentumként című részben. A Blob storage cikk is tájékoztatást nyújt, hogy milyen dokumentumformátumok támogatottak, milyen blob metaadat-tulajdonságok kibontása, növekményes indexelés és így tovább. Ez az információ ugyanaz lesz a Data Lake Storage Gen2 esetében.

## <a name="access-control"></a>Hozzáférés-vezérlés

Az Azure Data Lake Storage Gen2 olyan [hozzáférés-vezérlési modellt](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) valósít meg, amely támogatja az Azure szerepköralapú hozzáférés-vezérlési (RBAC) és a POSIX-szerű hozzáférés-vezérlési listákat (ACL-ek). A Data Lake Storage Gen2 tartalom indexelésekor az Azure Cognitive Search nem nyeri ki az RBAC- és ACL-adatokat a tartalomból. Ennek eredményeképpen ezek az információk nem fognak szerepelni az Azure Cognitive Search indexében.

Ha fontos a hozzáférés-vezérlés fenntartása az index minden dokumentumán, az alkalmazás fejlesztőjének kell végrehajtania a [biztonsági vágást.](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)

## <a name="change-detection"></a>Változásészlelés észlelése

A Data Lake Storage Gen2 indexelő támogatja a változások észlelését. Ez azt jelenti, hogy az indexelő futtatásakor csak újraindexeli `LastModified` a módosított blobok a blob időbélyege határozza meg.

> [!NOTE] 
> A Data Lake Storage Gen2 lehetővé teszi a könyvtárak átnevezését. Amikor egy könyvtár átnevezése a blobok a könyvtárban nem frissülnek. Ennek eredményeképpen az indexelő nem indexeli ezeket a blobokat. Ha szüksége van a blobok egy könyvtárban kell újraindexelt egy könyvtár átnevezése után, `LastModified` mert most már új URL-címeket, frissítenie kell az időbélyeget az összes blobok a könyvtárban, hogy az indexelő tudja, hogy újraindexeljük őket egy későbbi futtatás során.
