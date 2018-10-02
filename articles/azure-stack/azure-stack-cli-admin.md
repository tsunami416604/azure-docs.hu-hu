---
title: Azure CLI-vel engedélyezése az Azure Stack felhasználói |} A Microsoft Docs
description: Az Azure Stacken erőforrásokat üzembe helyezheti és kezelheti a többplatformos parancssori felület (CLI) használata
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: e9309f8cb46b31ded46b705308465ac6f6c89204
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585186"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure CLI-vel engedélyezése az Azure Stack-felhasználók számára

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A legfelső szintű hitelesítésszolgáltató tanúsítványát biztosíthat az Azure Stack felhasználói számára, így az Azure CLI-vel a fejlesztői gépen is használni. A felhasználóknak kell a tanúsítványt a CLI-n keresztül-erőforrások kezeléséhez.

* **Az Azure Stack hitelesítésszolgáltató főtanúsítványát** van szükség, ha a felhasználók parancssori felület használata az Azure Stack Development Kit kívül munkaállomásokon.  

* **A virtuális gép aliasok végpontjának** alias, mint a "UbuntuLTS" vagy "Win2012Datacenter," által hivatkozott egy rendszerkép közzétevője, ajánlat, Termékváltozat és verzió egyetlen paramétert, ha a virtuális gépek üzembe helyezéséhez biztosít.  

A következő szakaszok ismertetik ezeket az értékeket beszerzése.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Az Azure Stack hitelesítésszolgáltató legfelső szintű tanúsítványának exportálása

Megtalálhatja az Azure Stack hitelesítésszolgáltató főtanúsítványát a csomag és a egy bérlői virtuális gép, amelyen fut a development kit környezeten belül. Szeretné exportálni az Azure Stack főtanúsítványának PEM formátumú, jelentkezzen be a fejlesztői készlet vagy a bérlő virtuális gépet, és futtassa a következő szkriptet:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>A virtuális gép aliasok végpontjának beállítása

Az Azure Stack-operátorok egy nyilvánosan elérhető végponton, amelyen egy virtuális gép alias fájlt kell beállítania. A virtuális gép alias fájl nem egy JSON-fájl által biztosított rendszerképek köznapi neve. Azure CLI-vel paraméterként egy virtuális gép üzembe helyezésekor ezt követően megadva a neve.  

Előtt egy bejegyzést ad hozzá egy alias fájl, győződjön meg arról, hogy Ön [töltse le a rendszerképeket az Azure Marketplace-ről](azure-stack-download-azure-marketplace-item.md), vagy rendelkezik [közzé saját egyéni rendszerképét](azure-stack-add-vm-image.md). Ha egy egyéni rendszerkép közzététele, jegyezze fel a közzétevő, ajánlat, Termékváltozat és verzió közzététele során megadott információk. Ha egy lemezképet a marketplace webhelyről, az információk használatával megtekintheti a ```Get-AzureVMImage``` parancsmagot.  

A [alias mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) számos gyakori rendszerképpel aliasok érhető el. Kiindulási pontként használhatja azt. A gazdagép egy helyet, ahol a CLI-ügyfelek számára is elérhetővé válik a fájlhoz. Egyik módja a fájlt a blob storage-fiókot üzemeltethet, és az URL-cím megosztása a felhasználókkal:

1. Töltse le a [mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a Githubról.
2. Új tárfiók létrehozása az Azure Stackben. Ha ez megtörtént, hozzon létre egy új blobtárolót. A hozzáférési szabályzat a "nyilvános".  
3. Töltse fel az új tároló a JSON-fájlt. Ha ez megtörtént, megtekintheti a blob URL-CÍMÉT, jelölje ki a blob nevét, és ezután jelölje ki az URL-címet a blob tulajdonságai.

## <a name="next-steps"></a>További lépések

- [Az Azure CLI-vel sablonok üzembe helyezése](azure-stack-deploy-template-command-line.md)
- [Kapcsolódás a PowerShell-lel](azure-stack-connect-powershell.md)
- [Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)
