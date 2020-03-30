---
title: Windows 7 virtuális gépek Windows Virtual Desktop - Azure
description: A Windows 7 virtuális gépek (VM-ek) problémáinak megoldása Windows virtuális asztali környezetben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a2ff3f6fa9896e45ecd6ab40d40d46a046edf1cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127386"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop"></a>Windows 7 rendszerű virtuális gép hibaelhárítása a Windows Virtual Desktopban

Ebből a cikkből elháríthatja a Windows virtual desktop munkamenetgazda virtuális gépek (VM-ek) konfigurálásakor felmerülő problémákat.

## <a name="known-issues"></a>Ismert problémák

A Windows 7 windowsos virtuális asztalokon nem támogatja a következő szolgáltatásokat:

- Virtualizált alkalmazások (RemoteApps)
- Időzóna-átirányítás
- Automatikus DPI-méretezés

A Windows Virtual Desktop csak a Windows 7 teljes asztalait virtualizálhatja.

Bár az automatikus DPI-méretezés nem támogatott, manuálisan módosíthatja a felbontást a virtuális gépen, ha a jobb gombbal a Távoli asztali ügyfélben lévő ikonra kattint, és a **Resolution parancsot választja.**

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Hiba: Nem érhető el a Távoli asztali felhasználó csoport

Ha a Windows virtuális asztal nem találja Önt vagy a felhasználók hitelesítő adatait a Távoli asztali felhasználó csoportban, az alábbi hibaüzenetek egyike jelenhet meg:

- "Ez a felhasználó nem tagja a Távoli asztali felhasználó csoportnak"
- "Engedélyt kell kapnia a Távoli asztali szolgáltatásokon keresztül történő bejelentkezéshez"

A hiba kijavításához adja hozzá a felhasználót a Távoli asztali felhasználó csoporthoz:

1. Nyissa meg az Azure Portalt.
2. Válassza ki azt a virtuális gépet, amelyen a hibaüzenet et látta.
3. Válassza **a Parancs futtatása lehetőséget.**
4. Futtassa a `<username>` következő parancsot a hozzáadni kívánt felhasználó nevével helyettesítve:
   
   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```