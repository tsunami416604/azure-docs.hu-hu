---
title: Linux virtuális géphez a távoli asztal |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti és konfigurálhatja a Microsoft Azure Linux virtuális gépek a klasszikus üzemi modellhez való kapcsolódáshoz a távoli asztal
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5e68774c3edb7d82fef388c593a6b96c52857be6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927740"
---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Kapcsolódás Microsoft Azure-beli linuxos VM-hez a Távoli asztal használatával
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk frissített erőforrás-kezelő verzióját, lásd: [Itt](../use-remote-desktop.md).

## <a name="overview"></a>Áttekintés
RDP (Remote Desktop Protocol) egy olyan saját fejlesztésű protokoll Windows használt. Hogyan használhatjuk RDP (virtuális gép) Linux virtuális gép távolról csatlakozni?

Ez az útmutató a választ ad meg! Ez segít, hogy telepítse és a Microsoft Azure Linux VM, amely lehetővé teszi egy Windows-gépről a távoli asztalon keresztül csatlakozzanak ahhoz a config xrdp. Linux rendszerű virtuális gép futtatása az Ubuntu vagy opensuse-alapú ebben az útmutatóban példaként használjuk.

A xrdp eszköze egy nyílt forráskódú RDP-kiszolgáló, amely lehetővé teszi, hogy a Linux-kiszolgáló csatlakoztatása a távoli asztalon keresztül egy Windows-gépen. RDP jobb teljesítményt, mint a VNC (virtuális hálózat Computing) rendelkezik. VNC rendereli JPEG minőségű képek használatával, és a lassú lehet, mivel az RDP gyors és crystal törlése.

> [!NOTE]
> Már rendelkeznie kell egy Microsoft Azure virtuális Gépet. Hozhat létre, és állítsa be a Linux virtuális Gépet, tekintse meg a [Azure Linux VM-oktatóanyag](createportal-classic.md).
> 
> 

## <a name="create-an-endpoint-for-remote-desktop"></a>A távoli asztal végpont létrehozása
Az alapértelmezett végpont 3389-es feljegyzett használjuk a távoli asztal. Állítsa be, 3389 végpont `Remote Desktop` alább például a Linux rendszerű virtuális:

![image](./media/remote-desktop/endpoint-for-linux-server.png)

Ha nem tudja, hogyan állíthatja be egy végpontot a virtuális géphez, tekintse meg [Ez az útmutató](setup-endpoints.md).

## <a name="install-gnome-desktop"></a>Gnome Desktop telepítése
A Linux rendszerű virtuális gép keresztül csatlakozni `putty`, és telepítse `Gnome Desktop`.

Ubuntu rendszeren használja:

```bash
sudo apt-get update
sudo apt-get install ubuntu-desktop
```

Az OpenSUSE használja:

```bash
sudo zypper install gnome-session
```

## <a name="install-xrdp"></a>Xrdp telepítése
Ubuntu rendszeren használja:

```bash
sudo apt-get install xrdp
```

Az OpenSUSE használja:

> [!NOTE]
> Frissítse az OpenSUSE-verzió az alábbi parancsban használt verzióval. Az alábbi példa `OpenSUSE 13.2`.
> 
> 

```bash
sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc
```

## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Indítsa el a xrdp és értékre beállított xdrp szolgáltatás rendszerindítás
Az OpenSUSE használja:

```bash
sudo systemctl start xrdp
sudo systemctl enable xrdp
```

Az Ubuntu, a xrdp fog indíthatók és automatikusan engedélyezve rendszerindítás, a telepítés után.

## <a name="using-xfce-if-you-are-using-an-ubuntu-version-later-than-ubuntu-1204lts"></a>Xfce használatával, ha később Ubuntu 12.04LTS használ egy Ubuntu-verzió
Mert xrdp jelenlegi verziója nem támogatja a Gnome asztali Ubuntu-verziók Ubuntu 12.04LTS később, ezzel `xfce` asztali helyette.

A telepítendő `xfce`, használja a következő parancsot:

```bash
sudo apt-get install xubuntu-desktop
```

Engedélyeznie kell a `xfce` ezzel a paranccsal:

```bash
echo xfce4-session >~/.xsession
```

A konfigurációs fájl szerkesztésével `/etc/xrdp/startwm.sh`:

```bash
sudo vi /etc/xrdp/startwm.sh   
```

Adja hozzá a sort `xfce4-session` sora elé `/etc/X11/Xsession`.

Indítsa újra a xrdp szolgáltatást, használja ezt:

```bash
sudo service xrdp restart
```

## <a name="connect-your-linux-vm-from-a-windows-machine"></a>A Linux rendszerű virtuális gép csatlakoztatása a Windows-gépről
Egy Windows-gépen a távoli asztali ügyfél elindítása és a Linux rendszerű virtuális gép DNS-nevet adjon meg. Vagy nyissa meg az irányítópultot, a virtuális gép az Azure Portalon, és kattintson a `Connect` Linuxos virtuális gép csatlakozni. Ebben az esetben a bejelentkezési ablak jelenik meg:

![image](./media/remote-desktop/no2.png)

Jelentkezzen be a felhasználónevet és jelszót a Linux rendszerű virtuális gép.

## <a name="next-steps"></a>További lépések
Xrdp használatával kapcsolatos további információkért lásd: [ http://www.xrdp.org/ ](http://www.xrdp.org/).
