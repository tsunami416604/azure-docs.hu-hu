---
title: Windows rendszerindítási kezelő – hiba – a 0xC0000428 állapota érvénytelen képkivonat
titlesuffix: Azure Virtual Machines
description: Ez a cikk azokat a problémákat ismerteti, amelyekkel megoldhatók az előzetes verziójú rendszerképek, valamint a próbaidőszak lejárt, ami megakadályozza egy Azure-beli virtuális gép (VM) indítását.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 26c411f9-6c22-4f45-a445-c5781e547828
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: v-miegge
ms.openlocfilehash: f607ebb64b27c45ec696d7fcd431a0ba2342697f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447809"
---
# <a name="windows-boot-manager-error---0xc0000428-status-invalid-image-hash"></a>Windows rendszerindítási kezelő – hiba – a 0xC0000428 állapota érvénytelen képkivonat

Ez a cikk azokat a problémákat ismerteti, amelyekkel megoldhatók az előzetes verziójú rendszerképek, valamint a próbaidőszak lejárt, ami megakadályozza egy Azure-beli virtuális gép (VM) indítását.

## <a name="symptom"></a>Hibajelenség

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőkép a Windows rendszerindítási kezelőt jeleníti meg a következő üzenettel:

  `File: \windows\system32\boot\winload.exe`

  `Status: 0xc0000428`

  `Info: Windows cannot verify the digital signature for this file.`

  ![Az 1. ábra a "Ox0000428" állapotú Windows rendszerindítási kezelőt és a "Windows nem tudja ellenőrizni a fájl digitális aláírását" című ábrán látható.](./media/windows-boot-error-invalid-image-hash/1-cannot-verify-signature.png)

vagy az üzenet:

  `File: \Windows\system32\winload.exe`

  `Status: 0xc0000428`

  `Info: The digital signature for this file couldn't be verified.`
    
  ![A 2. ábrán a "Ox0000428" állapotú Windows rendszerindítási kezelő és a "a fájl digitális aláírása nem ellenőrizhető" információ látható.](./media/windows-boot-error-invalid-image-hash/2-digital-signature-not-verified.png)

## <a name="cause"></a>Ok

A virtuális gép felépítéséhez használt rendszerkép egy lejárati dátummal rendelkező előzetes rendszerkép volt, nem pedig egy RTM (kiadás a gyári) rendszerképnek. 

Az előzetes verziójú lemezképek egy kijelölt életciklussal rendelkeznek, és a megjelenő képernyőkép a lejárati dátum megadásakor jelenik meg, ami azt jelenti, hogy a kép próbaverziója véget ért.

### <a name="example-of-preview-images"></a>Előnézet lemezképek – példa

`a699494373c04fc0bc8f2bb1389d6106__Windows-Server-Technical-Preview-201505.01-en.us-127GB.vhd`

Nem lehet kiterjeszteni egy előnézeti kép lejárati dátumát. Ha az előzetes verzió lejárt, a virtuális gép már nem fog tudni elindulni.

- A terméktől függően előfordulhat, hogy a próbaidőszak változhat. Például a Windows előnézeti lemezképek 180 napos próbaidőszakot biztosítanak.

- Az Azure-ban az előzetes verziójú Windows rendszerhez készült összes rendszerkép tartalmazni fogja a leírását, hogy azok nem éles környezetben való használatra szántak, és csak a megadott próbaidőszakra vagy "előzetes kiadásra" használhatók.

## <a name="solution"></a>Megoldás

Ha a rendszerkép egy előnézeti kép, nem lehet kiterjeszteni a használt lemezkép lejárati idejét, hanem egy [új virtuális gépet kell telepítenie](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) egy nem előzetes verziójú lemezkép használatával. Az alábbi lépések segítséget nyújtanak annak azonosításában, hogy az előnézeti képet használta-e, valamint olyan erőforrásokat biztosít, amelyek segítséget nyújtanak az adatoknak a virtuális gépről egy új virtuális gépre való átvitelében. Ha pozitívan azonosította a rendszerképet előzetes képként, a rendszerkép nem helyreállítható, mert már lejárt.

A beállításoktól függően a Azure PowerShell vagy az Azure CLI segítségével lekérdezheti a rendszerképet, és megállapíthatja, hogy az előnézeti kép-e. Ezekkel a parancsokkal ellenőrizheti, hogy a rendszerkép előnézeti kép-e.

### <a name="query-using-azure-powershell"></a>Lekérdezés Azure PowerShell használatával

1. Nyissa meg a Windows PowerShell alkalmazást.
1. Futtassa az alábbi parancsot:

   ```powershell
   $locName = "<LOCATION>" 
   $pubName = "<PUBLISHER NAME>" 
   $offerName = "<OFFER NAME>" 
   $skuName= "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   Get-AzVMImagePublisher -Location $locName | Select $pubName
   Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
   Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
   Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
   ```

- Az előző parancsokban cserélje le a, a, a és a parancsot `<LOCATION>` `<PUBLISHER NAME>` `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` a megadott információra. Távolítsa el a "<" és a ">" szimbólumokat is.

  Lásd a következő példát:

  ```powershell
  $locName = "West US" 
  $pubName = "MicrosoftWindowsServer" 
  $offerName = "WindowsServer" 
  $skuName= "2016-Datacenter"
  Get-AzVMImagePublisher -Location $locName | Select $pubName
  Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
  Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
  Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName | Select Version
  ```

### <a name="query-using-the-azure-cli"></a>Lekérdezés az Azure CLI használatával

1. Ha még nem tette meg, akkor [telepítenie kell az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)-t.
1. A letöltés után a parancssor vagy a PowerShell használatával írja be a `az login` parancsot, majd jelentkezzen be a fiókja hitelesítő adataival.
1. Miután bejelentkezett, adja meg a következő parancsokat:

   ```powershell
   az vm image list-publishers --location "<LOCATION>"
   az vm image list-offers --location "West US" --publisher "<PUBLISHER NAME>"
   az vm image list-skus --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>"
   az vm image list  --location "West US" --publisher "<PUBLISHER NAME>" --offer "<OFFER NAME>" --sku "<YEAR WHEN THIS IMAGE WAS RELEASED>"
   ```

- Az előző parancsokban cserélje le a, a, a és a parancsot `<LOCATION>` `<PUBLISHER NAME>` `<OFFER NAME>` `<YEAR WHEN THIS IMAGE WAS RELEASED>` a megadott információra. Távolítsa el a "<" és a ">" szimbólumokat is.

  Lásd a következő példát:

  ```powershell
  az vm image list-publishers --location "West US"
  az vm image list-offers --location "West US" --publisher "MicrosoftWindowsServer"
  az vm image list-skus --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer"
  az vm image list  --location "West US" --publisher "MicrosoftWindowsServer" --offer "WindowsServer" --sku "2016-Datacenter"
  ```
