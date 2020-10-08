---
title: Az Exportálás végrehajtása $export parancs meghívásával a FHIR készült Azure API-ban
description: Ez a cikk a de azonosított exportálás beállítását és használatát ismerteti.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 433d3391e7b2b99d72ea71f1b4d48c3e04a46afc
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819978"
---
# <a name="how-to-export-fhir-data"></a>FHIR-adatexportálás

A tömeges exportálás funkció lehetővé teszi az adatok exportálását a FHIR-kiszolgálóról a [FHIR-specifikáció](https://hl7.org/fhir/uv/bulkdata/export/index.html)alapján. 

$Export használata előtt meg kell győződnie arról, hogy a FHIR készült Azure API használatára van konfigurálva. Az exportálási beállítások konfigurálásához és az Azure Storage-fiók létrehozásához tekintse meg [Az adatexportálás konfigurálása lapot](configure-export-data.md).

## <a name="using-export-command"></a>$export parancs használata

Miután konfigurálta az Azure API-t a FHIR-hoz az exportáláshoz, a $export paranccsal exportálhatja a szolgáltatásból az adatkészletet. Az adattárolási szolgáltatás az Exportálás konfigurálásakor megadott Storage-fiókba kerül. Ha meg szeretné tudni, hogyan hívhat meg $export parancsot a FHIR-kiszolgálón, olvassa el a [$export specifikáció](https://hl7.org/Fhir/uv/bulkdata/export/index.html)dokumentációját. 

A FHIR készült Azure API-ban található $export parancs egy opcionális _ \_ tároló_ paramétert használ, amely meghatározza azt a tárolót, amely a konfigurált Storage-fiókban található, ahol az adatexportálást el kell helyezni. Ha meg van adva egy tároló, a rendszer az adott tárolóba exportálja az adattárolót egy új mappába a tároló nevével. Ha nincs megadva tároló, a rendszer az új tárolóba exportálja az adatkészletet.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

## <a name="supported-scenarios"></a>Támogatott esetek

A FHIR készült Azure API a rendszer, a beteg és a csoport szintjén támogatja a $export. A csoportos exportálás esetében az összes kapcsolódó erőforrást exportáljuk, de a csoport jellemzőit nem exportáljuk.

> [!Note] 
> $export akkor exportálja az ismétlődő erőforrásokat, ha az erőforrás egynél több erőforrás rekeszében van, vagy több csoportban van.

Emellett az Exportálás állapotát a hely fejléce által visszaadott URL-cím alapján is ellenőrizheti, és a tényleges exportálási feladat megszakításával is támogatott.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan exportálhatja a FHIR-erőforrásokat $export parancs használatával. Következő lépésként tekintse át a támogatott funkciókat
 
>[!div class="nextstepaction"]
>[Támogatott funkciók](fhir-features-supported.md)
