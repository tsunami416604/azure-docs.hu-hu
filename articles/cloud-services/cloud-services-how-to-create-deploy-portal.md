---
title: Felhőszolgáltatás létrehozása és üzembe helyezése | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe felhőszolgáltatást az Azure Portalon.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 53f53976b20359afc45abe1b25ca60325b5d6a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386170"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Felhőszolgáltatás létrehozása és üzembe helyezése
Az Azure Portal kétféleképpen hozhat létre és helyezhet üzembe felhőalapú szolgáltatást: *gyors létrehozás* és *egyéni létrehozás.*

Ez a cikk bemutatja, hogyan használhatja a Gyorslétrehozás metódust egy új felhőszolgáltatás létrehozásához, majd **az Upload** használatával egy felhőalapú szolgáltatáscsomag feltöltéséhez és üzembe helyezéséhez az Azure-ban. Ha ezt a módszert használja, az Azure Portal elérhetővé teszi a kényelmes linkeket az összes követelmény teljesítéséhez menet közben. Ha készen áll a felhőszolgáltatás üzembe helyezésére, amikor létrehozza, mindkettőt megteheti egyszerre az egyéni létrehozás használatával.

> [!NOTE]
> Ha azt tervezi, hogy közzéteszi a felhőszolgáltatást az Azure DevOps-ból, használja a Gyorslétrehozás szolgáltatást, majd állítsa be az Azure DevOps-közzétételt az Azure gyorsútmutatóból vagy az irányítópultról. További információ: [Folyamatos kézbesítés az Azure-ba az Azure DevOps használatával,][TFSTutorialForCloudService]vagy tekintse meg a súgót a **Gyorsindítás** laphoz.
>
>

## <a name="concepts"></a>Alapelvek
Három összetevő szükséges egy alkalmazás felhőszolgáltatásként való üzembe helyezéséhez az Azure-ban:

* **Szolgáltatás definíciója**  
  A felhőszolgáltatás-definíciós fájl (.csdef) határozza meg a szolgáltatásmodellt, beleértve a szerepkörök számát is.
* **Szolgáltatás konfigurációja**  
  A felhőszolgáltatás konfigurációs fájlja (.cscfg) biztosítja a felhőszolgáltatás és az egyes szerepkörök konfigurációs beállításait, beleértve a szerepkörpéldányok számát is.
* **Szolgáltatási csomag**  
  A szolgáltatáscsomag (.cspkg) tartalmazza az alkalmazáskódot és konfigurációkat, valamint a szolgáltatásdefiníciós fájlt.

