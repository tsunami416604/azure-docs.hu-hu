---
title: Az Exportálás végrehajtása $export parancs meghívásával a FHIR készült Azure API-ban
description: Ez a cikk a de azonosított exportálás beállítását és használatát ismerteti.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 10d901f73006051e8b1ddd02aeb36b229c6a7761
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270144"
---
# <a name="how-to-export-fhir-data"></a>FHIR-adatexportálás

Az exportálási beállítások konfigurálásához és az Azure Storage-fiók létrehozásához tekintse meg a következőt [:.](configure-export-data.md)

## <a name="exporting-fhir-resources-using-export-command"></a>FHIR-erőforrások exportálása a $export paranccsal

Miután konfigurálta az Azure API-t a FHIR exportáláshoz, most már megteheti a $export parancsot, hogy exportálja a szolgáltatásból kifelé irányuló adatmennyiséget az Exportálás konfigurálásakor megadott Storage-fiókba. Ha szeretné megtudni, hogyan hívhat meg $export parancsot a FHIR-kiszolgálón, tekintse meg a dokumentációt a $export-specifikációról a következő címen: [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . 

A FHIR készült Azure API $export parancsa egy opcionális _ \_ Conatiner_ paramétert is igénybe vehet, amely segítségével megadhatja a tárolót a konfigurált Storage-fiókon belül, amelybe exportálni kívánja az adatexportálást.

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> Vegye figyelembe, hogy a FHIR-hez készült Azure API jelenleg csak a rendszerszintű exportálást támogatja $export specifikációban meghatározottak szerint [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . Emellett csak a _ \_ _ lekérdezési paraméter használata támogatott.

## <a name="exporting-de-identified-data-preview"></a>De azonosított adatexportálás (előzetes verzió)

A $export parancs a FHIR-kiszolgálóról származó, de azonosított adatok exportálására is használható. A névtelenítésével motort használja a [névtelenítésével FHIR eszközökről](https://github.com/microsoft/FHIR-Tools-for-Anonymization), és a lekérdezési paraméterekben a névtelenítésével konfiguráció részleteit veszi igénybe. Létrehozhatja saját névtelenítésével-konfigurációs fájlját, vagy kiindulási pontként használhatja a HIPAA Safe Harbor metódusának [minta konfigurációs fájlját](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method) . 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Lekérdezési paraméter            | Példa |Nem kötelező| Leírás|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.jsbekapcsolva|A deazonosított exportáláshoz szükséges |A konfigurációs fájl neve. Tekintse meg a konfigurációs fájlformátumot [itt](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Ezt a fájlt egy **névtelenítésével** nevű tárolóban kell tárolni, amely az exportálási helyként konfigurált Azure Storage-fiókban található. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Nem kötelező a azonosított exportáláshoz|Ez a konfigurációs fájl ETAG. A ETAG az Azure Storage Explorer használatával kérheti le a blob tulajdonságból.|

> [!IMPORTANT]
> Vegye figyelembe, hogy mind a nyers, mind a nem azonosított Exportálás az exportálási konfiguráció részeként megadott Azure Storage-fiókra is vonatkozik. Azt javasoljuk, hogy a különböző, de azonosított konfigurációnak megfelelő tárolókat használjon, és kezelje a felhasználói hozzáférést a tároló szintjén.
