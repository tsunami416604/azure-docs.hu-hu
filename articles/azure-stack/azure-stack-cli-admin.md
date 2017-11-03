---
title: "Az Azure parancssori felület engedélyezése a felhasználók Azure verem |} Microsoft Docs"
description: "A platformfüggetlen parancssori felület (CLI) használatával kezelheti és telepítheti az Azure-veremben erőforrások használata"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d854c106fbce7e3f01c2878bb9828bdffa4d42a5
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Azure CLI Azure verem felhasználók engedélyezése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Nincs bármely Azure verem operátor-specifikus feladatokat hajthat végre az Azure parancssori felület használatával. De a felhasználók kezelhetik az erőforrásokat parancssori felületen keresztül, mielőtt Azure verem operátorok kell biztosítani nekik a következő:

* **Az Azure-verem hitelesítésszolgáltató főtanúsítványát** szükség, ha a felhasználó CLI használja az Azure verem szoftverfejlesztői készlet kívül munkaállomásról.  

* **A virtuális gép aliasok végpont** biztosít alias, például a "UbuntuLTS" vagy "Win2012Datacenter," hivatkozó egy kép közzétevőjének, ajánlat, SKU és verzió egyetlen paraméterként virtuális gépek telepítése során.  

Az alábbi szakaszok ismertetik ezeket az értékeket beolvasása.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Az Azure-verem hitelesítésszolgáltató legfelső szintű tanúsítványának exportálása

Az Azure-verem hitelesítésszolgáltató főtanúsítványát a csomag és a development kit környezeten belül futó bérlői virtuális gépen érhető el. A PEM-formátumba Azure verem legfelső szintű tanúsítványának exportálásához jelentkezzen be a szoftverfejlesztői készlet vagy a bérlői virtuális gépet, és futtassa a következő parancsfájlt:

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

Az Azure verem operátorok egy nyilvánosan elérhető végponton, amelyen a virtuálisgép-alias fájlt kell beállítani. A virtuális gép alias fájl nem egy JSON-fájlt, amely rendszerképek köznapi neve. Ilyen nevű ezt követően van megadva, a virtuális gép telepítésekor az Azure parancssori felület paraméterként.  

Mielőtt alias fájlt adjon hozzá egy bejegyzést, győződjön meg arról, hogy akkor [töltse le a lemezképet az Azure piactérről](azure-stack-download-azure-marketplace-item.md), vagy [közzé a saját egyéni rendszerképét](azure-stack-add-vm-image.md). Ha egyéni lemezkép tesz közzé, jegyezze fel a közzétevő, az ajánlat, SKU és verzió adatok közzététele során megadott. Ha a piactérről származó lemezkép, az információk használatával megtekintheti a ```Get-AzureVMImage``` parancsmag.  
   
A [alias mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) számos gyakori lemezképpel aliasok érhető el. Amely használhatja kiindulási pontként. A gazdagép egy helyet, ahol a CLI ügyfelek csatlakozni tud hozzá a fájlhoz. Egyik módja a blob storage-fiók a fájlt, majd az URL-cím megosztása a felhasználókkal:

1. Töltse le a [mintafájl](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a Githubról.
2. Új tárfiók létrehozása Azure-készletben. Amely befejeződése után hozzon létre egy új blob-tároló. Állítsa be a hozzáférési házirendet "nyilvános".  
3. A JSON-fájl feltöltése az új tárolóhoz. Amely befejeződése után megtekintheti a blob URL-CÍMÉT blob nevére kattint, és jelölje be az URL-címet a blob tulajdonságai közül.


## <a name="next-steps"></a>Következő lépések

[Sablonok az Azure parancssori felület telepítése](azure-stack-deploy-template-command-line.md)

[Kapcsolódás a PowerShell-lel](azure-stack-connect-powershell.md)

[Felhasználói engedélyek kezelése](azure-stack-manage-permissions.md)

