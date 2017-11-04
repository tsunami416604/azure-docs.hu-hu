---
title: "Projektek az Azure-eszközök aktuális verziójának frissítése |} Microsoft Docs"
description: "Útmutató: Azure, a Visual Studio-projekt frissítsen az Azure-eszközök jelenlegi verziója"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 9a35de7ca0e7161468181b21709e1bd9915d566f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>A Visual Studio frissítése projektek az Azure-eszközök jelenlegi verziója
## <a name="overview"></a>Áttekintés
Miután telepítette az Azure-eszközök (vagy egy előző kiadása, amely újabb, mint az 1.6-os) a jelenlegi kiadásban, bármely projektek Azure-eszközök használatával létrehozott kiadási előtt 1.6-os (November 2011) automatikusan frissíti, amint megnyitja őket. Ha projektek az 1.6-os verzióra (November 2011) kiadási azok az eszközök használatával létrehozott és telepített kiadás továbbra is fennáll, nyissa meg ezeket a projektek a régebbi verzióban, és döntse el, később e frissíteni azokat.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Hogyan változik a projekthez, amikor a frissítés
Ha a rendszer automatikusan frissíti a projekt, vagy megadhatja, hogy szeretné-e frissíteni, a projekt úgy módosul, hogy bizonyos szerelvények aktuális változatának használata, és egyes tulajdonságok is van módosítani, mert ez a szakasz ismerteti. Ha a projekthez szükséges más jellegű módosítását a eszközök újabb verziójával kompatibilis, módosítania kell azokat manuálisan.

* A web.config fájlban webes szerepkörök és feldolgozói szerepkörök az app.config fájlt frissülnek való hivatkozáshoz Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll újabb verziója.
* A Microsoft.WindowsAzure.StorageClient.dll Microsoft.WindowsAzure.Diagnostics.dll és Microsoft.WindowsAzure.ServiceRuntime.dll szerelvények az új verzióra frissíti.
* Közzétételi profil az Azure-projekt fájlban (.ccproj) tárolt átkerül külön fájlt, a bővítmény .azurePubXml a a **közzététel** alkönyvtár.
* A közzétételi profil az egyes tulajdonságok frissítése új és módosított funkciók támogatásához. **AllowUpgrade** helyébe **DeploymentReplacementMethod** mert telepített felhőszolgáltatás egyidejűleg vagy fokozatosan frissítheti.
* A tulajdonság **UseIISExpressByDefault** hozzá, és állítsa be hamis értékre, hogy a webkiszolgáló hibakereséshez használt nem változik az Internet Information Services (IIS) az IIS Express. Az újabb kiadásokban az eszközök használatával létrehozott projektekhez az alapértelmezett webkiszolgáló az IIS Express.
* Ha egy vagy több, a projekt szerepkörök Azure gyorsítótárazás üzemelteti, a szolgáltatás konfigurációs (.cscfg fájl) és a szolgáltatás definíciós (.csdef fájl) az egyes tulajdonságok projekt frissítésekor módosult. Ha a projektet az Azure-gyorsítótárazás NuGet-csomagot használ, a projekt nem frissíti a csomag legutóbbi verzióját. Nyissa meg a web.config fájlt, és ellenőrizze, hogy a az ügyfél-konfigurációt megfelelően lett-e tartani a frissítési folyamat során. Ha hozzáadott Azure gyorsítótárazás ügyfélszerelvényekre mutató hivatkozásokat a NuGet-csomag használata nélkül, nem lesz frissítve a szerelvények; Ezeket a hivatkozásokat, az új verziókra manuálisan kell frissíteni.

> [!IMPORTANT]
> Az F # projektek manuálisan kell frissíteni az Azure szerelvények mutató hivatkozásokat, hogy ezeket a szerelvények újabb verziói hivatkozó.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>A jelenlegi kiadásban az Azure-projekt frissítése
1. Az Azure-eszközök aktuális verziójának telepítése a Visual Studio, a frissített projekthez használni kívánt telepítési, és nyissa meg a projekt, amely frissíti. Ha a projekt, amely egy Azure eszközök készült kibocsátási előtt 1.6-os (2011. November), a projekt a rendszer automatikusan frissíti az aktuális verzióra. Ha a projekt létrehozásának az a November 2011 kiadási és, hogy a kiadásban továbbra is telepítve van, a projekt nyílik meg, hogy a kiadás.
2. A Solution Explorerben nyissa meg a projekt csomópontjának helyi menüje, válassza a **tulajdonságok**, majd válassza a **alkalmazás** lapján megjelenő párbeszédpanelen.
   
    A **alkalmazás** lapon láthatók az eszközök verzióra, amely a projekt van társítva. Azure eszközök jelenlegi verziója jelenik meg, ha a projekt már frissítették. Ha az eszközök újabb verziójú, mint hogy milyen a lapon láthatók, hogy telepítette egy **frissítése** gomb akkor jelenik meg.
3. Válassza ki a **frissítése** gombra kattintva a projekt frissítsen az eszközök aktuális verzióját.
4. A projekt buildjének elkészítéséhez, és majd címet a API változások hibákról. A kódot az új verzióhoz tartozó módosításával kapcsolatos információkért lásd: a megadott API-JÁNAK dokumentációja.