Ezekről és a csomag létrehozásáról [itt](cloud-services-model-and-package.md)olvashat bővebben.

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése
A felhőalapú szolgáltatás üzembe helyezése előtt létre kell hoznia a felhőszolgáltatás-csomagot (.cspkg) az alkalmazáskódból és egy felhőszolgáltatás konfigurációs fájljából (.cscfg). Az Azure SDK eszközöket biztosít a szükséges központi telepítési fájlok előkészítéséhez. Az SDK-t az [Azure Downloads](https://azure.microsoft.com/downloads/) lapról telepítheti, azon a nyelven, amelyen az alkalmazáskód fejlesztését szeretné.

A szolgáltatáscsomag exportálása előtt három felhőszolgáltatás-szolgáltatás speciális konfigurációt igényel:

* Ha olyan felhőszolgáltatást szeretne telepíteni, amely Secure Sockets Layer (SSL) adattitkosítást használ, [konfigurálja az alkalmazást](cloud-services-configure-ssl-certificate-portal.md#modify) SSL-re.
* Ha a Távoli asztali kapcsolatokat szerepkörpéldányokra szeretné konfigurálni, [konfigurálja a szerepköröket a](cloud-services-role-enable-remote-desktop-new-portal.md) Távoli asztal hoz.
* Ha részletes figyelést szeretne konfigurálni a felhőszolgáltatáshoz, engedélyezze az Azure Diagnostics szolgáltatást a felhőszolgáltatáshoz. *Minimális figyelés* (az alapértelmezett figyelési szint) a gazdaoperációs rendszerekből a szerepkörpéldányok (virtuális gépek) teljesítményszámlálóit használja. *Részletes figyelési* gyűjt további metrikák teljesítményadatok alapján a szerepkör példányok, hogy közelebbi elemzése során felmerülő problémák az alkalmazás feldolgozása során. Az Azure Diagnosztika engedélyezéséről a [Diagnosztika engedélyezése az Azure-ban](cloud-services-dotnet-diagnostics.md)című témakörben talál.

Ha webes szerepköröket vagy feldolgozói szerepköröket központi telepítéssel szeretne létrehozni, létre kell [hoznia a szolgáltatási csomagot.](cloud-services-model-and-package.md#servicepackagecspkg)

## <a name="before-you-begin"></a>Előkészületek
* Ha még nem telepítette az Azure SDK-t, kattintson az **Azure SDK telepítése** gombra az [Azure Downloads lap](https://azure.microsoft.com/downloads/)megnyitásához, majd töltse le az SDK-t arra a nyelvre, amelyen a kódot fejleszteni szeretné. (Erre később lehetősége lesz.)
* Ha bármelyik szerepkörpéldányhoz tanúsítvány szükséges, hozza létre a tanúsítványokat. A felhőszolgáltatásokhoz .pfx fájlra van szükség, amelynek kulcsa titkos kulcs. A tanúsítványokat feltöltheti az Azure-ba a felhőszolgáltatás létrehozása és üzembe helyezése során.

## <a name="create-and-deploy"></a>Létrehozás és üzembe helyezés
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson **az Erőforrás > számítás**létrehozása elemre, majd görgessen le a **Felhőszolgáltatás**elemre.

    ![A felhőszolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Az új **Felhőszolgáltatás** ablaktáblán adja meg a **DNS-név**értékét.
4. Hozzon létre egy új **erőforráscsoportot,** vagy jelöljön ki egy meglévőt.
5. Válasszon egy **helyet**.
6. Kattintson **a Csomag gombra.** Ezzel megnyitja a **Csomag feltöltése** ablaktáblát. Töltse ki a szükséges mezőket. Ha bármelyik szerepkör egyetlen példányt tartalmaz, győződjön meg **arról, hogy a telepítés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz,** ki van jelölve.
7. Győződjön meg arról, hogy a **Központi telepítés indítása** jelölőnégyzet be van jelölve.
8. Kattintson **az OK gombra,** amely bezárja a **Csomag feltöltése** ablaktáblát.
9. Ha nincs hozzáaktatok, kattintson a **Létrehozás gombra.**

    ![A felhőszolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése
Ha a központi telepítési csomag tanúsítványok használatára lett [konfigurálva,](cloud-services-configure-ssl-certificate-portal.md#modify)most feltöltheti a tanúsítványt.

1. Válassza a **Tanúsítványok**lehetőséget, majd a **Tanúsítványok hozzáadása** ablaktáblán jelölje ki az SSL tanúsítvány .pfx fájlt, majd adja meg a tanúsítvány **jelszavát,**
2. Kattintson **a Tanúsítvány csatolása**gombra, majd a **Tanúsítványok hozzáadása** ablaktáblán kattintson az **OK** gombra.
3. Kattintson a **Felhőszolgáltatás** ablaktáblájának **Létrehozás** gombjára. Amikor a központi telepítés elérte a **Ready** állapotot, továbbléphet a következő lépésekkel.

    ![A felhőszolgáltatás közzététele](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>A telepítés sikeres befejezésének ellenőrzése
1. Kattintson a felhőszolgáltatás-példányra.

    Az állapotnak azt kell mutatnia, hogy a szolgáltatás **fut.**
2. Az **Essentials csoportban**kattintson a **Webhely URL-címére** a felhőszolgáltatás webböngészőben való megnyitásához.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>További lépések
* [A felhőszolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* Egyéni [tartománynév konfigurálása](cloud-services-custom-domain-name-portal.md).
* [A felhőszolgáltatás kezelése.](cloud-services-how-to-manage-portal.md)
* [Ssl-tanúsítványok konfigurálása](cloud-services-configure-ssl-certificate-portal.md).



