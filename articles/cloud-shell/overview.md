---
title: "Azure Cloud rendszerhéj áttekintése |} Microsoft Docs"
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
ms.date: 12/13/2017
ms.author: juluk
ms.openlocfilehash: 129b43db85a0962005352e0f1e6ad2ad3be2c7d5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Azure-felhőbe rendszerhéj áttekintése
Azure Cloud rendszerhéjjal egy interaktív, a böngésző által elérhető rendszerhéj Azure-erőforrások kezeléséhez.
Azt a rugalmasságot biztosít a rendszerhéj élmény igényei leginkább megfelelő kiválasztása.
A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

Próbálja a shell.azure.com ez a gomb használatával.

[![](https://shell.azure.com/images/launchcloudshell.png "Indítsa el az Azure-felhőbe rendszerhéj")](https://shell.azure.com)

Próbálja meg a felhő rendszerhéj ikonnal Azure portálról.

![Portál indítása](media/overview/portal-launch-icon.png)

## <a name="features"></a>Szolgáltatások
### <a name="browser-based-shell-experience"></a>Böngészőalapú felület élmény
Felhő rendszerhéj lehetővé teszi a hozzáférést egy Azure felügyeleti feladatok szem előtt a beépített parancssori böngészőalapú felületén.
Használja ki az felhő rendszerhéj módon csak a felhőben a helyi gépről untethered működéséhez biztosíthat.

### <a name="choice-of-preferred-shell-experience"></a>Előnyben részesített rendszerhéj élmény kiválasztása
Linux a felhasználók a Bash felhő rendszerhéj, amíg a Windows-felhasználók a PowerShell felhő rendszerhéj (előzetes verzió) segítségével a rendszerhéj legördülő listából.

![A felhő rendszerhéj bash](media/overview/overview-bash-pic.png)

![PowerShell felhő rendszerhéj (előzetes verzió)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure munkaállomás
Felhő rendszerhéj Microsoft által felügyelt, népszerű parancssori eszközök és a nyelvi támogatás származik. Felhő rendszerhéj is biztonságosan hitelesíti automatikusan azonnali erőforrások elérése érdekében az Azure CLI 2.0 vagy az Azure PowerShell parancsmagokon keresztül.

A teljes tooling listájának megtekintése a [élmény Bash](features.md#tools) és [PowerShell (előzetes verzió) élmény.](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Több kapcsolódási pont
Felhő rendszerhéjjal rugalmas eszköz, amely a használható:
* [Portal.Azure.com](https://portal.azure.com)
* [Shell.Azure.com](https://shell.azure.com)
* [Az Azure CLI 2.0 "Try It" dokumentációja](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Azure mobile Apps](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual STUDIO Code Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Csatlakozás a Microsoft Azure Files storage
Felhő rendszerhéj gépek ideiglenes, és az Azure-fájlok megosztás csatlakoztatható igényel `clouddrive` megőrizni a fájlokat.

Felhő rendszerhéj kérni fogja az erőforrás létrehozása első indítási csoport, a tárfiók és az Azure fájlok ossza meg az Ön nevében. Ez egy egyszeri lépés, és lesz automatikusan hozzárendelve minden munkamenetben. Egyetlen fájlmegosztást képezhetők és Bash és a felhő rendszerhéj (előzetes verzió) PowerShell is használható.

#### <a name="create-new-storage"></a>Új tároló létrehozása
![](media/overview/basic-storage.png)

Egy helyileg redundáns tárolás (LRS) fiókot és az Azure-fájlok létrehozhatók az Ön nevében. Azure-fájlok Bash és a PowerShell környezetben használandó, ha úgy dönt, hogy egyaránt. Rendszeres tárolási költségek vonatkoznak.

Három erőforrások hozza létre az Ön nevében:
1. Az erőforráscsoport neve:`cloud-shell-storage-<region>`
2. A Tárfiók neve:`cs<uniqueGuid>`
3. A fájlmegosztás neve:`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Felhő rendszerhéj bash is létrehoz egy alapértelmezett 5 GB-os lemezre lemezképet `$Home`. Például az SSH-kulcsok a $Home könyvtárban található összes fájl megmaradnak, a csatlakoztatott Azure fájlmegosztás tárolja a felhasználói lemezképet. Alkalmazza az ajánlott eljárásokat, ha a fájlok mentése a $Home címtár és a csatlakoztatott Azure-fájlmegosztáshoz.

#### <a name="use-existing-resources"></a>Létező erőforrásokból
![](media/overview/advanced-storage.png)

Speciális beállítás rendelje hozzá a meglévő erőforrásokat felhő rendszerhéj valósul meg.
A tárolási telepítő parancssorból, kattintson a "Show speciális beállítások" További beállítások megjelenítéséhez.

> [!Note]
> Legördülő lista az előzetesen hozzárendelt felhő rendszerhéj pedig a LRS/Georedundáns tárfiókok szűrve.

[További információk a felhő rendszerhéj tárolási, az Azure fájlmegosztások frissítése, és a feltöltése/letöltése fájlok.](persisting-shell-storage.md)

## <a name="concepts"></a>Alapelvek
* Felhő rendszerhéj futtat egy ideiglenes egy munkamenetenként, felhasználónkénti alapon megadott gazdagépen
* Interaktív tevékenység nélkül 20 perc elteltével időtúllépést felhő rendszerhéj
* Felhő rendszerhéj szükséges csatlakoztatni kell az Azure fájlmegosztások
* Felhő rendszerhéj azonos Azure fájlmegosztás Bash és a PowerShell használja.
* Felhő rendszerhéj hozzá van rendelve egy gép felhasználónként
* Bash továbbra is fennáll, a fájlmegosztáson tárolt 5 GB-os lemezképpel $Home
* A Bash Linux felhasználói engedélyek beállítása

További információk a szolgáltatások [felhő rendszerhéj Bash](features.md) és [PowerShell felhő rendszerhéj (előzetes verzió)](features-powershell.md).

## <a name="pricing"></a>Díjszabás
A felhő rendszerhéj futtató gépen szabad, az Azure Fileshoz csatlakoztatott megosztás előfeltételeként. Rendszeres tárolási költségek vonatkoznak.

## <a name="next-steps"></a>Következő lépések
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md) <br>
[PowerShell használatával a felhő rendszerhéj (előzetes verzió) gyors üzembe helyezés](quickstart-powershell.md)