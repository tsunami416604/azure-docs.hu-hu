---
title: "Azure Cloud rendszerhéj (előzetes verzió) – áttekintés |} Microsoft Docs"
description: "Az Azure-felhőbe rendszerhéj áttekintése."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 7f5c034df3410fc5563a5ed657f7c1aa0f4135ec
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Azure-felhőbe rendszerhéj (előzetes verzió) áttekintése
Azure Cloud rendszerhéjjal egy interaktív, a böngésző által elérhető rendszerhéj Azure-erőforrások kezeléséhez.
Azt a rugalmasságot biztosít a rendszerhéj élmény igényei leginkább megfelelő kiválasztása.
A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

![A felhő rendszerhéj bash](media/overview/overview-bash-pic.png)

![A felhő rendszerhéj PowerShell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Szolgáltatások
### <a name="browser-based-shell-experience"></a>Böngészőalapú felület élmény
Felhő rendszerhéj lehetővé teszi a hozzáférést egy Azure felügyeleti feladatok szem előtt a beépített parancssori böngészőalapú felületén.
Használja ki az felhő rendszerhéj módon csak a felhőben a helyi gépről untethered működéséhez biztosíthat.

### <a name="choice-of-preferred-shell-experience"></a>Előnyben részesített rendszerhéj élmény kiválasztása
Az Azure Cloud Shell lehetővé teszi, hogy a munkamódszerének leginkább megfelelő rendszerhéj-felületet válassza.
A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

### <a name="pre-configured-azure-workstation"></a>Előre konfigurált Azure munkaállomás
Felhő rendszerhéj előre előre telepített népszerű parancssori eszközökkel, és a nyelv támogatja, így gyorsabban működnek.

A teljes tooling listájának megtekintése [élmény Bash](features.md#tools) és [PowerShell élmény.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Automatikus hitelesítéshez
Felhő rendszerhéj biztonságosan hitelesíti automatikusan minden munkamenet azonnali erőforrások elérése érdekében a PowerShell-parancsmagok és az Azure CLI 2.0 keresztül.

### <a name="connect-your-azure-file-storage"></a>Csatlakozás az Azure File storage
Felhő rendszerhéj gépek ideiglenes, és emiatt igényel csatlakoztatni az Azure fájlmegosztások `clouddrive` megőrizni a $Home címtárban.
Felhő rendszerhéj kérni fogja az erőforrás létrehozása első indítsa el a csoport, a tárfiók, és fájlmegosztás, az Ön nevében. Ez egy egyszeri lépés, és lesz automatikusan hozzárendelve minden munkamenetben. Egyetlen fájlmegosztást képezhetők és Bash és a felhő rendszerhéj PowerShell is használható.

#### <a name="create-new-storage"></a>Új tároló létrehozása
![](media/overview/basic-storage.png)

A helyileg redundáns tárolás (LRS) fiók és az Azure fájlmegosztás hozhatók létre az Ön nevében. Az Azure fájlmegosztás Bash és a PowerShell környezetben használandó, ha úgy dönt, hogy egyaránt. Rendszeres tárolási költségek vonatkoznak.

Három erőforrások hozza létre az Ön nevében:
1. Az erőforráscsoport neve:`cloud-shell-storage-<region>`
2. A Tárfiók neve:`cs<uniqueGuid>`
3. A fájlmegosztás neve:`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Felhő rendszerhéj bash is létrehoz egy alapértelmezett 5 GB-os lemezre lemezképet `$Home`. Például az SSH-kulcsok a $Home könyvtárban található összes fájl megmaradnak, az a felhasználó lemezképet, a csatlakoztatott fájl megosztáson található. Alkalmazza az ajánlott eljárásokat, ha a fájlok mentése a $Home címtár és a csatlakoztatott fájlmegosztás.

#### <a name="use-existing-resources"></a>Létező erőforrásokból
![](media/overview/advanced-storage.png)

Speciális beállítás rendelje hozzá a meglévő erőforrásokat felhő rendszerhéj valósul meg.
A tárolási telepítő parancssorból, kattintson a "Show speciális beállítások" További beállítások megjelenítéséhez.
A hozzárendelt felhő rendszerhéj pedig a helyi/globálisan-redundancia tárfiókok legördülő listák Megnyílásának szűrve.

[További információk a felhő rendszerhéj tároló fájlmegosztások frissítése, és a feltöltése/letöltése fájlok.](persisting-shell-storage.md)

## <a name="concepts"></a>Alapelvek
* Felhő rendszerhéj egy munkamenetenként, egyes felhasználók számára a megadott ideiglenes gépen fut
* Interaktív tevékenység nélkül 20 perc elteltével időtúllépést felhő rendszerhéj
* Felhő rendszerhéj csak és csatolt fájlmegosztás érhető el
* Rendszerhéj használja a felhő egy ugyanazt a fájlmegosztást a Bash és a PowerShell
* Felhő rendszerhéj hozzá van rendelve egy gép felhasználónként
* Engedélyek beállítása Linux felhasználói (Bash)

További információk a szolgáltatások [felhő rendszerhéj Bash](features.md) és [felhő rendszerhéj PowerShell](features-powershell.md).

## <a name="examples"></a>Példák
* Az Azure felügyeleti feladatok automatizálására parancsfájlok használata
* Egyidejűleg kezelése az Azure-erőforrások Azure-portál és az Azure parancssori eszközök segítségével
* Azure CLI 2.0 vagy az Azure PowerShell-parancsmagok test-Drive

Próbálja ki ezeket a quickstarts szereplő példák [felhő rendszerhéj Bash](quickstart.md) és [felhő rendszerhéj PowerShell](quickstart-powershell.md).

## <a name="pricing"></a>Díjszabás
Felhő rendszerhéj futtató gépen egy csatlakoztatott Azure fájlmegosztás előfeltételként az ingyenes. Rendszeres tárolási költségek vonatkoznak.

## <a name="supported-browsers"></a>Támogatott böngészők
Felhő rendszerhéj Chrome, biztonsági és Safari ajánlott.
Felhő rendszerhéj Chrome, Firefox, Safari, Internet Explorer vagy Edge esetén támogatott, amíg felhő rendszerhéj az adott böngésző beállításait.
