---
title: Az Azure Cloud Shell áttekintése | Microsoft dokumentumok
description: Az Azure Cloud Shell áttekintése.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832479"
---
# <a name="overview-of-azure-cloud-shell"></a>Az Azure Cloud Shell áttekintése
Az Azure Cloud Shell interaktív, hitelesített, böngészőben elérhető felület az Azure-erőforrások felügyeletéhez.
Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével.

Próbálja ki shell.azure.com az alábbi gombra kattintva.

[![Indítás beágyazása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Próbálja ki az Azure Portalról a Cloud Shell ikonhasználatával.

![A portál elindítása](media/overview/portal-launch-icon.png)

## <a name="features"></a>Szolgáltatások

### <a name="browser-based-shell-experience"></a>Böngészőalapú rendszerhéj-élmény
A Cloud Shell lehetővé teszi a hozzáférést az Azure felügyeleti feladatainak szem előtt tartásával készített böngészőalapú parancssori élményhez.
Használja ki a Cloud Shell-t, hogy egy helyi gépről lekötötten dolgozzon oly módon, amelyet csak a felhő nyújthat.

### <a name="choice-of-preferred-shell-experience"></a>Az előnyben részesített rendszerhéj-élmény megválasztása
A felhasználók választhatnak a Bash vagy a PowerShell között.
1. Válassza a **Cloud Shell**lehetőséget.

    ![A Cloud Shell ikonja](media/overview/overview-cloudshell-icon.png)

2. Válassza a **Bash** vagy a **PowerShell lehetőséget.**

    ![Válassza a Bash vagy a PowerShell lehetőséget](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure-munkaállomás
A Cloud Shellt a Microsoft kezeli, így népszerű parancssori eszközökkel és nyelvi támogatással érkezik. A Cloud Shell is biztonságosan hitelesíti automatikusan az erőforrásokhoz való azonnali hozzáférést az Azure CLI vagy az Azure PowerShell-parancsmagok on keresztül.

Tekintse meg a [Cloud Shellben telepített eszközök](features.md#tools) teljes listáját.

### <a name="integrated-cloud-shell-editor"></a>Integrált Cloud Shell-szerkesztő
A Cloud Shell egy integrált grafikus szövegszerkesztőt kínál a nyílt forráskódú Monaco Editor alapján. Egyszerűen hozzon létre és `code .` szerkesztse a konfigurációs fájlokat az Azure CLI-n vagy az Azure PowerShellen keresztüli zökkenőmentes üzembe helyezés érdekében.

[További információ a Cloud Shell szerkesztőről.](using-cloud-shell-editor.md)

### <a name="integrated-with-docsmicrosoftcom"></a>Integrálva a docs.microsoft.com

A Cloud Shell t közvetlenül [a docs.microsoft.com](https://docs.microsoft.com)tárolt dokumentációból használhatja. Integrálva van a [Microsoft Learn](https://docs.microsoft.com/learn/), Az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) és az [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure) – kattintson a "Try It" gombra egy kódrészletben a magával ragadó rendszerhéj-élmény megnyitásához. 

### <a name="multiple-access-points"></a>Több hozzáférési pont
A Cloud Shell egy rugalmas eszköz, amely a következőkből használható:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Az Azure CLI dokumentációja](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell-dokumentáció](https://docs.microsoft.com/powershell/azure/overview)
* [Azure Mobile Apps](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure-fiókbővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>A Microsoft Azure Files tárhely csatlakoztatása
A Cloud Shell-gépek ideiglenesek, de a fájlok két módon maradnak meg: lemezképen és csatlakoztatott fájlmegosztáson keresztül `clouddrive`.  Az első indítás alkalmával a Cloud Shell felkéri egy erőforráscsoport, tárfiók és Azure Files-megosztás létrehozására. Ez egy egyszeri lépés, és automatikusan csatolni kell az összes munkamenethez. Egyetlen fájlmegosztás leképezhető, és a Bash és a PowerShell a Cloud Shellben is használható lesz.

További információ: új [vagy meglévő tárfiók](persisting-shell-storage.md) csatlakoztatása, illetve a [Cloud Shellben használt perzisztenciamechanizmusok](persisting-shell-storage.md#how-cloud-shell-storage-works)megismerése.

> [!NOTE]
> Az Azure storage tűzfal nem támogatott a felhőbeli rendszerhéj-tárfiókok.

## <a name="concepts"></a>Alapelvek
* A Cloud Shell egy munkamenetenként, felhasználónkénti alapon biztosított ideiglenes gazdagépen fut
* A Cloud Shell 20 perc után kiidőzik az interaktív tevékenység nélkül
* A Cloud Shell használatához Azure-fájlmegosztásra van szükség
* A Cloud Shell ugyanazt az Azure-fájlmegosztást használja bash és PowerShell esetén is
* A Cloud Shell felhasználói fiókonként egy géphez van rendelve
* A Cloud Shell $HOME a fájlmegosztásban található 5 GB-os kép használatával
* Az engedélyek normál Linux-felhasználóként vannak beállítva a Bash-ben

További információ a Bash a [Cloud Shellben](features.md) és a [PowerShell a Cloud Shellben](features-powershell.md)funkcióiról.

## <a name="pricing"></a>Díjszabás
A Cloud Shellt üzemeltető gép ingyenes, egy csatlakoztatott Azure Files megosztás előfeltételével. Rendszeres tárolási költségek érvényesek.

## <a name="next-steps"></a>További lépések
[Bash a Cloud Shell rövid útmutató](quickstart.md) <br>
[PowerShell a Cloud Shell rövid útmutatójában](quickstart-powershell.md)
