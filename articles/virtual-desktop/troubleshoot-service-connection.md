---
title: A Windows rendszerű virtuális asztali kapcsolat hibáinak megoldása – Azure
description: Problémák megoldása a Windows rendszerű virtuális asztali bérlői környezetben a szolgáltatási kapcsolatok beállítása során.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5eb5602b8330906311df4a0d1f59bc5e5130237e
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089904"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).

Ez a cikk a Windows rendszerű virtuális asztali ügyfélkapcsolatokkal kapcsolatos problémák megoldásához használható.

## <a name="provide-feedback"></a>Visszajelzés küldése

Küldhet visszajelzést, és megvitathatja a Windows rendszerű virtuális asztali szolgáltatást a termék csapatával és a többi aktív közösségi taggal a [Windows Virtual Desktop technikai közösségében](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>A felhasználó csatlakozik, de semmi nem jelenik meg (nincs hírcsatorna)

A felhasználók elindíthatják Távoli asztal-ügyfeleket, és képesek hitelesíteni magukat, azonban a felhasználó nem lát ikonokat a webes felderítési hírcsatornában.

1. Győződjön meg arról, hogy a problémát jelentő felhasználó a következő parancssor használatával van hozzárendelve az alkalmazási csoportokhoz:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Győződjön meg arról, hogy a felhasználó a megfelelő hitelesítő adatokkal jelentkezik be.

3. Ha a webes ügyfél használatban van, ellenőrizze, hogy nincsenek-e gyorsítótárazott hitelesítő adatok.

4. Ha a felhasználó egy Azure Active Directory (AD) felhasználói csoport tagja, győződjön meg róla, hogy a felhasználói csoport a terjesztési csoport helyett biztonsági csoport. A Windows virtuális asztal nem támogatja az Azure AD terjesztési csoportokat.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>A felhasználó elveszíti a meglévő hírcsatornát, és nem jelenik meg távoli erőforrás (nincs hírcsatorna)

Ez a hiba általában akkor jelenik meg, ha egy felhasználó áthelyezte az előfizetést egy Azure AD-bérlőből egy másikba. Ennek eredményeképpen a szolgáltatás elveszti a felhasználói hozzárendeléseinek nyomon követését, mivel ezek továbbra is a régi Azure AD-bérlőhöz vannak kötve.

Ennek megoldásához mindössze annyit kell tennie, hogy újra hozzárendeli a felhasználókat az alkalmazás-csoportjaihoz.

## <a name="next-steps"></a>Következő lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezetek és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
