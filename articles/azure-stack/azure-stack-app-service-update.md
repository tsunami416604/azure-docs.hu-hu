---
title: Az Azure App Service Azure veremben frissítése |} Microsoft Docs
description: Részletes útmutatást az Azure App Service Azure veremben frissítése
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.openlocfilehash: c822f25e25953b8709f481e51d6a63e6a912a60a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="update-azure-app-service-on-azure-stack"></a>Az Azure App Service Azure veremben frissítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

> [!IMPORTANT]
> A 1804 frissítés alkalmazásához a integrált Azure verem rendszerre, vagy telepítheti a legújabb Azure verem szoftverfejlesztői készlet Azure App Service 1.2 telepítése előtt.
>
>

Ez a cikk utasításait követve frissíthet a [App Service erőforrás-szolgáltató](azure-stack-app-service-overview.md) telepített Azure verem környezetben, amely kapcsolódik az internethez.

> [!IMPORTANT]
> A frissítés előtt, győződjön meg arról, hogy már végrehajtotta a [az Azure-verem erőforrás-szolgáltató az Azure App Service telepítése](azure-stack-app-service-deploy.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Az App Service resource provider telepítőjének futtatása

A folyamat során a frissítés tartalma:

* Az App Service a korábbi központi telepítés észlelése
* Készítse elő a csomagokat és a telepítendő összes OSS-tárak új verzióját
* Töltse fel a tároló
* Az összes App Service-szerepkör frissítése (vezérlők, a felügyeleti, előtér-, a közzétevő és a munkavégző szerepkörök)
* Az App Service méretezésicsoport-definícióinak frissítése
* Az App Service erőforrás-szolgáltató jegyzékfájlját frissítése

> [!IMPORTANT]
> Az App Service telepítőjét olyan számítógépen, amely képes elérni az Azure verem rendszergazda Azure Resource Manager-végpont kell futtatni.
>
>

Frissítse az üzemelő példányt, az App Service Azure veremben, kövesse az alábbi lépéseket:

1. Töltse le a [App Service-telepítő](https://aka.ms/appsvcupdate2installer)

2. Appservice.exe futtatása rendszergazdaként

    ![Az App Service-telepítő][1]

3. Kattintson a **App szolgáltatás telepítése vagy frissítése a legújabb verzióra.**

4. Tekintse át és fogadja el a Microsoft szoftverlicenc-szerződést, és kattintson **következő**.

5. Tekintse át és fogadja el a külső licencfeltételeket majd **következő**.

6. Győződjön meg arról, hogy az Azure verem Azure Resource Manager-végpont és az Active Directory-bérlő adatok helyesek. Azure verem szoftverfejlesztői készlet üzembe helyezése során az alapértelmezett beállításokat használja, ha elfogadhatja az alapértelmezett értékeket itt. Azonban Ha testreszabta a beállítások Azure verem üzembe helyezésekor, szerkesztenie kell a ebben az ablakban megfelelően, hogy az értékek is. Például, ha a tartományi utótag *mycloud.com*, módosítsa a Azure verem Azure Resource Manager-végpontot kell *management.region.mycloud.com*. Miután meggyőződött róla, hogy az adatait, kattintson a **következő**.

    ![Az Azure verem felhőalapú információk][2]

7. A következő lapon:

   1. Kattintson a **Connect** megjelenítő gombra a **Azure verem előfizetések** mezőbe.
        * Azure Active Directory (Azure AD) használata, adja meg az Azure AD rendszergazdai fiókot és Azure verem telepítésekor megadott jelszót. Kattintson a **bejelentkezés**.
        * Ha Active Directory összevonási szolgáltatások (AD FS) használ, adja meg a rendszergazdai fiókjával. Például *cloudadmin@azurestack.local*. Írja be a jelszót, és kattintson a **bejelentkezés**.
   2. Az a **Azure verem előfizetések** mezőben válassza a **alapértelmezett szolgáltató előfizetés**.
   3. Az a **Azure verem helyek** válassza ki a helyet, amely megfelel a régióban való telepítése esetén. Válassza például **helyi** Ha a központi telepítése az Azure verem szoftverfejlesztői készlet.
   4. Ha egy meglévő App Service-telepítés fel van derítve, majd az erőforrás csoport és a tárolási fiók lesz lehet feltöltve, szürkén jelenik meg.
   5. Kattintson a **következő** áttekintheti a frissítési összefoglalót.

    ![Az App Service telepítését Észleltük][3]

8. Az összefoglalás lapon:
   1. Ellenőrizze a kiválasztott beállítások. Módosításához használja a **előző** gombokra kattintva látogasson el az előző lapokra.
   2. Ha a konfiguráció helyes, jelölje be a jelölőnégyzetet.
   3. A frissítés elindításához kattintson **következő**.

       ![Az App Service frissítési összefoglalót][4]

9. Frissítési folyamat oldalon:
    1. A frissítési folyamat nyomon követéséhez. A frissítés az App Service Azure veremben időtartama változó telepített szerepkör-példányok száma függ.
    2. Ha a frissítés sikeresen befejeződött, kattintson a **kilépési**.

        ![Az App Service-frissítés állapota][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md).

* [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)
