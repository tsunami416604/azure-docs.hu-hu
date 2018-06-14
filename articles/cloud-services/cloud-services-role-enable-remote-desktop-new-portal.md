---
title: Az Azure felhőalapú szolgáltatások szerepkör távoli asztali kapcsolat engedélyezése |} Microsoft Docs
description: Az azure cloud service alkalmazás távoli asztali kapcsolatok lehetővé tételéhez konfigurálása
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
ms.author: mmccrory
ms.openlocfilehash: 2169fd95f51b468770a2e1e4c185d493babf220f
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877364"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Engedélyezze a távoli asztali kapcsolat egy szerepkör esetén az Azure Cloud Services csomag

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A távoli asztal lehetővé teszi az asztalon, egy Azure-beli szerepkör eléréséhez. A távoli asztali kapcsolat segítségével hibaelhárítását és diagnosztizálását az alkalmazás futtatása során.

Engedélyezheti a távoli asztali kapcsolat létrehozása a szerepkörben fejlesztése során a távoli asztal modulok belefoglalja a szolgáltatás definíciós, vagy választhatja azt is, a távoli asztal bővítményével távoli asztal engedélyezése. Az előnyben részesített megoldás, a távoli asztali bővítmény használatára, mert a távoli asztal az alkalmazás nem kell újratelepíteni az alkalmazás központi telepítése után is engedélyezheti.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>A távoli asztal konfigurálása az Azure-portálon

Az Azure-portálon a távoli asztali bővítmény megközelítést használ, így a távoli asztal az alkalmazás központi telepítése után is engedélyezheti. A **távoli asztal** beállításait a felhőalapú szolgáltatás lehetővé teszi a távoli asztal engedélyezéséhez módosítsa a helyi rendszergazda fiók használatával kapcsolódik a virtuális gépek, a tanúsítvány-hitelesítésben használt, és beállíthatja a lejárati idejét az adatok.

1. Kattintson a **Felhőszolgáltatások**, a felhőalapú szolgáltatás nevét, majd válassza ki és **távoli asztal**.

    ![Cloud services távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Válassza ki, hogy szeretné-e a távoli asztal engedélyezése egy adott szerepkör vagy minden szerepkört, majd módosítsa a kapcsoló értékének **engedélyezve**.

3. Adja meg a felhasználónevet, jelszót, lejárati és tanúsítvány kötelező mezők.

    ![Cloud services távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Ha először a távoli asztal engedélyezése, és bejelöli újraindul összes szerepkörpéldányt **OK** (jelölő). Újraindítás megakadályozása érdekében a jelszó titkosításához használt tanúsítványról telepítenie kell a szerepkört. Egy újraindítás érdekében [töltse fel a tanúsítványt a felhőalapú szolgáltatáshoz](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) és térjen vissza ezt a párbeszédpanelt.

4. A **szerepkörök**, válassza ki a frissíteni, vagy válassza ki a szerepkör **összes** összes szerepköre tekintetében.

5. A konfigurációs módosítások befejezése után válassza ki a **mentése**. Eltarthat egy kis ideig, mielőtt a szerepkörpéldányok készen áll kapcsolatok fogadására.

## <a name="remote-into-role-instances"></a>A szerepkörpéldányok távoli

A távoli asztal engedélyezése a szerepkörök után közvetlenül az Azure portálról kapcsolatot is kezdeményezhető:

1. Kattintson a **példányok** megnyitásához a **példányok** beállításait.
2. Válassza ki a szerepkör példánya, amely a távoli asztal konfigurálva van.
3. Kattintson a **Connect** a szerepkörpéldányhoz RDP-fájl letöltésére.

    ![Cloud services távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kattintson a **nyitott** , majd **Connect** a távoli asztali kapcsolat elindításához.

>[!NOTE]
> Ha a felhőszolgáltatás ülve mögött egy NSG-t, szükség lehet létrehozhat szabályokat, amelyek lehetővé teszik a forgalom a portokon **3389-es** és **20000**.  A távoli asztal-portot használja **3389-es**.  Felhőalapú szolgáltatás példányainak kerül, így nem közvetlenül szabályozhatja, hogy melyik példányt való csatlakozáshoz.  A *RemoteForwarder* és *RemoteAccess* ügynökök kezelése RDP-forgalmát, és az ügyfél küldése az RDP cookie, és adjon meg egy egyedi példány való csatlakozáshoz.  A *RemoteForwarder* és *RemoteAccess* ügynökök szükséges, hogy a port **20000*** van nyitva, ami megakadályozhatja, ha egy NSG.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
