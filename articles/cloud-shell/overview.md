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
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243975"
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
Felhasználók választhatnak, hogy a Bash vagy a PowerShell a rendszerhéj legördülő listából.

![A Cloud Shell bash](media/overview/overview-bash-pic.png)

![PowerShell a Cloud Shellben](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure munkaállomás
A cloud Shell Microsoft által kezelt, népszerű parancssori eszközökkel és a nyelvi támogatás származik. A cloud Shell is biztonságosan hitelesíti automatikusan azonnali hozzáférés az erőforrásokhoz az Azure CLI-vel vagy az Azure PowerShell parancsmagokon keresztül.

Megtekintheti az összes [a Cloud Shellben telepített eszközök listáját.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrált Cloud Shell-szerkesztő
A cloud Shell biztosít egy integrált grafikus szövegszerkesztőben a nyílt forráskódú Monaco szerkesztő alapján. Egyszerűen hozzon létre, és szerkessze a konfigurációs fájlokat futtatásával `code .` a zökkenőmentes üzembe helyezése az Azure CLI-vel vagy az Azure Powershellen keresztül.

[További információ a Cloud Shell-szerkesztő](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrálva a docs.microsoft.com webhelyen

Használhatja a Cloud Shellben lévő üzemeltetett dokumentációja közvetlenül a [docs.microsoft.com](https://docs.microsoft.com). Integrálva van a [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview) és [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure) – kattintson a "Kipróbálom" gombra a kódrészletben az ragadó rendszerhéj megnyitásához felhasználói élményt. 

### <a name="multiple-access-points"></a>Több kapcsolódási pont
A cloud Shell rugalmas eszköz használható a következő:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Az Azure CLI dokumentációja](https://docs.microsoft.com/cli/azure)
* [Az Azure PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-mobilalkalmazás](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code az Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>A Microsoft Azure Files storage csatlakoztatása
Cloud Shell gépek ideiglenes, és egy új vagy meglévő Azure-fájlmegosztási fürtkötetként való igényel `clouddrive` fájlmegtartás érdekében.

A Cloud Shell utasításait követve hozzon létre egy erőforrást első indításkor csoport, a storage-fiók és az Azure Files ossza meg az Ön nevében. Ez egy egyszeri lépés és lesz automatikusan hozzárendelve az összes munkamenet során. Egyetlen fájlmegosztást is le lehet képezni, és a Bash és a Cloud Shellbeli PowerShell is használható.

További információ a megtudhatja, hogyan csatlakoztathat egy [új vagy meglévő storage-fiók](persisting-shell-storage.md).

## <a name="concepts"></a>Alapelvek
* A cloud Shell egy ideiglenes a munkamenetenkénti, felhasználónként megadott gazdagépen fut.
* Nem interaktív tevékenység 20 perc után a cloud Shell túllépi az időkorlátot
* A cloud Shell Azure-fájlmegosztások csatlakoztatnia kell van szükség.
* A cloud Shell Bash és a PowerShell az azonos Azure-fájlmegosztást használja
* A cloud Shell felhasználói fiókonként egy machine van hozzárendelve.
* A cloud Shell továbbra is fennáll, a fájlmegosztáson tárolt 5 GB-os-lemezkép használatával $HOME
* A Bash Linux felhasználói engedélyek beállítása

További tudnivalók a szolgáltatások a [a Cloud Shell Bash](features.md) és [a Cloud Shellbeli PowerShell](features-powershell.md).

## <a name="pricing"></a>Díjszabás
A Cloud Shell üzemeltető gép olyan előfeltételt, egy csatlakoztatott Azure-fájlmegosztással együtt ingyenes. Rendszeres tárolási költségek a alkalmazni.

## <a name="next-steps"></a>További lépések
[A rövid útmutatóban a Cloud Shell bash](quickstart.md) <br>
[A PowerShell Cloud Shell a rövid útmutatóban](quickstart-powershell.md)
