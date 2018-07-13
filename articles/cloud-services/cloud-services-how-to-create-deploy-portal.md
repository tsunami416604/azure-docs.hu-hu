---
title: Hogyan hozhat létre és telepíthet egy felhőszolgáltatást |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és telepíthet egy felhőszolgáltatást, az Azure portal használatával.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 57109848bf78311ea4d601b135c5dd304d613aeb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008127"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hogyan hozhat létre, és a egy felhőalapú szolgáltatás üzembe helyezése
Az Azure portal két módon is hozhat létre és telepíthet egy felhőalapú szolgáltatás biztosít: *Gyorslétrehozás* és *egyéni létrehozás*.

Ez a cikk ismerteti, hogyan hozzon létre egy új felhőszolgáltatást, majd a gyors létrehozás módszerrel **feltöltése** és az Azure-ban a felhőszolgáltatás-csomagok telepítése. Ezt a módszert használja, ha az Azure Portalon elérhető hivatkozásokkal, a szükséges összes követelmény fizetést lehetővé teszi. Ha már készen áll, létrehozásakor, üzembe helyezéséhez a cloud service, érdemes egyéni létrehozás használatával egyszerre mindkettő.

> [!NOTE]
> Ha közzé szeretné tenni a felhőszolgáltatás a Visual Studio Team Services (VSTS), gyorsan hozhat létre, és ezután vsts-ben történő közzététel beállítása az Azure gyors üzembe helyezés vagy az irányítópulton. További információkért lásd: [folyamatos Készregyártás az Azure használatával a Visual Studio Team Services által][TFSTutorialForCloudService], vagy tekintse meg a Súgó a **gyors üzembe helyezés** lapot.
>
>

## <a name="concepts"></a>Alapelvek
Alkalmazás üzembe helyezése az Azure-ban felhőszolgáltatásként három összetevőkre van szükség:

* **Szolgáltatás definíciója**  
  A felhő szolgáltatásdefiníciós fájl (.csdef) a szolgáltatásmodellt, beleértve a szerepkörök számát határozza meg.
* **Szolgáltatás konfigurációja**  
  A felhőszolgáltatás konfigurációs fájljához (.cscfg) biztosít a felhő konfigurációs beállításait szolgáltatást, és az egyes szerepköröket, beleértve a szerepkörpéldányok számát.
* **Szolgáltatási csomag**  
  A felhőszolgáltatás csomagjához (.cspkg) az alkalmazás kódja és konfigurációkat és a szolgáltatásdefiníciós fájlt tartalmazza.

További, valamint hogyan hozhat létre egy csomagot [Itt](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése
Cloud service telepítése előtt létre kell hoznia a felhőszolgáltatás csomagjához (.cspkg) az alkalmazás kódjában és a egy felhőszolgáltatás konfigurációs fájljához (.cscfg). Az Azure SDK eszközöket biztosít a szükséges központi telepítés fájlok előkészítése. Telepítheti az SDK-t a a [Azure letöltések](https://azure.microsoft.com/downloads/) lapon, a kívánt nyelven, amelyben az alkalmazás kódjában fejlesztéséhez.

Három cloud service szolgáltatás speciális konfigurációk van szükség, a service-csomag exportálása előtt:

* Ha egy felhőszolgáltatás, amely a Secure Sockets Layer (SSL) használ az adatok titkosításához, telepítendő [-alkalmazás konfigurálása](cloud-services-configure-ssl-certificate-portal.md#modify) az SSL-hez.
* Ha szeretne konfigurálni a szerepkörpéldányok, távoli asztali kapcsolatok [a szerepkörök konfigurálása](cloud-services-role-enable-remote-desktop-new-portal.md) a távoli asztal.
* Ha szeretne konfigurálása a felhőszolgáltatásokhoz tartozó figyelési részletes, engedélyezze Azure Diagnostics a felhőszolgáltatás számára. *Minimális figyelési* (az alapértelmezett szint figyelés) szerepkörpéldányokat (virtuális gépek) a gazdagép operációs rendszerekből gyűjtött teljesítményszámlálókat használ. *Részletes figyelési* teljesítményadatai engedélyezéséhez a kérelem feldolgozása során előforduló problémák teljesítményének szorosabb elemzése a szerepkörpéldányok belül további metrikákat gyűjt. Az Azure-diagnosztika engedélyezése, lásd: [engedélyezi a diagnosztikát az Azure-ban](cloud-services-dotnet-diagnostics.md).

Szeretne létrehozni egy felhőalapú szolgáltatás webes szerepkört vagy feldolgozói szerepkörök központi telepítései, kell [létrehozása a csomag](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Előkészületek
* Ha még nem telepítette az Azure SDK-t, kattintson a **Azure SDK telepítése** megnyitásához a [Azure letöltőoldala](https://azure.microsoft.com/downloads/), és töltse le a kívánt nyelven, amelyben a kód fejlesztéséhez készült SDK. (Ezt a lehetőséget rendelkezni fog.)
* Ha minden szerepkör példányai szükséges tanúsítvány, a tanúsítványok létrehozása. A cloud services és a egy titkos kulcs egy .pfx-fájl szükséges. Az Azure-bA a tanúsítványok létrehozása és üzembe helyezése a felhőszolgáltatás tölthet fel.

## <a name="create-and-deploy"></a>Létrehozása és üzembe helyezése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **erőforrás létrehozása > Compute**, és görgessen le a, és kattintson a **Felhőszolgáltatás**.

    ![A felhőszolgáltatások közzététele](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Az új **Felhőszolgáltatás** panelen adjon meg egy értéket a **DNS-név**.
4. Hozzon létre egy új **erőforráscsoport** , vagy válasszon ki egy meglévőt.
5. Válasszon ki egy **helyet**.
6. Kattintson a **csomag**. Ekkor megnyílik a **csomag feltöltése** ablaktáblán. Adja meg a kötelező mezőket. Ha egyetlen példányt tartalmaz, a szerepkörökben, győződjön meg róla **telepítse, még akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** van kiválasztva.
7. Győződjön meg arról, hogy **üzembe helyezésének megkezdéséhez** van kiválasztva.
8. Kattintson a **OK** bezárul, amely a **csomag feltöltése** ablaktáblán.
9. Ha nem rendelkezik a tanúsítványok hozzáadása, kattintson a **létrehozás**.

    ![A felhőszolgáltatások közzététele](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése
Ha a központi telepítési csomag volt [tanúsítványok](cloud-services-configure-ssl-certificate-portal.md#modify), most már feltöltheti a tanúsítványt.

1. Válassza ki **tanúsítványok**, majd a a **tanúsítványok hozzáadása** ablaktáblán válassza ki az SSL-tanúsítvány .pfx fájlját, és adja meg a **jelszó** a tanúsítvány
2. Kattintson a **Attach tanúsítvány**, és kattintson a **OK** a a **tanúsítványok hozzáadása** ablaktáblán.
3. Kattintson a **létrehozás** a a **Felhőszolgáltatás** ablaktáblán. Ha az üzemelő példány elérte a **készen** állapotát, továbbléphet a következő lépéseket.

    ![A felhőszolgáltatások közzététele](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ellenőrizze a telepítés sikeresen befejeződött
1. Kattintson a felhőszolgáltatás-példányok.

    Az állapot jelenítsen meg, hogy van-e a szolgáltatás **futó**.
2. A **Essentials**, kattintson a **webhely URL-címe** a felhőszolgáltatás megnyitása egy webböngészőben.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációs](cloud-services-how-to-configure-portal.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatások kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate-portal.md).
