---
title: Azure Cloud Shell áttekintése | Microsoft Docs
description: A Azure Cloud Shell áttekintése.
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
ms.openlocfilehash: 2edb50910614b47aca5a4d8f7f02c2e3bc69b643
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449002"
---
# <a name="overview-of-azure-cloud-shell"></a>A Azure Cloud Shell áttekintése
A Azure Cloud Shell egy interaktív, hitelesített, böngészővel elérhető rendszerhéj az Azure-erőforrások kezeléséhez.
Rugalmasságot nyújt a munkamódszeréhez leginkább illő héjfelület (akár a Bash, akár a PowerShell) kiválasztásának lehetőségével.

Az alábbi gombra kattintva próbálja ki a shell.azure.com.

[![Beágyazás elindítása](https://shell.azure.com/images/launchcloudshell.png "Az Azure Cloud Shell indítása")](https://shell.azure.com)

Próbálja ki a Azure Portalt a Cloud Shell ikon használatával.

![Portál elindítása](media/overview/portal-launch-icon.png)

## <a name="features"></a>Jellemzők

### <a name="browser-based-shell-experience"></a>Böngészőalapú rendszerhéj-felület
A Cloud Shell lehetővé teszi az Azure felügyeleti feladatokkal létrehozott, böngészőalapú parancssori felület elérését.
Kihasználhatja a Cloud Shellt, hogy a helyi gépekről csak a felhő által biztosított munkafolyamatok legyenek lekötve.

### <a name="choice-of-preferred-shell-experience"></a>Az előnyben részesített rendszerhéj-élmény kiválasztása
A felhasználók a rendszerhéj legördülő menüjéből választhatnak a bash vagy a PowerShell közül.

![Bash Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell a Cloud Shellben](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Hitelesített és konfigurált Azure munkaállomás
A Cloud Shell a Microsoft felügyeli, ezért népszerű parancssori eszközöket és nyelvi támogatást nyújt. Az Azure CLI-n vagy Azure PowerShell-parancsmagokon keresztül a Cloud Shell automatikusan biztonságosan hitelesíti az erőforrásokhoz való azonnali hozzáférést.

Tekintse meg a [Cloud Shell telepített eszközök teljes listáját.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrált Cloud Shell szerkesztő
Cloud Shell egy integrált grafikus szövegszerkesztőt kínál a nyílt forráskódú Monaco-szerkesztő alapján. Egyszerűen hozhat létre és szerkeszthet konfigurációs fájlokat az Azure CLI vagy Azure PowerShell használatával történő zökkenőmentes üzembe helyezéshez `code .` futtatásával.

[További információ a Cloud Shell Szerkesztőről](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrálva a docs.microsoft.com

A Cloud Shell közvetlenül a [docs.microsoft.com](https://docs.microsoft.com)-on tárolt dokumentációból is használhatja. Integrálva van [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) és az [Azure CLI dokumentációjában](https://docs.microsoft.com/cli/azure) – kattintson a kódrészletben található "Try" (kipróbálás) gombra, hogy megnyissa a zökkenőmentes rendszerhéj-élményt. 

### <a name="multiple-access-points"></a>Több hozzáférési pont
A Cloud Shell egy rugalmas eszköz, amely a következő helyekről használható:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Az Azure CLI dokumentációja](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell dokumentáció](https://docs.microsoft.com/powershell/azure/overview)
* [Azure mobile app](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code Azure-fiók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Microsoft Azure fájlok tárolójának összekötése
Cloud Shell gépek ideiglenesek, de a fájlok két módon maradnak meg: lemezképen keresztül, valamint egy `clouddrive`nevű csatlakoztatott fájlmegosztás segítségével.  Az első indítás alkalmával a Cloud Shell felkéri egy erőforráscsoport, tárfiók és Azure Files-megosztás létrehozására. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez. Egyetlen fájlmegosztás képezhető le, amelyet a bash és a PowerShell is használ Cloud Shellban.

További információ: [új vagy meglévő Storage-fiók](persisting-shell-storage.md) csatlakoztatása, illetve a [Cloud Shell használt adatmegőrzési mechanizmusok](persisting-shell-storage.md#how-cloud-shell-storage-works)megismerése.

> [!NOTE]
> A Cloud Shell Storage-fiókok nem támogatják az Azure Storage-tűzfalat.

## <a name="concepts"></a>Alapelvek
* A Cloud Shell munkamenetenkénti, felhasználónkénti alapon megadott ideiglenes gazdagépen fut.
* Cloud Shell idő után 20 perccel interaktív tevékenység nélkül
* Cloud Shell egy Azure-fájlmegosztás csatlakoztatását igényli
* Cloud Shell ugyanazt az Azure-fájlmegosztást használja, mint a bash és a PowerShell
* Cloud Shell egy gépet rendelnek hozzá felhasználónkénti fiókkal
* Cloud Shell megőrzi $HOME a fájlmegosztás 5 GB-os rendszerképének használatával
* Az engedélyek normál Linux-felhasználóként vannak beállítva a bash-ben

További információ a bash szolgáltatásairól [Cloud Shell](features.md) és [a PowerShellben Cloud Shellban](features-powershell.md).

## <a name="pricing"></a>Díjszabás
A Cloud Shell gép üzemeltetése ingyenes, és egy csatlakoztatott Azure Files-megosztás előfeltétele. A normál tárolási költségek érvényesek.

## <a name="next-steps"></a>Következő lépések
[Bash Cloud Shell rövid útmutatóban](quickstart.md) <br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md)
