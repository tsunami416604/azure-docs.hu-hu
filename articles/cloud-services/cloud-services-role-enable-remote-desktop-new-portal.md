---
title: Engedélyezze a távoli asztali kapcsolat egy szerepkörhöz az Azure-Felhőszolgáltatásokat |} A Microsoft Docs
description: Az azure cloud service-alkalmazás lehetővé teszi a távoli asztali kapcsolatok konfigurálása
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 0c36dc5fb6b2754fc93a02e29d8d8ae74df36da7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963272"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Egy szerepkörhöz az Azure Cloud Services távoli asztali kapcsolat engedélyezése

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A távoli asztal segítségével elérheti az Azure-ban futó szerepkörök asztalát. Az alkalmazással kapcsolatos problémák diagnosztizálására futás közben és használhatja a távoli asztali kapcsolatot.

Engedélyezheti a távoli asztali kapcsolat a szerepkörben a fejlesztés során fel a szolgáltatás definíciós a távoli asztal modulok, vagy választhatja azt is, a távoli asztal bővítményével távoli asztal engedélyezése. A legmegfelelőbb módszer a távoli asztali bővítmény használatára, mert a távoli asztal a rendszer telepíti az alkalmazást nem kell az alkalmazás újbóli üzembe helyezése után is engedélyezheti.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>A távoli asztal konfigurálása az Azure Portalról

Az Azure Portalon a távoli asztali bővítmény módszert használ, így az alkalmazás központi telepítése után is engedélyezheti a távoli asztal. A **a távoli asztal** beállításait a cloud service lehetővé teszi a távoli asztal engedélyezése, módosítsa a helyi rendszergazdai fiók segítségével kapcsolódhat a virtuális gépek, a tanúsítvány hitelesítést használja, és beállíthatja a lejárati idejét a dátum.

1. Kattintson a **Cloud Services**, a felhőszolgáltatás neve, majd válassza ki és **a távoli asztal**.

    ![Felhőalapú szolgáltatások a távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Válassza ki, hogy szeretné-e a távoli asztal engedélyezése egy egyéni szerepkört, vagy minden szerepkör, majd módosítsa az értéket, hogy a váltó **engedélyezve**.

3. Adja meg a felhasználónevet, jelszót, lejárati és tanúsítvány a kötelező mezőket.

    ![Felhőalapú szolgáltatások a távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Ha először a távoli asztal engedélyezése, és válassza ki az összes szerepkörpéldány újraindul **OK** (pipa). Újraindítás elkerülése érdekében a szerepkör a jelszó titkosításához használt tanúsítványt kell telepíteni. Újraindítás elkerülése érdekében [töltsön fel egy tanúsítványt a felhőszolgáltatás](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) majd állítsa vissza ezt a párbeszédpanelt.

4. A **szerepkörök**, válassza ki a szerepkört szeretné frissíteni, vagy válassza ki **összes** összes szerepköre.

5. Amikor végzett a konfiguráció frissítéseit, válassza ki a **mentése**. Eltarthat egy kis ideig, mielőtt a szerepkörpéldányok készen áll kapcsolatok fogadására.

## <a name="remote-into-role-instances"></a>Távolról csatlakozzon a szerepkör példányai

Ha a távoli asztal engedélyezve van a szerepköröket, közvetlenül az Azure Portalról egy kapcsolatot is kezdeményezhet:

1. Kattintson a **példányok** megnyitásához a **példányok** beállításait.
2. Válassza ki a szerepkör-példány, amelyen a távoli asztal konfigurálva.
3. Kattintson a **Connect** a szerepkörpéldány az RDP-fájl letöltéséhez.

    ![Felhőalapú szolgáltatások a távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kattintson a **nyílt** , majd **Connect** a távoli asztali kapcsolat elindításához.

>[!NOTE]
> Ha a felhőszolgáltatás ülve mögött egy NSG-t, szükség lehet hozhat létre szabályokat, amelyek engedélyezik a forgalom a portokon **3389-es** és **20000**.  A távoli asztal-portot használja **3389-es**.  Felhőalapú szolgáltatás példányai terhelésű, így közvetlenül nem szabályozhatja, mely példányhoz való csatlakozáshoz.  A *RemoteForwarder* és *RemoteAccess* ügynökök kezelése RDP-forgalmat, és az ügyfél egy RDP-cookie küldhet, és adja meg egy egyedi példányt szeretne csatlakozni.  A *RemoteForwarder* és *RemoteAccess* ügynökök szükség erre a portra **20000*** van nyitva, ami blokkolhatja, ha van egy NSG-t.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
