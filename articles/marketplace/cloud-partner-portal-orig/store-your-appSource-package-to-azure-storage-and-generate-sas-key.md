---
title: Az AppSource-csomag az Azure storage-Store és a egy URL-cím létrehozhat egy SAS-kulcsának |} A Microsoft Docs
description: Töltse fel, és tegye biztonságossá az AppSource-csomag szükséges lépéseket részletezi.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 335590cc616035020f66cfc5208e21d4c5128fe6
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810754"
---
<a name="store-your-appsource-package-to-azure-storage-and-generate-a-url-with-sas-key"></a>Az AppSource-csomag az Azure storage-Store és a egy URL-cím létrehozhat egy SAS-kulcs
=============================================================================

A fájlok biztonságának fenntartása minden partnerek kell az appsource-ban alkalmazáscsomag-fájl tárolása egy Azure blob storage-fiókot, és ossza meg a SAS-kulcs használatával. Mi az alkalmazáscsomag-fájl lekérése az Azure storage-tárolójába minősítésre és az appsource-ban próbaverziókhoz használandó.

Az alábbi lépések segítségével a csomag feltöltése a blob storage:

1.  Lépjen a <http://azure.microsoft.com> és a egy próbaverziós vagy számlázott ingyenes fiók létrehozása.

2.  Jelentkezzen be az [Azure Portal](http://portal.azure.com/).

3.  Hozzon létre egy új Tárfiókot kattintva **+ új** és a **adatok + tárolás** fiókot.

  ![Adatok + tárolás panel a Microsoft Azure Portalon](media/CRMScreenShot7.png)

4. Adjon meg egy **neve** és **erőforráscsoport** nevet, és kattintson a **létrehozása** gombra.

  ![Storage-fiók létrehozása a Microsoft Azure Portalon](media/CRMScreenShot8.png)

5. Keresse meg az újonnan létrehozott erőforráscsoportot, és hozzon létre egy új blobtárolót.

  ![A Microsoft Azure Portallal blob csomag feltöltése](media/CRMScreenShot9.png)

6.  Ha még nem tette meg, töltse le és telepítse a Microsoft [Azure Storage Explorer](http://storageexplorer.com/).

7.  Nyissa meg a Storage Explorert, és az ikon segítségével csatlakozhat az Azure storage-fiók.

8.  Keresse meg a létrehozott blob-tárolóba, és kattintson a **feltöltése** felvenni a csomag zip fájlt.

  ![A Microsoft Storage Explorer használatával csomag feltöltése.](media/CRMScreenShot10.png)

9.  A jobb gombbal a fájlt, és válassza a **közös hozzáférési jogosultságkód igénylése**.

  ![Az Azure-fájl a közös hozzáférésű jogosultságkód igénylése](media/CRMScreenShot11.png)

10.  Módosítsa a **lejárati idő** ahhoz, hogy a SAS aktív egy hónapig, majd kattintson a **létrehozás**.

  ![Az Azure file SAS lejárati idejének módosítása](media/CRMScreenShot12.png)

11.  Másolja az URL-cím mezőben, és mentse későbbi használatra. Adja meg az URL-címet, a kapcsolódó ajánlat létrehozásakor kell. 

  ![Az SAS URL-cím az Azure-fájl másolása](media/CRMScreenShot13.png)

