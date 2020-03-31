---
title: Az AppSource-csomag tárolása az Azure storage-ba, és létrehoz egy URL-t sas-kulccsal
description: Az AppSource-csomagok feltöltéséhez és biztonságossá tételeihez szükséges lépések részletei.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: dsindona
ms.openlocfilehash: 57bc370fd160b8b3d6d7941ea28cd460c99f3d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285367"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Az AppSource-csomag tárolása az Azure storage-ba, és létrehoz egy URL-t sas-kulccsal
=============================================================================

A fájlok biztonságának megőrzése érdekében minden partnernek el kell tárolnia az AppSource-csomagfájlját egy Azure blob storage-fiókban, és sas-kulccsal kell megosztania. A csomagfájlt az Azure-tároló helyéről lefogjuk kérni a minősítéshez, és az AppSource-próbaverziókhoz használjuk.

A csomag blobstorage-ba való feltöltéséhez kövesse az alábbi lépéseket:

1. Lépjen <https://azure.microsoft.com> ingyenes próbaverzióra vagy számlázott fiókra.

2. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

3. Hozzon létre egy új tárfiókot a **+ Új** gombra kattintva, és a Data **+ Storage** fiókra kattintva.

   ![Adatok + Tárolás panel a Microsoft Azure Portalon](media/CRMScreenShot7.png)

4. Adja meg **a név** és **az erőforráscsoport nevét,** és kattintson a **Létrehozás** gombra.

   ![Tárfiók létrehozása a Microsoft Azure Portalon](media/CRMScreenShot8.png)

5. Keresse meg az újonnan létrehozott erőforráscsoportot, és hozzon létre egy új blob tárolót.

   ![Csomag feltöltése blobként a Microsoft Azure Portal használatával](media/CRMScreenShot9.png)

6. Ha még nem tette meg, töltse le és telepítse a Microsoft [Azure Storage Explorert.](https://storageexplorer.com/)

7. Nyissa meg a Storage Explorer alkalmazást, és az ikonnal csatlakozzon az Azure-tárfiókhoz.

8. Keresse meg a létrehozott blobtárolót, és kattintson a **Feltöltés gombra** a csomag zip-fájljának hozzáadásához.

   ![Csomag feltöltése a Microsoft Storage Explorer segítségével](media/CRMScreenShot10.png)

9. Kattintson a jobb gombbal a fájlra, és válassza **a Közös hozzáférés aláírása parancsot.**

   ![Azure-fájl megosztott hozzáférési aláírásának beszereznie](media/CRMScreenShot11.png)

10. Módosítsa a **lejárati időt,** hogy a SAS egy hónapig aktív legyen, majd kattintson a **Létrehozás gombra.**

    ![Azure-fájl SAS-lejárati dátumának módosítása](media/CRMScreenShot12.png)

11. Másolja az URL-mezőt, és mentse későbbre. Ezt az URL-címet a társított ajánlat létrehozásakor kell megadnia. 

    ![Azure-fájl SAS-URL-címének másolása](media/CRMScreenShot13.png)

