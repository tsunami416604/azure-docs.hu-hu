---
title: "A felhőszolgáltatás (klasszikus portál) konfigurálása |} Microsoft Docs"
description: "Útmutató az Azure felhőszolgáltatások konfigurálása. Ismerje meg, a felhőalapú szolgáltatás konfigurációja frissítése, és konfigurálja a távelérést a szerepkörpéldányok."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 4902f79d-ea91-41ca-89a4-7c818180ee5f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 39bb294c96ce0c12d91cf8b3488ac3e1a7b2f7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-cloud-services"></a>Felhőszolgáltatás konfigurálása
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-how-to-configure-portal.md)
> * [klasszikus Azure portál](cloud-services-how-to-configure.md)
> 
> 

A klasszikus Azure portálon konfigurálhatja a leggyakrabban használt beállításait egy felhőalapú szolgáltatás. Azok az ügyfeleink, akik közvetlenül szeretnék frissíteni a konfigurációs fájlokat, letölthetik a frissítendő szolgáltatáskonfigurációs fájlt, amelyet módosítás után feltölthetnek, így frissül a felhőszolgáltatás konfigurációja. A rendszer mindkét esetben az összes szerepkörpéldányon elvégzi a konfiguráció módosítását.

A klasszikus Azure portálon is lehetővé teszi [engedélyezése a távoli asztali kapcsolat egy szerepkör esetén az Azure Cloud Services csomag](cloud-services-role-enable-remote-desktop.md)

Azure is csak 99,95 % szolgáltatás rendelkezésre állásának biztosításához a konfigurációfrissítések során ha legalább két szerepkör minden egyes szerepkörhöz. Amely lehetővé teszi, hogy egy virtuális gép ügyfél kérelmek feldolgozásához, a másik frissítése közben. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Egy felhőalapú szolgáltatás módosítása
1. Az a [a klasszikus Azure portálon](http://manage.windowsazure.com/), kattintson a **Felhőszolgáltatások**, kattintson a nevére, a felhőalapú szolgáltatás, és kattintson a **konfigurálása**.
   
    ![Konfiguráció lap](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
   
    Az a **konfigurálása** lapon konfigurálja a figyelés, a frissítési beállítások, és válassza ki a vendég operációs rendszer és a szerepkörpéldányok készült. 
2. A **figyelési**, a figyelési szint beállítása részletes vagy a minimális és a diagnosztika kapcsolati karakterláncok szükséges részletes figyelés konfigurálása.
3. (A szerepkör szerint csoportosítva) szerepköreit frissítheti a következő beállításokat:
   
    * **Beállítások** megadott egyéb konfigurációs beállítások értékének módosításához a *ConfigurationSettings* elemei a szolgáltatás konfigurációs (.cscfg) fájljában.

    * **Tanúsítványok**  
        Módosítsa a tanúsítvány ujjlenyomatát, amely használatban van az SSL-titkosítást egy szerepkörhöz. Ha módosítani szeretné a tanúsítványt, akkor először fel kell töltenie az új tanúsítványt (a a **tanúsítványok** lap). Frissítse az ujjlenyomata a tanúsítvány karakterláncban jelennek meg a beállításokat.
4. A **operációs rendszer**, módosíthatja az operációsrendszer-családot vagy a szerepkörpéldányok verziója, vagy válasszon **automatikus** az operációs rendszer aktuális verzióját az automatikus frissítések engedélyezéséhez. Az operációs rendszer beállításait a webes és feldolgozói szerepkörök vonatkoznak, de nem befolyásolják a virtuális gépek.
   
    A telepítés során az operációs rendszer legújabb verzióját a összes szerepkör példánya telepítve van, és az operációs rendszerek alapértelmezés szerint automatikusan frissülnek. 
   
    Ha eltérő operációs rendszer futhat a kódban kompatibilitási követelményei miatt a felhőalapú szolgáltatás, választhat egy operációsrendszer-családot és verziót. Ha úgy dönt, hogy egy adott operációsrendszer-verzió, a rendszer felfüggeszti automatikus operációsrendszer-frissítések a felhőalapú szolgáltatáshoz. Szüksége lesz, annak érdekében, hogy az operációs rendszerek kapják a frissítéseket.
   
    Ha az alkalmazások is rendelkeznek a legújabb verziójával operációs rendszer összes kompatibilitási problémák megoldásához engedélyezheti az operációs rendszer verziójával értékre állításával automatikus operációsrendszer-frissítések **automatikus**. 
   
    ![Az operációs rendszer beállításai](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)
5. A konfigurációs beállítások mentéséhez, és azok leküldése a szerepkörpéldányok, kattintson a **mentése**. (Kattintson **elvetése** visszavonja a változtatásokat.) **Mentés** és **elvetése** kerülnek be a parancssávon, a beállítás módosítása után.

## <a name="update-a-cloud-service-configuration-file"></a>Egy felhőalapú szolgáltatás konfigurációs fájljának frissítése
1. Töltse le a felhőalapú szolgáltatás konfigurációs fájlját (.cscfg) az aktuális konfigurációval. Az a **konfigurálása** a felhőalapú szolgáltatás lapján kattintson **letöltése**. Kattintson a **mentése**, vagy kattintson a **Mentés másként** fájl mentéséhez.
2. Miután frissítette a szolgáltatás konfigurációs fájlja, töltse fel, és a konfigurációs frissítések alkalmazásához:
   
   1. Az a **konfigurálása** kattintson **feltöltése**.
      
       ![Konfigurációs feltöltése](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
   2. A **konfigurációs fájl**, használjon **Tallózás** válassza ki a frissített .cscfg-fájlt.
   3. Ha a felhőalapú szolgáltatás, amely csak egy példányban szerepköröket tartalmaz, válassza ki a **konfiguráció alkalmazásához, még akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** melletti jelölőnégyzetet, hogy folytatja a szerepkörök a konfigurációs frissítésének engedélyezése.
      
       Kivéve, ha legalább két példánya minden definiálásához Azure nem garantálható, hogy legalább 99,95 % során frissíteni a konfigurációt a felhőalapú szolgáltatás rendelkezésre állását. További információkért lásd: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/).
   4. Kattintson a **OK** (jelölő). 

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [felhőalapú szolgáltatás telepítése](cloud-services-how-to-create-deploy.md).
* Konfigurálja a [egyéni tartománynév](cloud-services-custom-domain-name.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage.md).
* [Engedélyezze a távoli asztali kapcsolat egy szerepkör esetén az Azure Cloud Services csomag](cloud-services-role-enable-remote-desktop.md)
* Konfigurálása [ssl-tanúsítványok](cloud-services-configure-ssl-certificate.md).

