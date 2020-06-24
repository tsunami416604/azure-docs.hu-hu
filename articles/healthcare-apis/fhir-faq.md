---
title: Az Azure-beli FHIR-szolgáltatásokkal kapcsolatos gyakori kérdések – Azure API for FHIR
description: Válaszok a FHIR készült Azure API-val kapcsolatos gyakori kérdésekre, például a FHIR API-k mögötti adattárolási helyhez és a verziók támogatásához.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: cdec5ade4fc72fba6fcfba131b69ca9eb373874c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "84871777"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>A FHIR készült Azure API-val kapcsolatos gyakori kérdések

## <a name="what-is-fhir"></a>Mi az a FHIR?
A gyors egészségügyi együttműködési erőforrások (FHIR – ejtsd: "Fire") egy együttműködési szabvány, amely lehetővé teszi az egészségügyi információk különböző egészségügyi rendszerek közötti cseréjét. Ezt a standardot a HL7-szervezet fejlesztette ki, és az egészségügyi szervezetek az egész világon elfogadják. A FHIR legújabb verziója az R4 (4. kiadás). A FHIR készült Azure API támogatja az R4-et, és az előző verzió STU3 is támogatja (a standard próbaverziós használatra 3). A FHIR kapcsolatos további információkért látogasson el a [HL7.org](http://hl7.org/fhir/summary.html)webhelyre.

## <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Az Azure-ban tárolt FHIR API-k mögött vannak az adathalmazok?

Igen, az Azure-ban felügyelt adatbázisok tárolják az adattárolást. A FHIR készült Azure API nem biztosít közvetlen hozzáférést a mögöttes adattárhoz.

## <a name="what-identity-provider-do-you-support"></a>Milyen identitás-szolgáltatót támogat?

Jelenleg az identitás-szolgáltatóként támogatja a Microsoft Azure Active Directory.

## <a name="what-fhir-version-do-you-support"></a>Milyen FHIR-verziót támogat?

A 4.0.0 és az 3.0.1-es verziókat a FHIR (FHIR-kiszolgáló) Azure API-ra és az Azure-ra (nyílt forráskód) is támogatja.

Részletekért lásd: [támogatott szolgáltatások](fhir-features-supported.md). Olvassa el, hogy mi változott a verziók között a [HL7 FHIR verziójának korábbi](https://hl7.org/fhir/R4/history.html)verzióiban.

## <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Mi a különbség a nyílt forráskódú Microsoft FHIR Server for Azure és az Azure API for FHIR között?

A FHIR készült Azure API az Azure-beli nyílt forráskódú Microsoft FHIR-kiszolgáló üzemeltetett és felügyelt verziója. A felügyelt szolgáltatásban a Microsoft biztosítja az összes karbantartást és frissítést. 

Ha a FHIR-kiszolgálót az Azure-hoz futtatja, közvetlen hozzáférést biztosít a mögöttes szolgáltatásokhoz. Azonban Ön is felelős a kiszolgáló karbantartásához és frissítéséhez, valamint az összes szükséges megfelelőségi munkához, ha a PHI-adatokat tárolja.

Fejlesztési szempontból minden funkció üzembe kerül az Azure nyílt forráskódú Microsoft FHIR-kiszolgálója számára. A nyílt forráskódú hitelesítés után a rendszer felveszi a FHIR-megoldáshoz tartozó, a Pásti Azure API-ra. A nyílt forráskódú és a Pásti kiadás közötti idő a szolgáltatás összetettsége és az ütemterv egyéb prioritásaitól függ. 

## <a name="what-is-smart-on-fhir"></a>Mi a FHIR intelligens?

Az intelligens (behelyettesíthető orvosi alkalmazások és újrafelhasználható technológia) a FHIR-ben olyan Nyílt specifikációk összessége, amelyek a FHIR-kiszolgálókkal és más egészségügyi informatikai rendszerekkel, például az elektronikus egészségügyi adatokkal és az állapotadatok cseréjével integrálják a partneri alkalmazásokat. Egy intelligens on FHIR alkalmazás létrehozásával gondoskodhat arról, hogy az alkalmazás elérhető legyen, és számos különböző rendszerből is kihasználható legyen.
Hitelesítés és Azure API a FHIR-hez. Ha többet szeretne megtudni az intelligens szolgáltatásról, látogasson el az [intelligens állapotba](https://smarthealthit.org/).

## <a name="next-steps"></a>További lépések

Ebben a cikkben elolvasta a FHIR készült Azure API-val kapcsolatos gyakori kérdéseket. További információ a FHIR Server for Azure támogatott szolgáltatásairól:
 
>[!div class="nextstepaction"]
>[Támogatott FHIR funkciók](fhir-features-supported.md)