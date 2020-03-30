---
title: Szerepkör távoli asztalának engedélyezése a portálon
titleSuffix: Azure Cloud Services
description: Az azure felhőszolgáltatás-alkalmazás konfigurálása távoli asztali kapcsolatok engedélyezéséhez
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247461"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Távoli asztali kapcsolat engedélyezése szerepkörhöz az Azure Cloud Servicesben

> [!div class="op_single_selector"]
> * [Azure-portál](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Vizuális stúdió](cloud-services-role-enable-remote-desktop-visual-studio.md)

A Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. A Távoli asztali kapcsolat segítségével elháríthatja és diagnosztizálhatja az alkalmazással kapcsolatos problémákat futás közben.

A távoli asztali kapcsolatot engedélyezheti a fejlesztés során, ha a szolgáltatásdefinícióba beírja a Távoli asztal modulokat, vagy engedélyezheti a Távoli asztal szolgáltatást a Távoli asztal bővítményen keresztül. Az előnyben részesített módszer a Távoli asztal bővítmény használata, mivel az alkalmazás telepítése után is engedélyezheti a Távoli asztal t, anélkül, hogy újra kellene telepítenie az alkalmazást.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Távoli asztal konfigurálása az Azure Portalról

Az Azure Portal a Távoli asztali bővítmény megközelítést használja, így az alkalmazás telepítése után is engedélyezheti a Távoli asztalt. A **felhőalapú** szolgáltatás Távoli asztal beállításai lehetővé teszik a Távoli asztal engedélyezését, a virtuális gépekhez való csatlakozáshoz használt helyi rendszergazdai fiók, a hitelesítéshez használt tanúsítvány és a lejárati dátum beállítását.

1. Kattintson a **Cloud Services**elemre, válassza ki a felhőszolgáltatás nevét, majd válassza a **Távoli asztal**lehetőséget.

    ![Felhőszolgáltatások távoli asztala](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Adja meg, hogy a Távoli asztal szolgáltatást egy adott szerepkörhöz vagy az összes szerepkörhöz szeretné-e engedélyezni, majd módosítsa a kapcsoló értékét **Engedélyezve értékre.**

3. Töltse ki a felhasználónévhez, jelszóhoz, lejárathoz és tanúsítványhoz szükséges mezőket.

    ![Felhőszolgáltatások távoli asztala](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > A távoli asztal első engedélyezésekor újraindul az összes szerepkörpéldány, és az **OK** gombra (pipa) kerül a program. Az újraindítás megakadályozása érdekében a jelszó titkosításához használt tanúsítványt telepíteni kell a szerepkörre. Az újraindítás megakadályozása [érdekében töltsön fel egy tanúsítványt a felhőszolgáltatáshoz,](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) majd térjen vissza erre a párbeszédpanelre.

4. A **Szerepkörök alkalmazásban**jelölje ki a frissíteni kívánt szerepkört, vagy válassza az **Összes** lehetőséget az összes szerepkörhöz.

5. A konfigurációs frissítések befejeztével válassza a **Mentés gombot.** Néhány percet vesz igénybe, amíg a szerepkörpéldányok készen állnak a kapcsolatok fogadására.

## <a name="remote-into-role-instances"></a>Távoli szereppéldányokba

Ha a Távoli asztal engedélyezve van a szerepkörökön, közvetlenül az Azure Portalról kezdeményezhet kapcsolatot:

1. Kattintson **a Példányok gombra** a **Példánybeállítások** megnyitásához.
2. Jelöljön ki egy olyan szerepkörpéldányt, amelyen a Távoli asztal konfigurálva van.
3. Kattintson a **Csatlakozás** gombra a szerepkörpéldány RDP-fájljának letöltéséhez.

    ![Felhőszolgáltatások távoli asztala](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. A Távoli asztali kapcsolat elindításához kattintson a **Megnyitás,** majd a **Csatlakozás** gombra.

>[!NOTE]
> Ha a felhőszolgáltatás egy NSG mögött van, előfordulhat, hogy olyan szabályokat kell létrehoznia, amelyek lehetővé teszik a forgalmat a **3389-es** és **a 20000-es**portokon.  A Távoli asztal a **3389-es portot**használja.  A Cloud Service-példányok terheléselosztásosak, így nem szabályozhatja közvetlenül, hogy melyik példányhoz csatlakozzon.  A *RemoteForwarder* és *a RemoteAccess* ügynökök kezelik az RDP-forgalmat, és lehetővé teszik az ügyfél számára, hogy RDP-cookie-t küldjön, és adjon meg egy egyedi példányt, amelyhez csatlakozni szeretne.  A *RemoteForwarder* és *a RemoteAccess* ügynökök megkövetelik, hogy a **20000*** port meg legyen nyitva, ami nsg esetén blokkolva lehet.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
