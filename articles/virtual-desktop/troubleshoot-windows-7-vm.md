---
title: Windows 7 rendszerű virtuális gépek hibáinak megoldása a Windows rendszerű virtuális asztalon – Azure
description: Windows 7 rendszerű virtuális gépek (VM-EK) hibáinak elhárítása Windows rendszerű virtuális asztali környezetben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
ms.openlocfilehash: 7fdb46ac7cffc4eb6a791304c7a6b58378806296
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679892"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows 7 rendszerű virtuális gépek hibáinak megoldása a Windows rendszerű virtuális asztalon

Ez a cikk a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek (VM-EK) konfigurálásakor felmerülő problémák elhárítására használható.

## <a name="known-issues"></a>Ismert problémák

A Windows rendszerű virtuális asztali számítógépeken a Windows 7 nem támogatja a következő funkciókat:

- Virtualizált alkalmazások (RemoteApp)
- Időzóna-átirányítás
- Automatikus DPI-méretezés

A Windows virtuális asztal csak a Windows 7 teljes asztalait tudja virtualizálni.

Habár az automatikus DPI-méretezés nem támogatott, manuálisan módosíthatja a virtuális gép felbontását úgy, hogy a jobb gombbal az Távoli asztal-ügyfél ikonjára kattint, és kiválasztja a **feloldás**elemet.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Hiba: Nem érhető el a Távoli asztal felhasználói csoport

Ha a Windows rendszerű virtuális asztal nem találja Önt vagy a felhasználók hitelesítő adatait a Távoli asztal felhasználói csoportban, a következő hibaüzenetek egyike jelenhet meg:

- "Ez a felhasználó nem tagja a Távoli asztal felhasználói csoportnak"
- "Engedélyeket kell adni a bejelentkezéshez Távoli asztali szolgáltatások"

A hiba elhárításához vegye fel a felhasználót a Távoli asztal felhasználói csoportba:

1. Nyissa meg az Azure Portalt.
2. Válassza ki azt a virtuális gépet, amelyen a hibaüzenet jelenik meg.
3. Válassza **a parancs futtatása**lehetőséget.
4. Futtassa a következő parancsot `<username>` helyére a hozzáadni kívánt felhasználó nevével:
   
   ```cmd
   localgroup "Remote Desktop Users" <username> /add
   ```