---
title: Az exportálási beállítások konfigurálása a FHIR készült Azure API-ban
description: Ez a cikk azt ismerteti, hogyan konfigurálható az exportálási beállítások a FHIR készült Azure API-ban
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871805"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>Az exportálási beállítás konfigurálása és az adattárolás egy Storage-fiókba való exportálása

A FHIR készült Azure API támogatja a $export parancsot, amely lehetővé teszi az Azure API-ból FHIR-fiókból származó adatexportálást egy Storage-fiókba.

Az Azure API FHIR való exportálásának végrehajtása négy lépésből áll:

1. Felügyelt identitás engedélyezése a FHIR Service-hez készült Azure API-ban
2. Azure Storage-fiók létrehozása (ha még nem történt meg) és engedélyek kiosztása az Azure API-hoz FHIR a Storage-fiókhoz
3. A Storage-fiók kiválasztása az Azure API-ban a FHIR exportálási fiókként
4. Az Exportálás végrehajtása $export parancs meghívásával a FHIR készült Azure API-ban

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Felügyelt identitás engedélyezése a FHIR készült Azure API-ban

Az Azure API FHIR-hez való konfigurálásának első lépése az, hogy a rendszer széles körben felügyelt identitást engedélyezzen a szolgáltatásban. Az Azure-ban felügyelt identitásokról [itt](../active-directory/managed-identities-azure-resources/overview.md)olvashat bővebben.

Ehhez navigáljon az Azure API FHIR szolgáltatáshoz, és válassza az Identity (identitás) panelt. Ha a be állapotra vált, a felügyelt identitást engedélyez az Azure API-ban a FHIR szolgáltatásban.

![Felügyelt identitás engedélyezése](media/export-data/fhir-mi-enabled.png)

Most térjünk át a következő lépésre, és hozzon létre egy Storage-fiókot, és rendeljen hozzá egy engedélyt a szolgáltatáshoz.

## <a name="adding-permission-to-storage-account"></a>Engedély hozzáadása a Storage-fiókhoz

Az Exportálás következő lépése a FHIR szolgáltatáshoz tartozó Azure API-nak a Storage-fiókba való írásához szükséges engedélyek kiosztása.

A Storage-fiók létrehozása után navigáljon a Storage-fiók Access Control (IAM) paneljére, és válassza a szerepkör-hozzárendelések hozzáadása elemet.

![Felügyelt identitás engedélyezése](media/export-data/fhir-export-role-assignment.png)

Itt hozzáadjuk a szerepkör-tároló blob-adatközreműködőjét a szolgáltatás nevéhez.

![Felügyelt identitás engedélyezése](media/export-data/fhir-export-role-add.png)

Most már készen áll a következő lépésre, ahol kiválaszthatja a Storage-fiókot az Azure API-ban a FHIR alapértelmezett Storage-fiókként a $export számára.

## <a name="selecting-the-storage-account-for-export"></a>$export Storage-fiók kiválasztása

A $export parancs meghívása előtt az utolsó lépés az Azure Storage-fiók társítása, amelyet az Azure API FHIR használ majd az adatexportáláshoz. Ehhez navigáljon az integráció panelre az Azure API-ban a FHIR szolgáltatáshoz Azure Portal és válassza ki a Storage-fiókot. 

![Felügyelt identitás engedélyezése](media/export-data/fhir-export-storage.png)

Ezután készen áll az adatexportálásra $export parancs használatával.

## <a name="exporting-the-data-using-export-command"></a>Az adatexportálás $export parancs használatával

Miután konfigurálta az Azure API-t a FHIR exportáláshoz, most már megteheti a $export parancsot, hogy a szolgáltatásból exportálja a megadott Storage-fiókba a szolgáltatást. Ha meg szeretné tudni, hogyan hívhat meg $export parancsot a FHIR-kiszolgálón, olvassa el a dokumentációt a $export specifikációjában:[https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)

> [!IMPORTANT]
> Vegye figyelembe, hogy a FHIR-hez készült Azure API jelenleg csak a rendszerszintű exportálást támogatja az exportálási specifikációban meghatározottak szerint [https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html) . A lekérdezési paramétereket jelenleg nem támogatja a $export.

>[!div class="nextstepaction"]
>[További beállítások](azure-api-for-fhir-additional-settings.md)