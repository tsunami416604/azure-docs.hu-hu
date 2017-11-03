---
title: "Nevű hitelesítő adatok beállítása |} Microsoft Docs"
description: "Megtudhatja, hogyan kell adnia hitelesítő adatait, amely a Visual Studio használatával kérések hitelesítése az Azure-ra, így közzétenni egy alkalmazást az Azure-bA a Visual Studio vagy egy meglévő felhőszolgáltatáshoz figyelése."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>Nevű hitelesítő adatok beállítása
Az Azure-bA a Visual Studio alkalmazás közzététele, vagy egy meglévő felhőszolgáltatáshoz figyelése, a Visual Studio segítségével az Azure-kérelmek hitelesítéséhez szükséges hitelesítő adatokat kell megadnia. A Visual Studio, ahol bejelentkezhet a hitelesítő adatok több helyen van. Például a Server Explorer eszközből megnyithatja a helyi menüje a **Azure** csomópont, és válassza **kapcsolódás a Microsoft Azure-előfizetés**. Amikor bejelentkezik, a Visual Studio az előfizetési adatok az Azure-fiókjával társított érhető el. Nincs szükség további kell elvégezni.

Azure-eszközökkel is támogatja a hitelesítő adatokat adjanak egy régebbi módja: az előfizetés fájllal (.publishsettings fájlt). Ez a cikk ismerteti a metódus, amely az Azure SDK 2.2 továbbra is támogatja.

A következőkre lesz szükség a hitelesítés az Azure-bA:

* Az előfizetés-Azonosítóval
* Egy érvényes X.509 v3 tanúsítvány

> [!NOTE]
> Az X.509 v3 tanúsítvány kulcs hossza legalább 2048 bit kell lennie. Azure elutasítja bármely olyan tanúsítvány, amely nem felel meg ennek a követelménynek, vagy, amely nem érvényes.
>
>

A Visual Studio az előfizetés-Azonosítóval és a tanúsítvány adatainak hitelesítő adatokat használja. A megfelelő hitelesítő adatokat a előfizetési fájl (.publishsettings-fájlt), amely tartalmazza a tanúsítványhoz tartozó nyilvános kulcs hivatkozott. Az előfizetés-fájl több előfizetés hitelesítő adatait tartalmazza.

Szerkesztheti az előfizetés adatait a **új előfizetés** vagy **előfizetés szerkesztése** párbeszédpanelen, a cikkben leírtak szerint.

Ha azt szeretné, létrehozhat egy tanúsítványt, hivatkozhat utasításait [létrehozása és feltöltése az Azure-tanúsítvány felügyeleti](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) , majd töltse fel manuálisan a tanúsítványt a [a klasszikus Azure portálon](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Ezeket a hitelesítő adatokat, amelyek a Visual Studio igényli a felhőszolgáltatások kezelése a kérelmet az Azure storage szolgáltatásainak hitelesítéséhez szükséges hitelesítő adatokat nem.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importálja, állítsa be, vagy a Visual Studio hitelesítő adatok szerkesztése

1. A Server Explorer eszközben a helyi menü megnyitása a **Azure** csomópont, és válassza **kezelése és a szűrő előfizetések**.
2. Válassza ki a **tanúsítványok** lapot, és kövesse az alábbi módszerek bármelyikét:

    * Válassza ki **importálási** megnyitásához a **importálása a Microsoft Azure-előfizetések** párbeszédpanel megnyitásához. Hiba, letöltheti az előfizetések fájlt az aktuálisan betöltött előfizetés, keresse meg a letöltési helyre, és importálja hitelesítés használható.
    * Válassza ki **új** megnyitásához a **új előfizetés** párbeszédpanel megnyitásához. Van beállíthat egy új előfizetés hitelesítés használható.
    * Válassza ki **szerkesztése** (után úgy dönt, hogy az aktív előfizetés) elemre a **előfizetés szerkesztése** párbeszédpanel. Szerkesztheti, egy meglévő előfizetéshez hitelesítés használható. 

A következő eljárás feltételezi, hogy a **új előfizetés** párbeszédpanel nyitva.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>A Visual Studio hitelesítő adatok beállítása
1. Az a **válasszon ki egy meglévő hitelesítéshez** menüben válassza ki a tanúsítványt.
2. Válassza ki a **másolja a teljes elérési útja** hivatkozásra. A tanúsítvány (.cer-fájl) elérési útja a vágólapra másolódik.

   > [!IMPORTANT]
   > A Visual Studio az Azure alkalmazás közzétételére, fel kell töltenie a tanúsítvány a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. A tanúsítvány feltöltése az Azure portálon:

   a. Nyissa meg az [Azure portált](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Ha a számítógép, jelentkezzen be a portálra, és tallózással keressen meg **beállítások** > **felügyeleti tanúsítványok**.
   
   c. Az a **felügyeleti tanúsítványok** ablaktáblán válassza előbb **feltöltése**.
   
   d. Az Azure-előfizetéssel, illessze be a teljes elérési útját a .cer fájlt, létre válassza ki és **feltöltése**.

## <a name="next-steps"></a>Következő lépések
* [Webalkalmazások általános áttekintése](https://docs.microsoft.com/azure/app-service/)
* [Az alkalmazás telepítése az Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [WebJobs üzembe helyezése Visual Studióval](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [Létrehozhat és telepíthet egy felhőalapú szolgáltatás](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)