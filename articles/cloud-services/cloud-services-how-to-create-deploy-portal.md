---
title: Hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás, az Azure portál használatával.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 96b92690cd164b1012380f82a1d1bd3336350e57
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388084"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás
Az Azure portálon két módot biztosít hozhat létre és telepíthet egy felhőalapú szolgáltatás: *Gyorslétrehozás* és *egyéni létrehozás*.

Ez a cikk ismerteti, hogyan hozzon létre egy új felhőalapú szolgáltatás, majd a gyors létrehozás módszerrel **feltöltése** frissítése és telepítése az Azure cloud service csomag. Ha ezt a módszert használja, az Azure-portálon teszi követelményeinek befejezése menet elérhető kényelmes hivatkozásokat. Ha készen áll a felhőalapú szolgáltatás történő létrehozásakor telepítendő, mindkettő egyszerre használja egyéni létrehozás teheti meg.

> [!NOTE]
> Ha azt tervezi, közzététele a felhőalapú szolgáltatás a Visual Studio Team Services (VSTS), használja a Gyorslétrehozás, és majd VSTS közzététel beállítása az Azure gyors üzembe helyezés vagy az irányítópulton. További információkért lásd: [használatával Visual Studio Team Services Azure folyamatos kézbesítése][TFSTutorialForCloudService], vagy tekintse át a súgóban talál a **gyors üzembe helyezés** lap.
>
>

## <a name="concepts"></a>Alapelvek
Három összetevők szükségesek a felhő alapú szolgáltatásként az Azure-ban az alkalmazás központi telepítéséről:

* **Service Definition**  
  A felhőalapú szolgáltatás definíciós fájl (.csdef) a szolgáltatásmodellt, beleértve a szerepkörök számának meghatározása.
* **Service Configuration**  
  A felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) a felhőre vonatkozó konfigurációs beállításokat tartalmazza, szolgáltatás és az egyes szerepkörök, beleértve a szerepkörpéldányok számát.
* **Szolgáltatáscsomag**  
  A szolgáltatás csomagba (.cspkg) tartalmazza az alkalmazás kódjában és konfigurációkat és a szolgáltatásdefiníciós fájlban.

Ezek és csomag létrehozásával kapcsolatos részletesebb [Itt](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése
Egy felhőalapú szolgáltatás telepítése előtt a felhőalapú szolgáltatás csomagba (.cspkg) alkalmazáskódjában és egy felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) kell létrehoznia. Az Azure SDK eszközöket biztosít a szükséges központi telepítés fájlok előkészítése. Az SDK telepítése a [Azure letölti](https://azure.microsoft.com/downloads/) oldalon, a nyelvet, amelyben az alkalmazás kódjában fejlesztéséhez.

Három cloud service funkciókhoz speciális konfigurációk service-csomag exportálása előtt:

* Ha szeretné központilag telepíteni egy felhőszolgáltatás, amely a Secure Sockets Layer (SSL) használ az adatok titkosításához, [állítsa be az alkalmazását](cloud-services-configure-ssl-certificate-portal.md#modify) SSL-t.
* Ha a távoli asztal kapcsolatokat szerepkörpéldányt beállítani, a konfigurálni kívánt [a szerepkörök konfigurálása](cloud-services-role-enable-remote-desktop-new-portal.md) a távoli asztal.
* Ha konfigurálni szeretné részletes figyelését a felhőalapú szolgáltatás, Azure diagnosztika engedélyezése a felhőalapú szolgáltatáshoz. *Minimális figyelési* (az alapértelmezett figyelési szint) az állomás operációs rendszereket a szerepkörpéldányok (virtuális gépek) szolgáltatástól összegyűjtött teljesítményszámlálókat használ. *Részletes figyelési* gyűjti a teljesítményadatokat belül a szerepkörpéldányok ahhoz, hogy szorosabb kérelem feldolgozása során előforduló problémák elemzése alapján további metrikákat. Azure Diagnostics engedélyezése regisztrációval, lásd: [engedélyezése az Azure diagnostics](cloud-services-dotnet-diagnostics.md).

Felhőalapú szolgáltatás létrehozása a webes szerepkörök vagy feldolgozói szerepkörök példányai, le kell [a service-csomag létrehozása](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Előkészületek
* Ha még nem telepítette az Azure SDK-t, kattintson a **Azure SDK telepítése** megnyitásához a [Azure letöltőoldala](https://azure.microsoft.com/downloads/), és töltse le az SDK for a nyelvet, amelyben a kód kialakításához. (Összekapcsolta elvégezheti később lehetőséget.)
* Ha bármely szerepkörpéldányokat szükséges tanúsítvány, a tanúsítványok létrehozása. Cloud services és a titkos kulcs egy .pfx fájlba szükséges. A tanúsítványok feltöltheti az Azure-ba, létrehozásához, és a felhőalapú szolgáltatás telepítéséhez.

## <a name="create-and-deploy"></a>Létrehozása és telepítése
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a **hozzon létre egy erőforrást > számítási**, és görgessen lefelé, és kattintson a **felhőalapú szolgáltatás**.

    ![A felhőalapú szolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Az új **felhőalapú szolgáltatás** panelen adjon meg egy értéket a **DNS-név**.
4. Hozzon létre egy új **erőforráscsoport** vagy válasszon egy meglévőt.
5. Válasszon ki egy **helyet**.
6. Kattintson a **csomag**. Ekkor megnyílik a **a csomag feltöltése** ablaktáblán. Töltse ki a kötelező mezőket. Ha egyetlen példányt tartalmaz, a szerepkörökben, győződjön meg arról **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** van kiválasztva.
7. Győződjön meg arról, hogy **indítsa el a központi telepítés** van kiválasztva.
8. Kattintson a **OK** bezárul, amely a **a csomag feltöltése** ablaktáblán.
9. Ha nem rendelkezik a tanúsítványok hozzáadása, kattintson a **létrehozása**.

    ![A felhőalapú szolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése
Ha a központi telepítési csomag [tanúsítványok](cloud-services-configure-ssl-certificate-portal.md#modify), most már feltöltheti a tanúsítványt.

1. Válassza ki **tanúsítványok**, majd a a **tanúsítványok hozzáadása** ablaktáblán válassza ki az SSL tanúsítvány .pfx fájlt, és adja meg a **jelszó** a tanúsítvány
2. Kattintson a **Attach tanúsítvány**, és kattintson a **OK** a a **adja hozzá a tanúsítványok** ablaktáblán.
3. Kattintson a **létrehozása** a a **Felhőszolgáltatás** ablaktáblán. Ha a központi telepítés elérte a **készen** állapota, továbbléphet a következő lépéseket.

    ![A felhőalapú szolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ellenőrizze a telepítés sikeresen befejeződött
1. Kattintson a cloud service-példány.

    Az állapot jelenítsen meg, hogy van-e a szolgáltatás **futtató**.
2. A **Essentials**, kattintson a **webhely URL-címe** a felhőalapú szolgáltatás megnyitása a böngészőben.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>További lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
