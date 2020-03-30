---
title: Adatok exportálása és törlése
titleSuffix: ML Studio (classic) - Azure
description: Az Azure Machine Learning Studio (klasszikus) által tárolt terméken belüli adatok az Azure Portalon, valamint a hitelesített REST API-kon keresztül is exportálhatók és törlésre. A telemetriai adatok az Azure Adatvédelmi portálon keresztül érhetők el. Ez a cikk bemutatja, hogyan.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 05/25/2018
ms.openlocfilehash: c380d10d0c68794ec3810cea25341d68bb41400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251686"
---
# <a name="export-and-delete-in-product-user-data-from-azure-machine-learning-studio-classic"></a>Terméken lévő felhasználói adatok exportálása és törlése az Azure Machine Learning Studio-ból (klasszikus)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Törölheti vagy exportálhatja az Azure Machine Learning Studio (klasszikus) által tárolt terméken belüli adatokat az Azure Portal, a Studio (klasszikus) felület, a PowerShell és a hitelesített REST API-k használatával. Ez a cikk bemutatja, hogyan. 

A telemetriai adatok az Azure Privacy portalon keresztül érhetők el. 

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-kinds-of-user-data-does-studio-classic-collect"></a>Milyen típusú felhasználói adatokat gyűjt a Studio (klasszikus)?

Ehhez a szolgáltatáshoz a felhasználói adatok a szolgáltatással folytatott felhasználói interakciók munkaterületeinek és telemetriai rekordjainak elérésére jogosult felhasználókadatait tartalmazza.

A Machine Learning Studio kétféle felhasználói adattal (klasszikus):
- **Személyes fiókadatok:** Fiókazonosítók és e-mail címek társítva egy fiókhoz.
- **Ügyféladatok:** Az elemzésre feltöltött adatok.

## <a name="studio-classic-account-types-and-how-data-is-stored"></a>Studio (klasszikus) fióktípusok és az adatok tárolásának módját

A Machine Learning Studio (klasszikus) háromféle fiókból áll. A fiók típusának meghatározása határozza meg az adatok tárolásának módját, valamint a törlés vagy exportálás módját.

- A **vendégmunkaterület** egy ingyenes, névtelen fiók. Hitelesítő adatok, például e-mail-cím vagy jelszó megadása nélkül regisztrál.
    -  Az adatok törlődnek a vendég munkaterület lejárta után.
    - A vendégfelhasználók exportálhatják az ügyféladatokat a felhasználói felületen, a REST API-kon vagy a PowerShell-csomagon keresztül.
- Az **ingyenes munkaterület** egy ingyenes fiók, amelybe a Microsoft-fiók hitelesítő adataival – e-mail címmel és jelszóval – jelentkezik be.
    - Exportálhatja és törölheti a személyes és az ügyféladatokat, amelyekre az érintettek jogaira (DSR) vonatkoznak.
    - Az ügyféladatok at a felhasználói felületen, a REST API-kon vagy a PowerShell-csomagon keresztül exportálhatja.
    - Az Azure AD-fiókokat nem használó ingyenes munkaterületek esetében a telemetriai adatok exportálhatók az adatvédelmi portálhasználatával.
    - A munkaterület törlésekor az összes személyes ügyféladat törlődik.
- A **szabványos munkaterület** egy fizetős fiók, amelyhez bejelentkezési hitelesítő adatokkal érhet i.
    - Exportálhatja és törölheti a dsr-kérelmek tárgyát képező személyes és ügyféladatokat.
    - Az azure-beli adatvédelmi portálon keresztül érheti el az adatokat
    - Személyes és ügyféladatokat exportálhat a felhasználói felületen, a REST API-kon vagy a PowerShell-csomagon keresztül
    - Törölheti az adatokat az Azure Portalon.

## <a name="delete-workspace-data-in-studio-classic"></a><a name="delete"></a>Munkaterületi adatok törlése a Studio-ban (klasszikus) 

### <a name="delete-individual-assets"></a>Egyedi eszközök törlése

A felhasználók a munkaterület eszközeit úgy törölhetik, hogy kijelölik őket, majd kijelölik a törlés gombot.

![Eszközök törlése a Machine Learning Studióban (klasszikus)](./media/export-delete-personal-data-dsr/delete-studio-asset.png)

### <a name="delete-an-entire-workspace"></a>Teljes munkaterület törlése

A felhasználók a teljes munkaterületüket is törölhetik:
- Fizetős munkaterület: Törlés az Azure Portalon keresztül.
- Szabad munkaterület: Használja a Törlés gombot a **Beállítások** ablaktáblán.

![Szabad munkaterület törlése a Machine Learning Studio alkalmazásban (klasszikus)](./media/export-delete-personal-data-dsr/delete-studio-data-workspace.png)
 
## <a name="export-studio-classic-data-with-powershell"></a>Studio (klasszikus) adatok exportálása a PowerShell segítségével
A PowerShell segítségével exportálhatja az összes információt egy hordozható formátumba az Azure Machine Learning Studio (klasszikus) parancsok használatával. További információkért tekintse meg a [PowerShell-modul az Azure Machine Learning Studio (klasszikus)](powershell-module.md) cikket.

## <a name="next-steps"></a>További lépések

A webes szolgáltatásokkal és a kötelezettségvállalási terv számlázásával kapcsolatos dokumentációt az [Azure Machine Learning Studio (klasszikus) REST API-referencia című témakörben találja.](https://docs.microsoft.com/rest/api/machinelearning/) 
