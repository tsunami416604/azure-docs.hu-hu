---
title: A Windows rendszerű virtuális asztali kapcsolat hibáinak megoldása – Azure
description: Az ügyfélkapcsolatok Windows virtuális asztali bérlői környezetben való beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 06/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 56a31caeefb3589527fdbbac118fa3a544a0d1a1
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85208893"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows rendszerű virtuális asztali szolgáltatások kapcsolatai

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezetek és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
