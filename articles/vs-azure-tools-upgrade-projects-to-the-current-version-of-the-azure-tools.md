---
title: Az aktuális verzióra, az Azure-eszközök frissítése a projektek |} A Microsoft Docs
description: Ismerje meg az Azure a Visual Studio-projekt frissítése az aktuális verzióra, az Azure-eszközök
services: visual-studio-online
author: ghogen
manager: douge
assetId: 1d64070a-078d-468a-87f4-e6715de6475f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: a8b62bd261fb2b39f3e16549d2b531e442dc7b38
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969320"
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Projektek frissítése az aktuális verzióra, az Azure Tools for Visual Studio
## <a name="overview"></a>Áttekintés
Miután telepítette az Azure-eszközöket (vagy egy korábbi verzió újabb, mint az 1.6-os) a jelenlegi kiadásban, azokat a projekteket, létrehozott egy Azure-eszközök segítségével kiadás előtt 1.6-os (Listopad 2011) automatikusan frissül, amint megnyitja őket. Ha projektek létrehozott munkaterhelések 1.6-os verzióra (Listopad 2011) kiadását használja, és továbbra is adott kiadás telepítve van, nyissa meg ezeket a projektek a régebbi kiadást, és döntse el, később e frissíti őket.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Hogyan változik a projekthez, amikor frissít
Ha egy projektet a rendszer automatikusan frissíti, vagy megadhatja, hogy szeretné-e frissít a csomagon belül, bizonyos szerelvényeket aktuális változatának használata úgy módosul, hogy a projekt, és egyes tulajdonságait is megváltozott, ebben a szakaszban leírtak szerint. Ha a projekt van szüksége ahhoz, hogy az eszközök az újabb verzióval kompatibilis egyéb változások, módosítania kell azokat manuálisan.

* Webes szerepkörök esetében a web.config fájlt, és az app.config fájlt, a feldolgozói szerepkörök frissülnek való hivatkozáshoz Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll újabb verziója.
* Az új verzióra frissíti a Microsoft.WindowsAzure.StorageClient.dll Microsoft.WindowsAzure.Diagnostics.dll és Microsoft.WindowsAzure.ServiceRuntime.dll szerelvényeket.
* Az Azure-projekt fájlban (.ccproj) tárolt közzétételi profilokba a egy külön fájlt, a bővítmény .azurePubXml kerül a **közzététel** alkönyvtárat.
* A közzétételi profilt az egyes tulajdonságok frissülnek új és módosított funkciók támogatásához. **AllowUpgrade** helyébe **DeploymentReplacementMethod** mert Növekményesen vagy egyszerre egy üzembe helyezett felhőalapú szolgáltatás frissítheti.
* A tulajdonság **UseIISExpressByDefault** egészül ki, és állítsa be hamis értékre, hogy a webkiszolgáló a hibakereséshez használt nem változik az Internet Information Services (IIS) IIS Express. Az újabb kiadásokban az eszközök használatával létrehozott projektekhez az alapértelmezett webkiszolgáló az IIS Express.
* Ha az Azure gyorsítótár egy vagy több, a projekt szerepkörök üzemel, a szolgáltatás konfigurációs (.cscfg fájl) és a szolgáltatás definíciós (.csdef fájl) az egyes tulajdonságok projekt frissítésekor módosult. Ha a projekt az Azure gyorsítótár NuGet-csomagot használ, a projekt frissítése a legújabb verzióra a csomag. Nyissa meg a web.config fájlt kell, és győződjön meg arról, hogy a az ügyfél-konfiguráció megfelelően lett-e tartani a frissítési folyamat során. Ha a NuGet-csomag használata nélkül hozzáadott Azure-gyorsítótárazási ügyfél szerelvényeket mutató hivatkozásokat, ezekkel a szerelvényekkel nem lesz frissítve; manuálisan frissítenie kell az új verziókra mutató hivatkozásokat.

> [!IMPORTANT]
> Az F # projekty manuálisan frissítenie kell az Azure szerelvényre hivatkozik, hogy ezeket a szerelvények újabb verziói hivatkozó.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>A jelenlegi kiadásban az Azure-projekt frissítése
1. A telepítés a Visual Studio, a frissített projekthez használni kívánt Azure-eszközök jelenlegi verziója telepítse, és nyissa meg a frissíteni kívánt projekt. Ha a projekt egy Azure-eszközökkel együtt lett létrehozva. Mielőtt 1.6-os kiadás (Listopad 2011), a projekt a rendszer automatikusan frissíti az aktuális verzióra. Ha a projekt létrehozásának a 2011. November a kiadási és vált még telepítve van, a projekt nyílik vált.
2. A Megoldáskezelőben a projekt csomópontjának helyi menüjének megnyitásához, válassza a **tulajdonságok**, majd válassza a **alkalmazás** lapján megjelenő párbeszédpanelen.
   
    A **alkalmazás** fülre a projekthez tartozó eszközök verziót jeleníti meg. Azure-eszközök jelenlegi verziója jelenik meg, ha a projekt már frissítették. Ha telepített egy újabb verziója az eszközök milyen a lapon láthatók, mint egy **frissítése** gomb jelenik meg.
3. Válassza ki a **frissítése** gombra kattintva frissítse a projekt, az eszközök aktuális verzióját.
4. A projekt buildjének elkészítéséhez, és oldja meg a API-módosítás eredő esetleges hibákat. A kód az új verzióhoz tartozó módosításával kapcsolatos információkért tekintse meg az adott API dokumentációja.

