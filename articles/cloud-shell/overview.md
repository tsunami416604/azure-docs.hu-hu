---
title: Az Azure Cloud Shell áttekintése |} A Microsoft Docs
description: Az Azure Cloud Shell áttekintése.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: 9588bebdc827760f0e0d3e2aadccbff5f24723f1
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258925"
---
# <a name="overview-of-azure-cloud-shell"></a>Az Azure Cloud Shell áttekintése
Az Azure Cloud Shell egy interaktív, a böngésző által elérhető shell Azure-erőforrások kezeléséhez.
Biztosít munkamódszer leginkább megfelelő rendszerhéj-felületet válassza.
A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

Próbálja a shell.azure.com alábbi gombra kattintva.

[![](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Próbálja ki az Azure Portalról, a Cloud Shell ikon használatával.

![Portál indítása](media/overview/portal-launch-icon.png)

## <a name="features"></a>Áttekintés
### <a name="browser-based-shell-experience"></a>Böngészőalapú rendszerhéj-felület
A cloud Shell lehetővé teszi a hozzáférést egy beépített szem előtt az Azure felügyeleti feladatainak parancssori böngészőalapú felület.
Használja ki a Cloud Shell működik a helyi gépről untethered oly módon csak a felhőben is biztosítanak.

### <a name="choice-of-preferred-shell-experience"></a>Kedvenc rendszerhéj-felületét kiválasztása
Linux-felhasználók használhatja a Cloud Shellben, a Bash közben Windows felhasználók használhatják a PowerShell Cloud shellben (előzetes verzió) a rendszerhéj legördülő listából.

![A Cloud Shell bash](media/overview/overview-bash-pic.png)

![A PowerShell Cloud shellben (előzetes verzió)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure munkaállomás
A cloud Shell Microsoft által kezelt, népszerű parancssori eszközökkel és a nyelvi támogatás származik. A cloud Shell is biztonságosan hitelesíti automatikusan azonnali hozzáférés az erőforrásokhoz az Azure CLI 2.0 vagy az Azure PowerShell parancsmagokon keresztül.

Megtekintheti az összes [a Cloud Shellben telepített eszközök listáját.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrált Cloud Shell-szerkesztő
A cloud Shell biztosít egy integrált grafikus szövegszerkesztőben a nyílt forráskódú Monaco szerkesztő alapján. Egyszerűen hozzon létre, és szerkessze a konfigurációs fájlokat futtatásával `code .` a zökkenőmentes üzembe helyezése az Azure CLI 2.0-t vagy az Azure Powershellen keresztül.

[További információ a Cloud Shell-szerkesztő](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Több kapcsolódási pont
A cloud Shell rugalmas eszköz használható a következő:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Az Azure CLI 2.0-val "Próbálja ki" dokumentációja](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Azure-mobilalkalmazás](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [A VS Code Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>A Microsoft Azure Files storage csatlakoztatása
Cloud Shell gépek ideiglenes, és egy Azure-fájlmegosztási fürtkötetként való igényel `clouddrive` fájlmegtartás érdekében.

A Cloud Shell utasításait követve hozzon létre egy erőforrást első indításkor csoport, a storage-fiók és az Azure Files ossza meg az Ön nevében. Ez egy egyszeri lépés és lesz automatikusan hozzárendelve az összes munkamenet során. Egyetlen fájlmegosztást is le lehet képezni, és a Bash és a PowerShell használatával a Cloud Shell-(előzetes verzió) használják.

#### <a name="create-new-storage"></a>Új tároló létrehozása
![](media/overview/basic-storage.png)

Egy helyileg redundáns tárolás (LRS) fiókot és az Azure-fájlmegosztást az Ön nevében hozható létre. Az Azure-fájlmegosztást, ha úgy dönt, hogy egyaránt használható Bash és a PowerShell környezetben. Rendszeres tárolási költségek a alkalmazni.

Az Ön nevében három erőforrások jönnek létre:
1. Az erőforráscsoport neve: `cloud-shell-storage-<region>`
2. A Tárfiók neve: `cs<uniqueGuid>`
3. A fájlmegosztás neve: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> A Cloud Shell bash is létrehoz egy alapértelmezett 5 GB méretű lemezképet `$Home`. Az SSH-kulcsok például a $Home könyvtár található összes fájl megmaradnak, az a felhasználó a csatlakoztatott Azure-fájlmegosztásban tárolt lemezképet. Alkalmazza az ajánlott eljárásokat, ha a fájlok mentése a $Home könyvtár konzisztenciája és a csatlakoztatott Azure-fájlmegosztást.

#### <a name="use-existing-resources"></a>Meglévő erőforrások használata
![](media/overview/advanced-storage.png)

Egy speciális beállítás a Cloud Shell a meglévő erőforrások társítása biztosítunk.
A tároló a telepítő parancssorába kattintson a "Speciális beállítások megjelenítése" További beállítások megjelenítéséhez.

> [!Note]
> Legördülő menük az előzetesen hozzárendelt Cloud Shell-régióra és LRS vagy GRS/ZRS tárfiókok vannak szűrve.

[Ismerje meg a Cloud Shell-storage, Azure-fájlmegosztások frissítése és feltöltése/letöltése fájlok.](persisting-shell-storage.md)

## <a name="concepts"></a>Alapelvek
* A cloud Shell egy ideiglenes a munkamenetenkénti, felhasználónként megadott gazdagépen fut.
* Nem interaktív tevékenység 20 perc után a cloud Shell túllépi az időkorlátot
* A cloud Shell Azure-fájlmegosztások csatlakoztatnia kell van szükség.
* A cloud Shell Bash és a PowerShell az azonos Azure-fájlmegosztást használja
* A cloud Shell felhasználói fiókonként egy machine van hozzárendelve.
* A cloud Shell továbbra is fennáll, a fájlmegosztáson tárolt 5 GB-os-lemezkép használatával $Home
* A Bash Linux felhasználói engedélyek beállítása

További tudnivalók a szolgáltatások a [a Cloud Shell Bash](features.md) és [PowerShell Cloud shellben (előzetes verzió)](features-powershell.md).

## <a name="pricing"></a>Díjszabás
A Cloud Shell üzemeltető gép olyan előfeltételt, egy csatlakoztatott Azure-fájlmegosztással együtt ingyenes. Rendszeres tárolási költségek a alkalmazni.

## <a name="next-steps"></a>További lépések
[A rövid útmutatóban a Cloud Shell bash](quickstart.md) <br>
[A PowerShell Cloud Shell-(előzetes verzió) a rövid útmutatóban](quickstart-powershell.md)