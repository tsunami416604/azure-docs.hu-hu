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
ms.date: 09/04/2018
ms.author: juluk
ms.openlocfilehash: 67624e7ad21e6c3526b3ca47ff4b8e2df3b6ae54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962264"
---
# <a name="overview-of-azure-cloud-shell"></a>Az Azure Cloud Shell áttekintése
Az Azure Cloud Shell egy interaktív, a böngésző által elérhető shell Azure-erőforrások kezeléséhez.
Biztosít munkamódszer leginkább megfelelő rendszerhéj-felületet válassza.
A Linuxot használók választhatják a Basht, a Windows-használók pedig a PowerShellt.

Próbálja a shell.azure.com alábbi gombra kattintva.

[![Indítási beágyazási](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell indítása")](https://shell.azure.com)

Próbálja ki az Azure Portalról, a Cloud Shell ikon használatával.

![Portál indítása](media/overview/portal-launch-icon.png)

## <a name="features"></a>Szolgáltatások

### <a name="browser-based-shell-experience"></a>Böngészőalapú rendszerhéj-felület
A cloud Shell lehetővé teszi a hozzáférést egy beépített szem előtt az Azure felügyeleti feladatainak parancssori böngészőalapú felület.
Használja ki a Cloud Shell működik a helyi gépről untethered oly módon csak a felhőben is biztosítanak.

### <a name="choice-of-preferred-shell-experience"></a>Kedvenc rendszerhéj-felületét kiválasztása
Linux-felhasználók használhatja a Cloud Shellben, a Bash közben Windows felhasználók használhatják a PowerShell Cloud shellben (előzetes verzió) a rendszerhéj legördülő listából.

![A Cloud Shell bash](media/overview/overview-bash-pic.png)

![A PowerShell Cloud shellben (előzetes verzió)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure munkaállomás
A cloud Shell Microsoft által kezelt, népszerű parancssori eszközökkel és a nyelvi támogatás származik. A cloud Shell is biztonságosan hitelesíti automatikusan azonnali hozzáférés az erőforrásokhoz az Azure CLI-vel vagy az Azure PowerShell parancsmagokon keresztül.

Megtekintheti az összes [a Cloud Shellben telepített eszközök listáját.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrált Cloud Shell-szerkesztő
A cloud Shell biztosít egy integrált grafikus szövegszerkesztőben a nyílt forráskódú Monaco szerkesztő alapján. Egyszerűen hozzon létre, és szerkessze a konfigurációs fájlokat futtatásával `code .` a zökkenőmentes üzembe helyezése az Azure CLI 2.0-t vagy az Azure Powershellen keresztül.

[További információ a Cloud Shell-szerkesztő](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Több kapcsolódási pont
A cloud Shell rugalmas eszköz használható a következő:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Az Azure parancssori felület "Próbálja ki" dokumentációja](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Azure-mobilalkalmazás](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [A VS Code Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>A Microsoft Azure Files storage csatlakoztatása
Cloud Shell gépek ideiglenes, és egy új vagy meglévő Azure-fájlmegosztási fürtkötetként való igényel `clouddrive` fájlmegtartás érdekében.

A Cloud Shell utasításait követve hozzon létre egy erőforrást első indításkor csoport, a storage-fiók és az Azure Files ossza meg az Ön nevében. Ez egy egyszeri lépés és lesz automatikusan hozzárendelve az összes munkamenet során. Egyetlen fájlmegosztást is le lehet képezni, és a Bash és a PowerShell használatával a Cloud Shell-(előzetes verzió) használják.

További információ a megtudhatja, hogyan csatlakoztathat egy [új vagy meglévő storage-fiók](persisting-shell-storage.md).

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
