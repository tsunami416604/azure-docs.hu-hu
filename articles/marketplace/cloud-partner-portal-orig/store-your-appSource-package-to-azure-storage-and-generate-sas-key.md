---
title: AppSource-csomag tárolása az Azure Storage-ban és egy URL-cím létrehozása SAS-kulccsal
description: Részletesen ismerteti a AppSource-csomagok feltöltéséhez és védelméhez szükséges lépéseket.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 5f1a09244697a6771ad1b499f3d7c36eb7297067
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827647"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>AppSource-csomag tárolása az Azure Storage-ban és egy URL-cím létrehozása SAS-kulccsal
=============================================================================

A fájlok biztonságának fenntartása érdekében minden partnernek egy Azure Blob Storage-fiókban kell tárolnia a AppSource-csomagot, és a megosztáshoz SAS-kulcsot kell használnia. Az Azure Storage-beli tárolási helyről beolvasjuk a csomagfájl minősítését, és azt AppSource-próbák esetén használni fogjuk.

A következő lépésekkel töltheti fel a csomagot a blob Storage-ba:

1. Lépjen <https://azure.microsoft.com>, és hozzon létre egy ingyenes próbaverziót vagy egy számlázott fiókot.

2. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

3. Hozzon létre egy új Storage-fiókot az **+ új** lehetőségre kattintva, majd lépjen az **adattároló** -fiókra.

   ![Adattárolási panel az Microsoft Azure portálon](media/CRMScreenShot7.png)

4. Adja meg a **név** és az **erőforráscsoport** nevét, majd kattintson a **Létrehozás** gombra.

   ![Storage-fiók létrehozása Microsoft Azure portálon](media/CRMScreenShot8.png)

5. Navigáljon az újonnan létrehozott erőforráscsoporthoz, és hozzon létre egy új BLOB-tárolót.

   ![Csomag feltöltése blobként Microsoft Azure portál használatával](media/CRMScreenShot9.png)

6. Ha még nem tette meg, töltse le és telepítse a Microsoft [Azure Storage Explorert](https://storageexplorer.com/).

7. Nyissa meg Storage Explorer és az ikon használatával kapcsolódjon az Azure Storage-fiókjához.

8. Navigáljon a létrehozott blob-tárolóhoz, és kattintson a **feltöltés** gombra a csomag zip-fájljának hozzáadásához.

   ![Csomag feltöltése a Microsoft Storage Explorer használatával](media/CRMScreenShot10.png)

9. Kattintson a jobb gombbal a fájlra, és válassza a **közös hozzáférési aláírás beolvasása**elemet.

   ![Azure-fájl közös hozzáférésű aláírásának beolvasása](media/CRMScreenShot11.png)

10. Módosítsa a **lejárati időt** , hogy az SAS aktív legyen egy hónapig, majd kattintson a **Létrehozás**gombra.

    ![Azure-fájl SAS-lejárati dátumának módosítása](media/CRMScreenShot12.png)

11. Másolja az URL-címet a mezőbe, és mentse később. A társított ajánlat létrehozásakor meg kell adnia ezt az URL-címet. 

    ![Azure-fájl SAS URL-címének másolása](media/CRMScreenShot13.png)

