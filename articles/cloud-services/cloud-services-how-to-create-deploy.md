---
title: "Hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás, a gyors létrehozás módszerrel használata az Azure-ban."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 0ea78ccc-5e7d-40f8-bdb6-478c0eb0e265
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 2a2172a78bfd3ac923edbc9de366b035629dd27b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Hogyan hozhat létre és telepíthet egy felhőalapú szolgáltatás
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-create-deploy-portal.md)
> * [klasszikus Azure portál](cloud-services-how-to-create-deploy.md)
> 
> 

A klasszikus Azure portálon két módot biztosít hozhat létre és telepíthet egy felhőalapú szolgáltatás: **Gyorslétrehozás** és **egyéni létrehozás**.

Ez a témakör ismerteti, hogyan hozzon létre egy új felhőalapú szolgáltatás, majd a gyors létrehozás módszerrel **feltöltése** frissítése és telepítése az Azure cloud service csomag. Ha ezt a módszert használja, a klasszikus Azure portálon teszi követelményeinek befejezésének menet rendelkezésre álló kényelmes kapcsolatok. Ha készen áll a felhőalapú szolgáltatás telepítéséhez a létrehozás során, egyszerre csak használatával teheti **egyéni létrehozás**.

> [!NOTE]
> Ha közzé szeretné tenni a felhőszolgáltatás a Visual Studio Team Services (VSTS), **Gyorslétrehozás**, majd állítsa be a VSTS-közzététel **gyors üzembe helyezés** vagy az irányítópulton.
> 
> 

## <a name="concepts"></a>Alapelvek
Három összetevők szükségesek ahhoz, hogy az alkalmazás üzembe helyezése az Azure-ban felhőszolgáltatásként:

* **Szolgáltatásdefiníció**  
  A felhőalapú szolgáltatás definíciós fájl (.csdef) a szolgáltatásmodellt, beleértve a szerepkörök számának meghatározása.
* **Szolgáltatás konfigurációja**  
  A felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) a felhőre vonatkozó konfigurációs beállításokat tartalmazza, szolgáltatás és az egyes szerepkörök, beleértve a szerepkörpéldányok számát.
* **Szolgáltatáscsomag**  
  A szolgáltatás csomagba (.cspkg) tartalmazza az alkalmazás kódjában és konfigurációkat és a szolgáltatásdefiníciós fájlban.

Ezek és csomag létrehozásával kapcsolatos részletesebb [Itt](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése
Egy felhőalapú szolgáltatás telepítése előtt a felhőalapú szolgáltatás csomagba (.cspkg) alkalmazáskódjában és egy felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) kell létrehoznia. Az Azure SDK eszközöket biztosít a szükséges központi telepítés fájlok előkészítése. Az SDK telepítése a [Azure letölti](https://azure.microsoft.com/downloads/) oldalon, a nyelvet, amelyben az alkalmazás kódjában fejlesztéséhez.

Három cloud service funkciókhoz speciális konfigurációk service-csomag exportálása előtt:

* Ha szeretné központilag telepíteni egy felhőszolgáltatás, amely a Secure Sockets Layer (SSL) használ az adatok titkosításához, [állítsa be az alkalmazását](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) SSL-t.
* Ha a távoli asztal kapcsolatokat szerepkörpéldányt beállítani, a konfigurálni kívánt [a szerepkörök konfigurálása](cloud-services-role-enable-remote-desktop.md) a távoli asztal.
* Ha konfigurálni szeretné részletes figyelését a felhőalapú szolgáltatás, Azure diagnosztika engedélyezése a felhőalapú szolgáltatáshoz. *Minimális figyelési* (az alapértelmezett figyelési szint) az állomás operációs rendszereket a szerepkörpéldányok (virtuális gépek) szolgáltatástól összegyűjtött teljesítményszámlálókat használ. "Részletes figyelési * gyűjti a teljesítményadatokat belül a szerepkörpéldányok ahhoz, hogy szorosabb kérelem feldolgozása során előforduló problémák elemzése alapján további metrikákat. Azure Diagnostics engedélyezése regisztrációval, lásd: [diagnosztika engedélyezésével az Azure-ban](cloud-services-dotnet-diagnostics.md).

Felhőalapú szolgáltatás létrehozása a webes szerepkörök vagy feldolgozói szerepkörök példányai, le kell [a service-csomag létrehozása](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Előkészületek
* Ha még nem telepítette az Azure SDK-t, kattintson a **Azure SDK telepítése** megnyitásához a [Azure letöltőoldala](https://azure.microsoft.com/downloads/), és töltse le az SDK for a nyelvet, amelyben a kód kialakításához. (Összekapcsolta elvégezheti később lehetőséget.)
* Ha bármely szerepkörpéldányokat szükséges tanúsítvány, a tanúsítványok létrehozása. Cloud services és a titkos kulcs egy .pfx fájlba szükséges. Is [a tanúsítványok feltöltése az Azure-bA](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) létrehozásához és a felhőalapú szolgáltatás telepítéséhez.
* Ha azt tervezi, központi telepítéséhez a felhőalapú szolgáltatás affinitáscsoporthoz, hozzon létre az affinitáscsoportot. Az affinitáscsoportokban segítségével a felhőszolgáltatás és más Azure-szolgáltatásokkal telepítheti ugyanarra a helyre, egy régióban. Az affinitáscsoport hozhat létre a **hálózatok** a klasszikus Azure portál területén portált, a **Affinitáscsoportok** lap.

## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Útmutató: a gyors létrehozással felhőalapú szolgáltatás létrehozása
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **új**>**számítási**>**Felhőszolgáltatás**>**Gyorslétrehozás**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)
2. A **URL-cím**, adjon meg egy altartománynév eléréséhez az éles környezetekben a felhőalapú szolgáltatás nyilvános URL-címét használja. Az üzemi környezetek az URL-formátum: http://*myURL*. cloudapp.net.
3. A **régiót vagy Affinitáscsoportot**, válassza ki a földrajzi régiót vagy affinitáscsoportot a felhőalapú szolgáltatást. Az affinitáscsoportokban akkor válassza, ha azt szeretné, a felhőalapú szolgáltatás telepítéséhez és más Azure-szolgáltatásokkal régión belül ugyanazon a helyen.
4. Kattintson a **Felhőalapú szolgáltatás létrehozása** elemre.
   
    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)
   
    Az ablak alján az üzenetmezőben figyelemmel kísérheti a folyamat állapotát.
   
    A **Felhőszolgáltatások** területen jelenik meg új felhőalapú szolgáltatással nyílik meg. Amikor az állapot változik létrehozva, felhőalapú szolgáltatás létrehozása sikeresen befejeződött.
   
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)

## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Útmutató: a felhőalapú szolgáltatáshoz a tanúsítvány feltöltése
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, kattintson a nevére, a felhőalapú szolgáltatás, és kattintson a **tanúsítványok**.
   
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)
2. Jelölje be az **tanúsítvány feltöltése** vagy **feltöltése**.
3. A **fájl**, használjon **Tallózás** jelölje be a tanúsítvány (.pfx-fájl).
4. A **jelszó**, írja be a tanúsítványhoz tartozó titkos kulcs.
5. Kattintson a **OK** (jelölő).
   
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)
   
    A feltöltés üzenet területen lent látható módon az előrehaladását figyelemmel követheti. Ha befejeződött a feltöltés, a táblához hozzáadta a tanúsítványt. Az üzenet területen kattintson az OK gombra az üzenet bezárásához.
   
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Útmutató: a felhőalapú szolgáltatás telepítése
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, kattintson a nevére, a felhőalapú szolgáltatás, és kattintson a **irányítópult**.
2. Jelölje be az **töltse fel az új éles környezet** vagy **feltöltése**.
3. A **üzemelő példány címkéje**, adjon meg egy nevet az új központi telepítés – például MyCloudServicev4.
4. A **csomag**, használjon **Tallózás** jelölje be a felhőszolgáltatás csomagfájlját (.cspkg) használatára.
5. A **konfigurációs**, használjon **Tallózás** válassza ki a szolgáltatás konfigurálása fájlt (.cscfg) használatára.
6. Ha a felhőszolgáltatás szerepköröket tartalmazza, ha csak egy példánya, válassza ki a **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** jelölőnégyzetet ahhoz, hogy a telepítés folytatásához.
   
    Azure is csak 99,95 % való hozzáférés biztosítása a felhőalapú szolgáltatás karbantartási és a frissítések során legalább két példányt minden szerepkör-e. Ha szükséges, további szerepkörpéldányokat adhat meg a **méretezési** oldalon felhőszolgáltatás telepítése után. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).
7. Kattintson a **OK** (jelölő) a felhőalapú szolgáltatás telepítésének megkezdéséhez.
   
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)
   
    Az üzenet területen a telepítés állapotának figyelése Kattintson az OK gombra az üzenetet.
   
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ellenőrizze a telepítés sikeresen befejeződött
1. Kattintson a **irányítópult**.
   
    Az állapot jelenítsen meg, hogy van-e a szolgáltatás **futtató**.
2. A **gyors áttekintő**, kattintson a webhely URL-címet egy webböngészőben nyissa meg a felhőalapú szolgáltatás.
   
    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációs](cloud-services-how-to-configure.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage.md).
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate.md).

