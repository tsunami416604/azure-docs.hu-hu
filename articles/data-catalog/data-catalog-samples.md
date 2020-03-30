---
title: Az Azure Data Catalog fejlesztői mintái
description: Ez a cikk áttekintést nyújt a Data Catalog REST API elérhető fejlesztői mintáiról.
ms.service: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7d0e27802745dda62f87e412053650907e9b812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950161"
---
# <a name="azure-data-catalog-developer-samples"></a>Az Azure Data Catalog fejlesztői mintái

Az Azure Data Catalog-alkalmazások fejlesztésének első lépései a Data Catalog REST API használatával. A Data Catalog REST API egy REST-alapú API, amely programozott hozzáférést biztosít a Data Catalog erőforrásaihoz a regisztrációhoz, a jegyzetekhez és az adatelemek programozott kereséséhez.

## <a name="samples-available-on-githubcom"></a>A GitHub.com elérhető minták

* [Ismerkedés az Azure Data Catalog szolgáltatással](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/)
  
   Az első lépések minta bemutatja, hogyan hitelesítheti magát az Azure AD-vel az adateszköz regisztrálásához, kereséséhez és törléséhez a Data Catalog REST API használatával.
   
* [Az Azure Data Catalog használatának első lépései az egyszerű szolgáltatás használatával](https://github.com/Azure-Samples/data-catalog-dotnet-service-principal-get-started/)

   Ez a minta bemutatja, hogyan regisztrálhat, kereshet és törölhet egy adategységet a Data Catalog REST API használatával. Ez a minta az egyszerű szolgáltatás hitelesítését használja.

* [Importálási/exportálási eszköz az Azure Data Catalog-hoz](https://github.com/Azure-Samples/data-catalog-dotnet-import-export/)

   Ez a minta, amely bemutatja, hogyan használhatja a Data Catalog REST API-t az Azure Data Catalog eszközeinek lehívásához és egy fájlba való szerializálásához. Azt is bemutatja, hogyan vehet json-ként szerializált eszközök készletét, és hogyan kell őket a katalógusba bevinni. Támogatja a katalógus egy részhalmazának exportálását egy keresési lekérdezés használatával.

* [Tömeges regisztráció és jegyzet az Azure Data Catalogban](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets/)
  
   Ez a minta bemutatja, hogyan regisztrálhat adatelemeket egy Excel-munkafüzetből a Data Catalog REST API és az Open XML használatával.
  
* [Tömeges importálási szószedetkifejezések az Azure Data Catalog-ba](https://github.com/Azure-Samples/data-catalog-bulk-import-glossary/)

   Ez a minta bemutatja, hogyan importálhat szószedetkifejezéseket csv-fájlokból ADC szószedetbe.

* [Tömeges importálási kapcsolatok az Azure Data Catalog-ba](https://github.com/Azure-Samples/data-catalog-bulk-import-relationship/)

   Ez a minta bemutatja, hogyan importálhat programozott kapcsolati adatokat egy CSV-fájlból egy adatkatalógusba.

* [Kapcsolatok közzététele az Azure Data Catalogban](https://github.com/Azure-Samples/data-catalog-dotnet-publish-relationships/)

   Ez a minta bemutatja, hogyan tehet közzé programozott kapcsolati információkat egy adatkatalógusban.
   
## <a name="next-steps"></a>További lépések
[Az Azure Data Catalog REST API-hivatkozása](/rest/api/datacatalog/)
