---
title: Az Azure-ban – Azure API-FHIR FHIR szolgáltatások kapcsolatos gyakori kérdések (GYIK)
description: Ez a cikk gyakori kérdések FHIR az Azure API-val kapcsolatos gyakori kérdésekre ad választ
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7403a23e236c14d77672d5b80d953b1e11088f8a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824066"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Gyakori kérdések az Azure API FHIR számára

## <a name="storage-location"></a>Tárolási hely

**Az adatok a FHIR mögött&reg; API-k az Azure-ban tárolt?** Igen, az adatokat a felügyelt adatbázisok az Azure-ban vannak tárolva. Az Azure API for FHIR nem biztosít az alapul szolgáló adattár közvetlen hozzáférést.

## <a name="identity-providers"></a>Identitásszolgáltatók

Jelenleg a Microsoft Azure Active Directory Identitásszolgáltatóként.

## <a name="supported-fhir-version"></a>Támogatott FHIR-verzió

Jelenleg tesztenként 3.0.1-es verziójú. Lásd: [támogatott funkciók](fhir-features-supported.md) részleteiről.

## <a name="oss-and-azure-api-for-fhir"></a>Nyílt Forráskódú és az Azure API-FHIR

Mi a különbség az Azure a nyílt forráskódú Microsoft FHIR-kiszolgáló és FHIR az Azure API-hoz? Az Azure API FHIR a a nyílt Forráskódú Microsoft FHIR Server az Azure-ban üzemeltetett és kezelt verziója is. A felügyelt szolgáltatás a Microsoft biztosít minden fenntartása, frissítések stb. Az Azure a nyílt Forráskódú FHIR kiszolgáló fut, ha az alapul szolgáló szolgáltatások közvetlen hozzáféréssel rendelkezik, de Ön felelős is fenntartják, a kiszolgáló és az összes szükséges megfelelőségi munkahelyi frissítése, ha a Phi-k adatok tárolására.

## <a name="next-steps"></a>További lépések

Ez a cikk az Azure API – gyakori kérdések a FHIR némelyike elolvasta. A Microsoft FHIR server támogatott API-funkciókkal olvassa el az Azure-hoz.
 
>[!div class="nextstepaction"]
>[Támogatott FHIR-funkciók](fhir-features-supported.md)