---
title: "A Linux virtuális gép távoli asztal |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse és konfigurálja a klasszikus telepítési modell a Microsoft Azure Linux virtuális gép kapcsolódni a távoli asztal"
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: mingzhan
ms.openlocfilehash: 2ad497a0244f9c7cdad34faf807cc9ed10ea704d
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Kapcsolódás Microsoft Azure-beli linuxos VM-hez a Távoli asztal használatával
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk frissített Resource Manager verziója, lásd: [Itt](../use-remote-desktop.md).

## <a name="overview"></a>Áttekintés
RDP (Remote Desktop Protocol) a saját fejlesztésű protokollja használt Windows. A Microsoft használatát RDP távolról csatlakozni a Linux virtuális gépek (virtuális gép)?

Ez az útmutató Erre azért van szükség a válasz! Azt, hogy telepítse és a Microsoft Azure Linux virtuális gép meg, amely lehetővé teszi, hogy csatlakozni a távoli asztalról a Windows-gépről a config xrdp segítségével. Ubuntu, OpenSUSE vagy az ebben az útmutatóban példaként futó Linux virtuális gép használjuk.

A xrdp eszköze egy nyílt forráskódú RDP-kiszolgáló, amely lehetővé teszi a kapcsolódást a Linux-kiszolgálóra a távoli asztalról a Windows-gépről. RDP (virtuális hálózat számítástechnikai) VNC jobb teljesítményt rendelkezik. VNC Renderelés JPEG minőségű grafikus használatával, és a lassú lehet, mivel az RDP gyors és egyszerű crystal.

> [!NOTE]
> Már rendelkeznie kell egy Microsoft Azure virtuális gépet. Hozzon létre, és a Linux virtuális gépet, a [Azure Linux virtuális gép oktatóanyag](createportal.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>Hozzon létre egy végpontot a távoli asztal
Használjuk az alapértelmezett végpont 3389-es távoli asztal Ez a dokumentum. Állítsa be, 3389 végpont `Remote Desktop` a Linux virtuális gépekre például alatt:

![Kép](./media/remote-desktop/endpoint-for-linux-server.png)

Ha nem tudja, hogyan állíthatja be a végpont a virtuális Gépet, tekintse meg [Ez az útmutató](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Gnome asztali telepítése
Csatlakoztassa a Linux virtuális Gépet keresztül `putty`, és telepítse `Gnome Desktop`.

Ubuntu használja:

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


OpenSUSE használja:

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Xrdp telepítése
Ubuntu használja:

    #sudo apt-get install xrdp

OpenSUSE használja:

> [!NOTE]
> Frissítse a OpenSUSE a verziójával, az alábbi parancsot használja. Az alábbi példa `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Indítsa el a xrdp, és állítsa be xdrp szolgáltatás állítja
OpenSUSE használja:

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Az Ubuntu xrdp fog legyen elindult, és automatikusan engedélyezve állítja, a telepítés után.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Ha a Ubuntu 12.04LTS később egy Ubuntu verzióját használja xfce használatával
Mert xrdp jelenlegi verziója nem támogatja a Gnome asztali Ubuntu verzióihoz Ubuntu 12.04LTS később, használjuk `xfce` asztali helyette.

A telepítendő `xfce`, használja ezt a parancsot:

    #sudo apt-get install xubuntu-desktop

Engedélyezze a `xfce` használja a következő parancsot:

    #echo xfce4-session >~/.xsession

A konfigurációs fájl szerkesztése `/etc/xrdp/startwm.sh`:

    #sudo vi /etc/xrdp/startwm.sh   

Adja hozzá a sort `xfce4-session` sora elé `/etc/X11/Xsession`.

Indítsa újra a xrdp szolgáltatást, használja ezt:

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Csatlakozás a Linux virtuális Gépet egy Windows-gépről
A Windows-gépen a távoli asztal ügyfél elindítása, és adjon meg a Linux virtuális gép DNS-nevét. Vagy a virtuális gép az Azure-portálon az irányítópult megnyitásához, és kattintson a `Connect` a Linux virtuális gép csatlakozni. Ebben az esetben a bejelentkezési ablak jelenik meg:

![Kép](./media/remote-desktop/no2.png)

Jelentkezzen be a felhasználónevet és jelszót a Linux virtuális gép.

## <a name="next-steps"></a>Következő lépések
Xrdp használatával kapcsolatos további információkért lásd: [http://www.xrdp.org/](http://www.xrdp.org/).
