---
title: Felkészülés az közzététele, vagy telepítse a Visual Studio Felhőszolgáltatás |} Microsoft Docs
description: Ismerje meg, az eljárások a felhő- és tárolási fiók szolgáltatások beállítása és konfigurálása az Azure-alkalmazásában.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 8a7d6f114bfa10170cdfe7126e01a35b02affd20
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Felkészülés az közzététele, vagy egy felhőalapú szolgáltatás, a Visual Studio telepítése

Egy felhőszolgáltatás-projekt közzététele, be kell állítania a következő szolgáltatások ebben a cikkben leírtak szerint:

* A **felhőalapú szolgáltatás** környezetben való futtatásához a szerepkörök az Azure, és 
* A **tárfiók** , amely biztosítja, hogy a Blob, Queue és Table szolgáltatások eléréséhez.

## <a name="create-a-cloud-service"></a>Felhőszolgáltatás létrehozása

Egy felhőalapú szolgáltatás fut a szerepkörök az Azure környezetben. Visual Studio vagy a felhőszolgáltatás hozhat létre a [Azure-portálon](https://portal.azure.com/) a következő szakaszok leírtak szerint.

### <a name="create-a-cloud-service-from-visual-studio"></a>Felhőalapú szolgáltatás létrehozása a Visual Studio eszközből

1. Egy korábban létrehozott Felhőszolgáltatás-projekt, kattintson a jobb gombbal a projekt válassza **közzététel**.
1. Szükség esetén a Microsoft vagy az Azure-előfizetéshez társított szervezeti fiókkal jelentkezzen be, majd válassza ki **következő** ahhoz, hogy értékről a **beállítások** lap.
1. A **felhőalapú szolgáltatás létrehozása és a Tárfiók** párbeszédpanel jelenik meg (Ha nem, válassza ki a **hozzon létre új** a a **Felhőszolgáltatás** lista).
1. Adjon meg egy nem betűérzékeny nevet a felhőalapú szolgáltatás, amely a URL-CÍMÉT a részét képezi, és egyedinek kell lennie. Válasszon egy régiót vagy Affinitáscsoportot és is replikációs beállítás.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Az Azure portálon keresztül felhőalapú szolgáltatás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **Felhőszolgáltatások (klasszikus)** a lap bal oldalán.
1. Válassza ki **+ Hozzáadás**, majd adja meg a szükséges információkat (DNS-neve, előfizetés, erőforráscsoportban, és helyet). Nincs szükség ezen a ponton feltölteni egy csomagot, mert így tesz, amely később a Visual Studióban.
1. Válassza ki **létrehozása** a folyamat befejezéséhez.

## <a name="create-a-storage-account"></a>Create a storage account

A tárfiók a Blob, Queue és Table szolgáltatások hozzáférést biztosít. Visual Studio alkalmazással tárfiókot is létrehozhat vagy a [Azure-portálon](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>A storage-fiók létrehozása a Visual Studio eszközből

1. A **Megoldáskezelőben** egy korábban létrehozott Felhőszolgáltatás-projekt esetében, keresse meg a **kapcsolódó szolgáltatások** csomópont egy szerepkör projekt, kattintson a jobb gombbal és válassza ki **hozzá kapcsolódó szolgáltatás**. (A Visual Studio 2015, kattintson a jobb gombbal a **tárolási** csomópont, és válassza **Storage-fiók létrehozása**.)
1. Az a **kapcsolódó szolgáltatások** megjelenő listában, válasszon **felhőalapú tárolás az Azure Storage**.
1. Válassza ki az Azure Storage párbeszédpanelen megjelenő **+ új Tárfiók létrehozása**, amely megjelenik egy párbeszédpanel, amely az előfizetéséhez, a neve meg fő a fiók, egy árképzési szint, erőforráscsoportot és helyet.
1. Válassza ki **létrehozása** befejezése. Az új tárfiók az előfizetésben a rendelkezésre álló tárfiókok listájában jelenik meg.
1. Válassza ki, hogy a fiókot, és válassza ki **Hozzáadás**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Hozzon létre egy tárfiókot az Azure portálon keresztül

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **+ új** a bal felső sarokban.
1. Válassza ki **tárolási** az "Azure piactérről," majd **tárfiók - blob, a fájl, a tábla, a várólista** jobb oldalán.
1. Adja meg a szükséges információkat (név, üzembe helyezési modellt, és így tovább.
1. Válassza ki **létrehozása** a folyamat befejezéséhez.

## <a name="configure-your-app-to-use-the-storage-account"></a>Állítsa be alkalmazását annak a tárfiók használata

A storage-fiók létrehozása után lehet hozzá csatlakozni a Visual Studio automatikusan frissíti a szolgáltatáskonfiguráció a projekthez, beleértve az URL-címek és hozzáférési kulcsokkal.

Ha a Visual Studio használatával hozott létre egy felhőalapú szolgáltatást a **kapcsolódó szolgáltatás hozzáadása**, megnyitásával ellenőrizheti a kapcsolatok `ServiceConfiguration.Cloud.cscfg` és `ServiceConfiguration.Local.cscfg`.

Ha létrehozott egy felhőalapú szolgáltatás, az Azure portálon keresztül, ugyanazokat a lépéseket a [storage-fiók létrehozása a Visual Studio eszközből](#create-a-storage-account-from-visual-studio) , de a meglévő fiók kiválasztása helyett egy új létrehozásával. A Visual Studio majd frissíti a konfigurációt.

Konfigurálandó beállításokat manuálisan, használja a tulajdonságlapokon a Visual Studio a megfelelő szerepkör esetén a felhőszolgáltatás-projekt (kattintson a jobb gombbal a szerepkört, és válassza ki **tulajdonságok**). További információkért lásd: [tárfiók kapcsolati karakterláncának konfigurálása](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Hozzáférési kulcsok

Az Azure-portálon jeleníti meg az URL-címek használható az Azure storage szolgáltatások, valamint a fiók elsődleges és másodlagos elérési kulcsainak mindegyikében erőforrások eléréséhez. Ezek a kulcsok használatával a tárolási szolgáltatások ellen kérelmek hitelesítéséhez szükséges.

A másodlagos elérési kulcsát hozzáférést biztosít a azonos a tárfiók elsődleges elérési kulcsát, és jön létre biztonsági az elsődleges elérési kulcsát utaló jeleket. Emellett ajánlott, hogy a tárelérési kulcsok rendszeresen újragenerálja. Módosíthatja a kapcsolati karakterlánc beállítása használható a másodlagos kulcs újragenerálja az elsődleges kulcsot úgy, hogy használja a rendszer újragenerálta elsődleges kulcs, miközben a másodlagos kulcs újragenerálása módosíthatja.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a közzétételi alkalmazások az Azure-bA a Visual Studio eszközből, lásd: [közzététele a felhőalapú szolgáltatás, az Azure-eszközökkel](vs-azure-tools-publishing-a-cloud-service.md).
