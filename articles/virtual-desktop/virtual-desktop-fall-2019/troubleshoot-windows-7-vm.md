---
title: Windows 7 rendszerű virtuális gépek – Azure
description: Windows 7 rendszerű virtuális gépek (VM-EK) hibáinak elhárítása Windows rendszerű virtuális asztali környezetben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 74f2e22bcc9d75070e4f7af304f92d9c5640ca7a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615278"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows 7 rendszerű virtuális gép hibaelhárítása a Windows Virtual Desktopban

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager.

Ez a cikk a Windows rendszerű virtuális asztali munkamenetgazda virtuális gépek (VM-EK) konfigurálásakor felmerülő problémák elhárítására használható.

## <a name="known-issues"></a>Ismert problémák

A Windows rendszerű virtuális asztali számítógépeken a Windows 7 nem támogatja a következő funkciókat:

- Virtualizált alkalmazások (RemoteApp)
- Időzóna-átirányítás
- Automatikus DPI-méretezés

A Windows virtuális asztal csak a Windows 7 teljes asztalait tudja virtualizálni.

Habár az automatikus DPI-méretezés nem támogatott, manuálisan módosíthatja a virtuális gép felbontását úgy, hogy a jobb gombbal az Távoli asztal-ügyfél ikonjára kattint, és kiválasztja a **feloldás**elemet.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Hiba: nem érhető el a Távoli asztal felhasználói csoport

Ha a Windows rendszerű virtuális asztal nem találja Önt vagy a felhasználók hitelesítő adatait a Távoli asztal felhasználói csoportban, a következő hibaüzenetek egyike jelenhet meg:

- "Ez a felhasználó nem tagja a Távoli asztal felhasználói csoportnak"
- "Engedélyeket kell adni a bejelentkezéshez Távoli asztali szolgáltatások"

A hiba elhárításához vegye fel a felhasználót a Távoli asztal felhasználói csoportba:

1. Nyissa meg az Azure Portalt.
2. Válassza ki azt a virtuális gépet, amelyen a hibaüzenet jelenik meg.
3. Válassza **a parancs futtatása**lehetőséget.
4. Futtassa a következő parancsot, `<username>` és a helyére írja be a hozzáadni kívánt felhasználó nevét:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```