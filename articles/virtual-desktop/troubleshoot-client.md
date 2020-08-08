---
title: Távoli asztal-ügyfél Windows rendszerű virtuális asztalának hibáinak megoldása – Azure
description: Az ügyfélkapcsolatok Windows virtuális asztali bérlői környezetben való beállításakor felmerülő problémák megoldása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6ac2eb2632ad96538e965e4d1057d7e4a116c05d
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009392"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Az Távoli asztal-ügyfél hibáinak megoldása

Ez a cikk az Távoli asztal-ügyféllel kapcsolatos gyakori problémákat és azok javítását ismerteti.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>A Windows 7 vagy Windows 10 rendszerhez készült Távoli asztal-ügyfél nem válaszol, vagy nem nyitható meg

A 1.2.790 verziótól kezdődően alaphelyzetbe állíthatja a felhasználói adatait a névjegy lapról, vagy egy parancs használatával.

A következő paranccsal távolíthatja el a felhasználói adatait, visszaállíthatja az alapértelmezett beállításokat, és lemondhatja az összes munkaterületről.

```cmd
msrdcw.exe /reset [/f]
```

Ha az Távoli asztal-ügyfél korábbi verzióját használja, javasoljuk, hogy távolítsa el és telepítse újra az ügyfelet.

## <a name="web-client-wont-open"></a>A webes ügyfél nem nyílik meg

Először is tesztelje az internetkapcsolatot egy másik webhely megnyitásával a böngészőben; például: [www.Bing.com](https://www.bing.com).

Az **nslookup** használatával ellenőrizze, hogy a DNS képes-e a teljes tartománynév feloldására:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Próbáljon meg csatlakozni egy másik ügyfélhez, például Távoli asztal Windows 7 vagy Windows 10 rendszerű ügyfélhez, és ellenőrizze, hogy meg tudja-e nyitni a webes ügyfelet.

### <a name="opening-another-site-fails"></a>Egy másik hely megnyitása sikertelen

Ezt általában hálózati kapcsolódási problémák vagy hálózati kimaradás okozza. Javasoljuk, hogy forduljon a hálózati ügyfélszolgálathoz.

### <a name="nslookup-cannot-resolve-the-name"></a>Az nslookup nem tudja feloldani a nevet

Ezt általában hálózati kapcsolódási problémák vagy hálózati kimaradás okozza. Javasoljuk, hogy forduljon a hálózati ügyfélszolgálathoz.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Az ügyfél nem tud kapcsolatot létesíteni, de a hálózaton lévő többi ügyfél csatlakozhat

Ha a böngésző a webes ügyfél használata közben elindul vagy leáll, kövesse az alábbi utasításokat a megoldásához:

1. Indítsa újra a böngészőt.
2. Böngészőbeli cookie-k törlése. Lásd: [cookie-fájlok törlése az Internet Explorerben](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Törölje a böngésző-gyorsítótár tartalmát. Lásd: [böngésző gyorsítótárának törlése](https://binged.it/2RKyfdU)a böngészőben.
4. Nyissa meg a böngészőt privát módban.

## <a name="web-client-does-not-show-my-resources"></a>A webes ügyfél nem jeleníti meg az erőforrásokat

Először tekintse meg az Ön által használt Azure Active Directory fiókot. Ha már bejelentkezett egy másik Azure Active Directory fiókkal, mint amelyet a Windowsos virtuális asztalhoz kíván használni, akkor vagy jelentkezzen be, vagy használjon privát böngészőablakot.

Ha a Windows rendszerű virtuális asztalt (klasszikus) használja, a [jelen cikkben](./virtual-desktop-fall-2019/connect-web-2019.md) található webes ügyfél hivatkozásra kattintva csatlakozhat az erőforrásokhoz.

## <a name="web-client-stops-responding-or-disconnects"></a>A webes ügyfél nem válaszol vagy bontja a kapcsolatot

Próbáljon meg csatlakozni egy másik böngésző vagy ügyfél használatával.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Más böngészők és ügyfelek is meghibásodnak vagy nem nyithatók meg

Ha a problémák még a böngészők bekapcsolását követően is folytatódnak, előfordulhat, hogy a probléma nem a böngészővel, hanem a hálózattal van. Javasoljuk, hogy forduljon a hálózati ügyfélszolgálathoz.

## <a name="web-client-keeps-prompting-for-credentials"></a>A webes ügyfél megkéri a hitelesítő adatok megadását

Ha a webes ügyfél a hitelesítő adatok megadását kéri, kövesse az alábbi utasításokat:

1. Ellenőrizze, hogy helyes-e a webes ügyfél URL-címe.
2. Győződjön meg arról, hogy az Ön által használt hitelesítő adatok az URL-címhez kötött Windowsos virtuális asztali környezethez tartoznak.
3. Böngészőbeli cookie-k törlése. További részletek: [cookie-fájlok törlése az Internet Explorerben](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Törölje a böngésző-gyorsítótár tartalmát. További részletekért lásd: [böngésző gyorsítótárának törlése](https://binged.it/2RKyfdU)a böngészőben.
5. Nyissa meg a böngészőt privát módban.

## <a name="next-steps"></a>További lépések

- A Windows rendszerű virtuális asztalok és a eszkalációs sávok hibaelhárításával kapcsolatban lásd: [Hibaelhárítás – áttekintés, visszajelzés és támogatás](troubleshoot-set-up-overview.md).
- A Windows rendszerű virtuális asztali környezetek és a gazdagépek Windows rendszerű virtuális asztali környezetben való létrehozásakor felmerülő problémák elhárításához tekintse meg a [környezet és az alkalmazáskészlet létrehozása](troubleshoot-set-up-issues.md)című témakört
- A virtuális gép (VM) Windows rendszerű virtuális asztali gépen való konfigurálása során felmerülő problémák elhárításával kapcsolatban lásd: a [munkamenet-gazdagép virtuális gép konfigurálása](troubleshoot-vm-configuration.md).
- A PowerShell és a Windows virtuális asztal használatával kapcsolatos problémák elhárításához tekintse meg a [Windows rendszerű virtuális asztali PowerShell](troubleshoot-powershell.md)című témakört.
- A következő témakörben talál útmutatást a hibakereséshez [: oktatóanyag: Resource Manager-sablonok telepítésének hibája](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
