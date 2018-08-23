---
title: Közzétételekor vagy telepítésekor a Visual Studio Felhőszolgáltatás előkészítése |} A Microsoft Docs
description: Ismerje meg, az eljárások a felhőbeli és a tárolási fiók szolgáltatások beállítása és konfigurálása az Azure-alkalmazásokat.
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 92ee2f9e-ec49-4c7a-900d-620abe5e9d8a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 084ba64389e2f3f8d62b77697df368dac7e09eac
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42061391"
---
# <a name="prepare-to-publish-or-deploy-a-cloud-service-from-visual-studio"></a>Közzétételekor vagy telepítésekor a Visual Studio felhőszolgáltatás előkészítése

Egy felhőszolgáltatás-projekt közzététele, be kell állítania a következő szolgáltatások ebben a cikkben leírtak szerint:

* A **felhőszolgáltatás** a szerepkörök futtatásához az Azure-környezetben, és 
* A **tárfiók** , amely a Blob, Queue és Table szolgáltatások hozzáférést biztosít.

## <a name="create-a-cloud-service"></a>Felhőszolgáltatás létrehozása

Egy felhőalapú szolgáltatás a szerepkörök az Azure-környezetben fut. Létrehozhat egy felhőalapú szolgáltatás, vagy a Visual studióban, vagy keresztül a [az Azure portal](https://portal.azure.com/) az az alábbi szakaszokban leírtak szerint.

### <a name="create-a-cloud-service-from-visual-studio"></a>Egy felhőalapú szolgáltatás létrehozása a Visual Studióból

1. A korábban létrehozott Felhőszolgáltatás-projektben kattintson a jobb gombbal a projekt válassza **közzététel**.
1. Ha szükséges, jelentkezzen be a Microsoft vagy a szervezeti fiók az Azure-előfizetéséhez társított, majd válassza ki **tovább** azt mutatja be, hogy a **beállítások** lapot.
1. A **Felhőszolgáltatás létrehozása és a Storage-fiók** párbeszédpanel jelenik meg (Ha nem, válassza ki a **hozzon létre új** származó a **Felhőszolgáltatás** lista).
1. Adja meg a kis-és nevét, a felhőalapú szolgáltatás, amely az URL-Címének részét képezi, és egyedinek kell lennie. Is válasszon egy régió vagy Affinitáscsoport, és válassza ki a replikációs beállítás.

### <a name="create-a-cloud-service-through-the-azure-portal"></a>Az Azure Portalon keresztül felhőalapú szolgáltatás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **Felhőszolgáltatások (klasszikus)** az oldal bal oldalán.
1. Válassza ki **+ Hozzáadás**, majd adja meg a szükséges információkat (DNS név, előfizetés, erőforráscsoport és hely). Nem kell csomagot ezen a ponton feltölteni, mert később a Visual Studióban megtenni.
1. Válassza ki **létrehozás** a folyamat befejezéséhez.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A tárfiók a Blob, Queue és Table szolgáltatások hozzáférést biztosít. Hozhat létre egy tárfiókot, Visual Studión keresztül vagy a [az Azure portal](https://portal.azure.com/).

### <a name="create-a-storage-account-from-visual-studio"></a>Storage-fiók létrehozása a Visual Studióból

1. A **Megoldáskezelőben** a korábban létrehozott Felhőszolgáltatás-projektben keresse meg a **csatlakoztatott szolgáltatás** belül egy szerepkör projekt, kattintson a jobb gombbal, és válassza a csomópontot **csatlakoztatott szolgáltatás hozzáadása**. (A Visual Studio 2015, kattintson a jobb gombbal a **tárolási** csomópontra, és válassza **Create Storage Account**.)
1. Az a **csatlakoztatott szolgáltatás** megjelenő listában, válassza ki **felhőalapú tárolás az Azure Storage**.
1. Azure Storage, a megjelenő párbeszédpanelen jelölje ki a **+ létrehozás új Tárfiók**, amely megjelenik egy párbeszédpanel, amelyben adja meg az előfizetés nevét fo a fiók, egy árképzési szint, erőforráscsoportot és helyet.
1. Válassza ki **létrehozás** befejezése. Az új tárfiókot az előfizetésében rendelkezésre álló tárfiókok listájában jelenik meg.
1. Válassza ki, hogy a fiókot, és válassza ki **Hozzáadás**.

### <a name="create-a-storage-account-through-the-azure-portal"></a>Hozzon létre egy tárfiókot az Azure Portalon keresztül

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Válassza ki **+ új** a bal felső sarokban.
1. Válassza ki **tárolási** szerint "Az Azure Marketplace-en," majd **tárfiók – blob, fájl, tábla, üzenetsor** jobb oldaláról.
1. Adja meg a szükséges információkat (név, üzembe helyezési modell, és így tovább.
1. Válassza ki **létrehozás** a folyamat befejezéséhez.

## <a name="configure-your-app-to-use-the-storage-account"></a>A storage-fiókot az alkalmazás konfigurálása

Miután létrehozott egy tárfiókot, csatlakoztathatja azt a Visual Studio automatikusan frissíti a szolgáltatáskonfiguráció a projekt URL-címek és a hozzáférési kulcsokat.

Ha a felhőszolgáltatás a Visual Studio használatával hozott létre a **csatlakoztatott szolgáltatás hozzáadása**, a kapcsolatok megnyitásával ellenőrizheti `ServiceConfiguration.Cloud.cscfg` és `ServiceConfiguration.Local.cscfg`.

Ha létrehozott egy felhőalapú szolgáltatás, az Azure Portalon keresztül, ugyanazokat a lépéseket a [storage-fiók létrehozása a Visual Studióból](#create-a-storage-account-from-visual-studio) , de válassza ki a meglévő fiók helyett egy új létrehozásához. A Visual Studio majd frissíti a konfigurációt.

Konfigurálása beállításokat manuálisan, használja a tulajdonságlapokon a Visual Studióban a felhőszolgáltatási projektet a alkalmazni szerepkörének (kattintson a jobb gombbal a szerepkört, és válassza ki **tulajdonságok**). További információkért lásd: [egy tárfiók kapcsolati sztringjének konfigurálása](https://docs.microsoft.com/azure/vs-azure-tools-multiple-services-project-configurations#configuring-a-connection-string-to-a-storage-account).

### <a name="about-access-keys"></a>Hozzáférési kulcsok

Az Azure Portalon látható, hogy az egyes az Azure storage szolgáltatások, valamint az a fiók elsődleges és másodlagos hívóbetűit erőforrások eléréséhez használhatja az URL-címeket. Ezek a kulcsok használatával hitelesíti a tárolási szolgáltatások kérelmekre.

A másodlagos elérési kulcsot azonos hozzáféréssel a tárfiók elsődleges hívóbetűjét biztosít, és akkor jön létre biztonsági mentéséhez az elsődleges elérési kulcs sérült. Emellett ajánlott, hogy a a hozzáférési kulcsok rendszeres újragenerálása. Módosíthatja a kapcsolati karakterlánc beállítása használata a másodlagos kulcsot amíg az elsődleges kulcs, majd módosíthatja úgy, hogy az elsődleges kulcs újragenerálása használja, míg a másodlagos kulcs újragenerálása.

## <a name="next-steps"></a>További lépések

Alkalmazások közzététele az Azure-bA a Visual Studio-bővebben lásd: [közzétételéhez egy Cloud Service, Azure-eszközök segítségével](vs-azure-tools-publishing-a-cloud-service.md).
