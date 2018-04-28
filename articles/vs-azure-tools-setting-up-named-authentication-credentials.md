---
title: Nevű hitelesítő adatok beállítása |} Microsoft Docs
description: Megtudhatja, hogyan kell adnia hitelesítő adatait, amely a Visual Studio használatával kérések hitelesítése az Azure-ra, így közzétenni egy alkalmazást az Azure-bA a Visual Studio vagy egy meglévő felhőszolgáltatáshoz figyelése.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 3dce80f5c6611cb2a22293d1a574980db5ea8378
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="set-up-named-authentication-credentials"></a>Nevű hitelesítő adatok beállítása

Közzétesz egy alkalmazást az Azure-ba, vagy egy meglévő felhőszolgáltatáshoz figyelése, a Visual Studio-kérelmek hitelesítéséhez szükséges az Azure-ba, nevezetesen az Azure-előfizetése Azonosítóját és egy érvényes X.509 v3 tanúsítvány legalább 2048 bites kulccsal rendelkező hitelesítő adatokra van szüksége. Megadja ezen hitelesítő adatok használatával a következő módszerek egyikét:

- A Visual Studio select **Nézet > Server Explorer**, kattintson a jobb gombbal a **Azure** csomópontban jelölje ki **kapcsolódás a Microsoft Azure-előfizetés**, és jelentkezzen be.
- Hozzon létre egy előfizetés fájlt (`.publishsettings`), amely tartalmazza a tanúsítványhoz tartozó nyilvános kulcs. Az előfizetés fájl tartalmazhat egynél több előfizetésnek hitelesítő adatait, ebben a cikkben leírtak szerint.

Megjegyzés: ezek a hitelesítő adatok eltérnek az Azure storage szolgáltatások kérelmek hitelesítéséhez szükséges hitelesítő adatokat.

## <a name="create-a-subscription-file"></a>Hozzon létre egy előfizetés fájlt

A Server Explorer eszközben kattintson a jobb gombbal a **Azure** csomópont, és válassza **kezelése és a szűrő előfizetések**. Válassza ki a **tanúsítványok** lapot, és tegye a következők közül:

- Válassza ki **importálási** megnyitásához a **importálása a Microsoft Azure-előfizetések** párbeszédpanel megnyitásához. Válassza ki a **letöltési előfizetési fájl** hivatkozásra, és a böngészőben mentse a letöltött fájlt egy ideiglenes helyre. A párbeszédpanelen keresse meg a letöltési helyre, és importálja a hitelesítés használható.
- Válassza az aktív előfizetéssel, majd **szerkesztése**, amely, megnyílik egy párbeszédpanel, amelyben szerkesztheti egy meglévő előfizetéshez hitelesítés használható.
- Válassza ki **új** megnyitásához a **új előfizetés** párbeszédpanel mezőben, és adja meg a szükséges adatokat. A tanúsítvány feltöltése a felhőbe service párbeszédpanelen jeleztük jelentkezzen be az Azure-portálra, keresse meg a felhőalapú szolgáltatás, válassza **beállítások > felügyeleti tanúsítványok**, jelölje be **feltöltése**, majd Adja meg az elérési útját a `.cer` fájlt.

Ha azt szeretné, létrehozhat egy tanúsítványt, hivatkozhat utasításait [létrehozása és feltöltése az Azure-tanúsítvány felügyeleti](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) , majd töltse fel manuálisan a tanúsítványt a [Azure-portálon](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

- [Webalkalmazások általános áttekintése](https://docs.microsoft.com/azure/app-service/)
- [Az alkalmazás telepítése az Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [WebJobs üzembe helyezése Visual Studióval](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Létrehozhat és telepíthet egy felhőalapú szolgáltatás](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)