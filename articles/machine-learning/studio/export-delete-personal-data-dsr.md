---
title: Exportálás és az adatokat törölni a Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Terméken belüli, az Azure Machine Learning Studio által tárolt adatok exportálása és törlése az Azure Portalon keresztül és hitelesített REST API-kon keresztül érhető el. Telemetriai adatokat az Azure adatvédelmi portálon keresztül érhető el. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: 49f045e8da5094f583c01c514bf92f6bf3fe1c09
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453358"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio"></a>Exportálás és terméken belüli felhasználói adatok törlése az Azure Machine Learning Studióban

Törölheti vagy a terméken belüli adatok exportálása az Azure Portalon, a Studio felületén, a PowerShell, az Azure Machine Learning Studio által tárolt és a hitelesített REST API-k. Ez a cikk bemutatja, hogyan. 

Az adatvédelem az Azure Portalon keresztül elérhető telemetriai adatokat. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Milyen típusú felhasználói adatok gyűjt Studio?

Ezt a szolgáltatást, a felhasználói adatok áll munkaterületek és telemetriai rekordjait felhasználói interakció a szolgáltatás elérésére jogosult felhasználók adatait.

Kétféle felhasználói adatokat a Machine Learning Studio van:
- **Személyes fiók adatok:** Fiók azonosítói és e-mail-címek az adott fiókhoz tartozó.
- **Vásárlói adatok:** Elemezheti a feltöltött adatokat.

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio fióktípus esetében és az adatok tárolási módját

Nincsenek három típusú fiókok a Machine Learning Studióban. Fiók típusa határozza meg, az adatok tárolási módját, és hogyan törölheti, vagy exportálja azt.

- A **Vendég munkaterület** egy ingyenes, névtelen fiók. Jelentkezzen hitelesítő adatok, például e-mail-cím vagy jelszó megadása nélkül.
    -  Adatok törölve van, a Vendég munkaterület lejárata után.
    - Vendégfelhasználók exportálhatja a felhasználói felület, a REST API-k vagy a PowerShell csomag keresztül a vásárlói adatokat.
- A **ingyenes munkaterületet** egy ingyenes fiók bejelentkezik a Microsoft-fiók hitelesítő adatai – egy e-mail címet és jelszót.
    - Exportálhatja, és törölje a személyes és a vásárlói adatokat, amelyek adattulajdonosi jogokra (vonatkozó DSR) kérelmek vonatkoznak.
    - A felhasználói felület, a REST API-k vagy a PowerShell csomag ügyféladat exportálhatja.
    - Ingyenes munkaterületek nem használja az Azure AD-fiókokat, telemetriai adatok exportálhatók a adatvédelmi portál használatával.
    - Ha törli a munkaterületet, minden személyes vásárlói adatokat törli.
- A **standard munkaterületen** egy díjköteles fiók bejelentkezési hitelesítő adatok eléréséhez.
    - Exportálhatja, és törölje a személyes és a vásárlói adatokat, amelyekre kapcsolatos DSR-kérelmekre.
    - Az adatvédelem az Azure Portalon keresztül férhet hozzá adataihoz
    - Exportálhatja a személyes és a vásárlói adatokat a felhasználói felület, a REST API-k vagy a PowerShell-csomag
    - Törölheti az adatokat az Azure Portalon.

## <a name="delete"></a>A Studio munkaterület adatainak törlése 

### <a name="delete-individual-assets"></a>Egyes eszközök törlése

Felhasználók eszközöket egy adott munkaterület törléséhez válassza ki azokat, és válassza a Törlés gombra.

![A Machine Learning Studióban eszközök törlése](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Egy teljes munkaterület törlése

Felhasználók is törölheti a teljes munkaterület:
- Fizetős munkaterület: Törölje az Azure Portalon keresztül.
- Ingyenes munkaterületet: A Törlés gomb a **beállítások** ablaktáblán.

![A Machine Learning Studio ingyenes munkaterület törlése](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>Studio-adatok exportálása a PowerShell-lel
Minden információt az Azure Machine Learning Studio-parancsokkal egy hordozható formátumba exportálhat a PowerShell használatával. További információ: a [PowerShell-modul az Azure Machine Learning Studio](powershell-module.md) cikk.

## <a name="next-steps"></a>További lépések

Webszolgáltatások és számlázási kötelezettségvállalási csomag dokumentációjáért lásd: [Azure Machine Learning Studio REST API-referencia](https://docs.microsoft.com/rest/api/machinelearning/). 
