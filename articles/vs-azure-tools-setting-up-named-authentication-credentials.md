---
title: Állítsa be a hitelesítő adatok elnevezett |} A Microsoft Docs
description: Megtudhatja, hogyan kell adnia hitelesítő adatait, a Visual Studio segítségével az Azure-kérelmek hitelesítéséhez, így közzétenni egy alkalmazást a Visual Studióból az Azure-bA vagy egy létező felhőszolgáltatás monitorozása.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 61570907-42a1-40e8-bcd6-952b21a55786
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2017
ms.author: ghogen
ms.openlocfilehash: 48c410df1768c2cae7807aa1538d76867a581c71
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060175"
---
# <a name="set-up-named-authentication-credentials"></a>Elnevezett hitelesítő adatok beállítása

Közzétenni egy alkalmazást az Azure-bA vagy egy meglévő felhőszolgáltatáshoz figyelésére, a Visual Studio szükséges hitelesítő adatok-kérések hitelesítéséhez az Azure-ba, nevezetesen az Azure-előfizetése Azonosítóját és a egy érvényes X.509 v3 tanúsítvány legalább 2048 bites kulccsal. Ezek révén az alábbi módszerek bármelyikével hitelesítő adatok megadása:

- A Visual Studióban válassza **Nézet > Server Explorer**, kattintson a jobb gombbal a **Azure** csomópontban jelölje ki **kapcsolódás a Microsoft Azure-előfizetéshez**, és jelentkezzen be.
- Hozzon létre egy előfizetés fájlt (`.publishsettings`), amely tartalmazza a tanúsítványhoz tartozó nyilvános kulcs. Az előfizetés-fájl ebben a cikkben leírtak szerint egynél több előfizetéshez tartozó hitelesítő adatokat tartalmazza.

Megjegyzés: ezeket a hitelesítő adatokat eltérnek az Azure storage-szolgáltatásokra irányuló kérések hitelesítéséhez használandó hitelesítő adatokat.

## <a name="create-a-subscription-file"></a>Előfizetés-fájl létrehozása

A Kiszolgálókezelőben kattintson a jobb gombbal a **Azure** csomópontra, és válassza **kezelése és a szűrő előfizetések**. Válassza ki a **tanúsítványok** lapra, és hajtsa végre az alábbi műveletek egyikét:

- Válassza ki **importálás** megnyitásához a **importálása a Microsoft Azure-előfizetések** párbeszédpanel bezárásához. Válassza ki a **letöltési előfizetésfájlt** hivatkozásra, és a böngésző mentse a letöltött fájlt egy ideiglenes helyre. A párbeszédpanelen tallózással keresse meg a letöltési helyre, és importálja a hitelesítéshez használatos.
- Aktív előfizetés, és válasszon **szerkesztése**, amely megnyílik egy párbeszédpanel, amelyben szerkesztheti a meglévő előfizetését hitelesítés használható.
- Válassza ki **új** megnyitásához a **új előfizetés** párbeszédpanel mezőbe, majd adja meg a szükséges adatokat. Töltse fel a tanúsítványt a felhőhöz a szolgáltatás jeleztük, a párbeszédpanelen jelentkezzen be az Azure Portalra, keresse meg a cloud Services, a select **beállítások > felügyeleti tanúsítványok**, jelölje be **feltöltése**, majd Adja meg az elérési útját a `.cer` fájlt.

Ha azt szeretné, létrehozhat egy tanúsítványt, olvassa el a következő témakör utasításait [létrehozása és feltöltése az Azure felügyeleti tanúsítvány-](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) , majd töltse fel manuálisan a tanúsítványt a [az Azure portal](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

- [Web Apps általános áttekintése](https://docs.microsoft.com/azure/app-service/)
- [Alkalmazás üzembe helyezése az Azure App Service-ben](https://docs.microsoft.com/azure/app-service/app-service-deploy-local-git) 
- [WebJobs üzembe helyezése Visual Studióval](https://docs.microsoft.com/azure/app-service/websites-dotnet-deploy-webjobs)
- [Hozzon létre és telepíthet egy felhőszolgáltatást](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)