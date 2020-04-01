---
title: Távoli asztali ügyfél – Windows Virtuális asztal – Azure – Távoli asztali ügyfél – Azure – problémamegoldás
description: Az ügyfélkapcsolatok Windows virtuális asztal bérlői környezetben történő beállításakor felmerülő problémák megoldása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473854"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>A Távoli asztali ügyfél – problémamegoldás

Ez a cikk a Távoli asztali ügyféllel kapcsolatos gyakori problémákat és azok javítását ismerteti.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>A Windows 7 vagy a Windows 10 Távoli asztali ügyfélalkalmazása nem válaszol, vagy nem nyitható meg

Az 1.2.790-es verziótól kezdve alaphelyzetbe állíthatja a felhasználói adatokat a Beet lapon vagy egy paranccsal.

A következő paranccsal eltávolíthatja a felhasználói adatokat, visszaállíthatja az alapértelmezett beállításokat, és leiratkozhat az összes munkaterületről.

```cmd
msrdcw.exe /reset [/f]
```

Ha a Távoli asztali ügyfél egy korábbi verzióját használja, javasoljuk, hogy távolítsa el és telepítse újra az ügyfelet.

## <a name="web-client-wont-open"></a>A webes ügyfél nem nyílik meg

Először tesztelje az internetkapcsolatot egy másik webhely megnyitásával a böngészőjében; például [www.bing.com](https://www.bing.com).

Az **nslookup** segítségével ellenőrizze, hogy a DNS képes-e feloldani a teljes tartománynnnt:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Próbáljon meg csatlakozni egy másik ügyfélhez, például a Windows 7 vagy a Windows 10 távoli asztali ügyfélprogramjához, és ellenőrizze, hogy meg tudja-e nyitni a webes ügyfelet.

### <a name="opening-another-site-fails"></a>Egy másik hely megnyitása sikertelen

Ezt általában hálózati csatlakozási problémák vagy hálózati kimaradás okozza. Javasoljuk, hogy lépjen kapcsolatba a hálózati támogatással.

### <a name="nslookup-cannot-resolve-the-name"></a>Az Nslookup nem tudja feloldani a nevet

Ezt általában hálózati csatlakozási problémák vagy hálózati kimaradás okozza. Javasoljuk, hogy lépjen kapcsolatba a hálózati támogatással.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Az ügyfél nem tud csatlakozni, de a hálózat többi ügyfele is csatlakozhat

Ha a böngésző a webes ügyfél használata közben működésbe lép, vagy nem működik, kövesse az alábbi utasításokat a hibaelhárításhoz:

1. Indítsa újra a böngészőt.
2. Törölje a böngésző cookie-kat. Lásd: [Cookie-fájlok törlése az Internet Explorerben](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Törölje a böngésző-gyorsítótár tartalmát. Lásd [a böngésző gyorsítótárának törlését a böngészőhöz.](https://binged.it/2RKyfdU)
4. Nyissa meg a böngészőt privát módban.

## <a name="web-client-stops-responding-or-disconnects"></a>A webes ügyfél nem válaszol vagy bontja a kapcsolatot

Próbáljon meg másik böngészővel vagy ügyféllel csatlakozni.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Más böngészők és ügyfelek is hibásan működnek, vagy nem nyitják meg

Ha a problémák a böngészőváltás után is fennállnak, akkor lehet, hogy a probléma nem a böngészővel, hanem a hálózattal van. Javasoljuk, hogy lépjen kapcsolatba a hálózati támogatással.

## <a name="web-client-keeps-prompting-for-credentials"></a>A webes ügyfél folyamatosan kéri a hitelesítő adatokat

Ha a webes ügyfél továbbra is hitelesítő adatokat kér, kövesse az alábbi utasításokat:

1. Ellenőrizze, hogy a webes ügyfél URL-címe helyes-e.
2. Ellenőrizze, hogy a használt hitelesítő adatok az URL-címhez kötött Windows virtuális asztal környezethez vannak-e.
3. Törölje a böngésző cookie-kat. További információt a [Cookie-fájlok törlése az Internet Explorerprogramban című témakörben talál.](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)
4. Törölje a böngésző-gyorsítótár tartalmát. További információt a [böngésző gyorsítótárának törlése a böngészőhöz.](https://binged.it/2RKyfdU)
5. Nyissa meg a böngészőt privát módban.

## <a name="next-steps"></a>További lépések

- A Windows virtuális asztal hibáinak és az eszkalációs pályáknak a [hibaelhárítás – áttekintés– áttekintés– visszajelzés és támogatás – áttekintést.](troubleshoot-set-up-overview.md)
- A bérlői és gazdagépkészlet Windows virtuális asztali környezetben való létrehozása során felmerülő problémák elhárításához olvassa el a [Bérlő és a gazdakészlet létrehozása című témakört.](troubleshoot-set-up-issues.md)
- A Windows virtuális asztal virtuális gépének konfigurálása során felmerülő problémák elhárításához olvassa el a [Munkamenetgazda virtuálisgép-konfiguráció című témakört.](troubleshoot-vm-configuration.md)
- A PowerShell Windows virtuális asztallal való használatakor felmerülő problémák elhárításáról a [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)című témakörben található.
- Ha hibaelhárítási oktatóanyagon szeretne átmenni, olvassa el [az Oktatóanyag: Erőforrás-kezelő sablontelepítésekkel kapcsolatos hibaelhárítása című témakört.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)