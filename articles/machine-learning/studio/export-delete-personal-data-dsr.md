---
title: Exportálás és az adatok törléséhez a Machine Learning Studio - Azure |} Microsoft Docs
description: Azure Machine Learning Studio a terméken belüli adataihoz az exportálás és törlése az Azure portálon keresztül, valamint hitelesített REST API-kon keresztül érhető el. Az Azure adatvédelmi portálon keresztül elérhető telemetriai adatokat. Ez a cikk bemutatja, hogyan.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: 6317d4baba5775c1e5a4fda66de80dd8299d8fed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655155"
---
# <a name="export-and-delete-in-product-user-data-from-machine-learning-studio"></a>Exportálja és terméken belüli felhasználói adatok törléséhez a Machine Learning Studióban

Törölheti vagy a terméken belüli adatok exportálása az Azure-portálon a Studio felületén, a PowerShell használatával Azure Machine Learning Studio által tárolt és hitelesített REST API-k. Ez a cikk azt ismerteti, hogyan. 

Telemetriai adatok elérhetők az adatvédelmi Azure portálon keresztül. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-collect"></a>Milyen típusú felhasználói adatok gyűjt Studio?

A szolgáltatás felhasználói adatok áll felhasználói férhetnek hozzá a munkaterületek és a szolgáltatás felhasználói interakció rekordjának telemetriai adatokat.

Felhasználói adatokat a Machine Learning Studio két fő típusba sorolhatók:
- **Személyes fiók adatait:** fiók azonosítókat és az adott fiókhoz tartozó e-mail címet.
- **Ügyféladatok:** elemzéséhez feltöltött adatok.

## <a name="studio-account-types-and-how-data-is-stored"></a>Studio fiókok típusai és az adatok tárolásának módját

Három fő típusba sorolhatók a fiókok a Machine Learning Studióban. Fiók típusa határozza meg, hogyan tárolja az adatokat, és hogyan törölheti vagy exportálni kell.

- A **Vendég munkaterület** egy ingyenes, névtelen fiók. Feliratkozás hitelesítő adatokat, például egy e-mail címet, vagy a jelszó megadása nélkül.
    -  Az adatok törlődnek a Vendég munkaterület lejárata után is.
    - Vendégfelhasználók exportálhatja az ügyféladatokat a felhasználói felület, a REST API-k vagy a PowerShell csomag keresztül.
- A **szabad munkaterület** van egy ingyenes fiókot, amikor bejelentkezik a Microsoft a fiók hitelesítő adatait – egy e-mail címet és jelszót.
    - Exportálás és a személyes és a felhasználói adatok, amelyek tulajdonos rights (DSR) kérelmek törlése.
    - Exportálhatja az ügyféladatokat, a felhasználói felület, a REST API-k vagy a PowerShell csomag keresztül.
    - Szabad munkaterületek nem használja az Azure AD-fiókok, telemetriai exportálhatja az adatvédelmi portál használatával.
    - A munkaterület törlése, ha törli az összes személyes felhasználói adatokat.
- A **szabványos munkaterület** egy fizetős fiók bejelentkezési hitelesítő adataival hozzáférhet.
    - Exportálás és a személyes és a felhasználói adatok, amelyek DSR kérelmek törlése.
    - Az Azure adatvédelmi portálon keresztül lehet elérni az adatokat
    - Exportálhatja a személyes és a felhasználói adatok a felhasználói felület, a REST API-k vagy a PowerShell csomag keresztül
    - Az adatok az Azure portálon törölheti.

## <a name="delete-workspace-data-in-studio"></a>A Studio munkaterület adatok törlése 

### <a name="delete-individual-assets"></a>Egyes eszközök törlése

Felhasználók munkaterület eszközök törléséhez válassza ki azokat, majd jelölje be a Törlés gomb.

![A Machine Learning Studióban eszközök törlése](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Egy teljes munkaterület törlése

Felhasználók törölheti is a teljes munkaterület:
- A fizetős munkaterület: törlése az Azure portálon keresztül.
- Szabad munkaterület: a Törlés gomb a **beállítások** ablaktáblán.

![A Machine Learning Studióban szabad munkaterület törlése](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-data-with-powershell"></a>A PowerShell-lel Studio adatok exportálása
A PowerShell szolgáltatás minden az adatok exportálására egy hordozható formátumú az Azure Machine Learning Studio-parancsok használatával. További információ: a [Azure Machine Learning PowerShell-modul](powershell-module.md) cikk.

## <a name="next-steps"></a>További lépések

Webszolgáltatások és kötelezettségvállalás terv számlázási dokumentációjáért lásd: [Azure Machine Learning REST API-referenciában](https://docs.microsoft.com/en-us/rest/api/machinelearning/). 
