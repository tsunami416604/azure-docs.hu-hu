---
title: Virtual Machine Scale Sets elérhetővé az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan a felhő üzemeltetője adhat hozzá virtuálisgép-méretezési csoportok az Azure Stack piactéren
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/05/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 4e77e187d969af7ea2a12754b18d4a218daceed6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411906"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Virtual Machine Scale Sets elérhetővé az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A Virtual machine scale sets az Azure Stack számítási erőforrás. Üzembe helyezéséhez és az azonos virtuális gépek kezelésére használhatja őket. Az összes virtuális gép konfigurálva, a méretezési csoportok nem szükséges előzetesen kiépített virtuális gépek. Sokkal jobban is egyszerűsödik a nagy számítási igényű, big data és tárolóalapú számítási feladatokra nagyméretű szolgáltatások kiépítése.

Ez a cikk végigvezeti a méretezési csoportok az Azure Stack piactéren elérhetővé. Az eljárás befejezése után a felhasználók adhat hozzá, azok az előfizetések virtuálisgép-méretezési csoport állítja be.

Virtuális gép méretezési csoportok az Azure Stacken hasonlóak a virtuális gép méretezési csoportok az Azure-ban. További információkért tekintse meg a következő videót:
* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

Az Azure Stacken virtuálisgép-méretezési csoportok automatikus skálázása nem támogatott. További példányokat adhat hozzá egy méretezési csoportot a Resource Manager-sablonok, a parancssori felület vagy a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

- **A Marketplace-en**  
    Regisztrálja az Azure Stack a globális Azure-ral ahhoz, hogy a piactéren elemek rendelkezésre állását. Kövesse a [regisztrálása az Azure Stack az Azure-ral](azure-stack-registration.md).
- **Operációs rendszer lemezképének**  
    Ha még nem adott hozzá az operációs rendszer lemezképét az Azure Stack piactéren, [Azure Stack piactéren elem felvétele az Azure-ból](asdk/asdk-marketplace-item.md).

## <a name="add-the-virtual-machine-scale-set"></a>A virtuális gép méretezési csoport hozzáadása

1. Nyissa meg az Azure Stack piactéren, és csatlakozzon az Azure-bA. Válassza ki **Marketplace felügyeleti**> **+ Hozzáadás az Azure-ból**.

    ![Marketplace-en kezelése](media/azure-stack-compute-add-scalesets/image01.png)

2. Adja hozzá, és töltse le a Virtual Machine Scale Set Piactéri elemet.

    ![Virtuálisgép-méretezési csoport](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Egy virtuálisgép-méretezési csoportban található rendszerképek frissítése

Miután létrehozott egy virtuálisgép-méretezési csoportot, a felhasználók frissíthetik rendszerképeket a méretezési csoport anélkül, hogy a méretezési csoportot, hogy újra létre kell hozni. A lemezképek frissítésének folyamata attól függ, hogy a következő esetekben:

1. Virtuálisgép-méretezési csoport üzembe helyezési sablon beállítása **adja meg a legújabb** a *verzió*:  

   Ha a *verzió* van beállítva, **legújabb** a a *imageReference* méretezési csoport sablonjának szakaszában állítsa be, vertikális felskálázása műveleteket a méretezési készlet használatát a legújabb elérhető verzió a kép a méretezési csoport példányai beállítása. Vertikális felskálázási befejezése után törölheti a régebbi virtuális gép méretezési csoportok példányai.  (A tartozó értékeket *közzétevő*, *ajánlat*, és *termékváltozat* változatlanok maradnak). 

   Az alábbiakban adja meg például a *legújabb*:  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Ahhoz, vertikális felskálázási egy új rendszerképet, le kell töltenie, hogy új lemezképet:  

   - Ha a lemezképet a Marketplace-en-e egy újabb verziójú, mint a rendszerképet a méretezési csoportban lévő: Töltse le az új rendszerképet, amely felváltja a régi lemezképet. Miután váltja fel a képet, a felhasználó folytassa vertikális felskálázás. 

   - Ha a lemezképet a Marketplace-en verziószáma megegyezik a rendszerképet a méretezési csoportban lévő: törölnie kell a rendszerképet a méretezési csoportban lévő használatban lévő, és töltse le az új lemezképet. Az eredeti rendszerkép eltávolítása és az új kép letöltése közötti időszakban nem skálázhatja. 
      
     Ez a folyamat megadása kötelező resyndicate, amellyel használja a ritka fájlformátum, 1803-verzióval jelent meg. 
 

2. Virtuálisgép-méretezési csoport üzembe helyezési sablon beállítása **legújabb nem határoz meg** a *verzió* , és adja meg helyette egy verziószámot:  

    Ha egy újabb verzióval (amely megváltoztatja a rendelkezésre álló verzió) egy rendszerképet tölt le, a méretezési csoportban nem vertikális felskálázás. Ez az elvárt, mert a rendszerkép verziószámát, a méretezési csoport sablonjában megadott elérhetőnek kell lennie.  

További információkért lásd: [operációsrendszer-lemezek és lemezképek](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Távolítsa el a virtuálisgép-méretezési csoportot

Egy virtuális gép eltávolítása a gyűjteményelem készlet méretezése, futtassa a következő PowerShell-parancsot:

```PowerShell  
    Remove-AzsGalleryItem
````

> [!NOTE]
> A Katalóguselem nem lehet, hogy azonnal eltávolítja. Éjszakai, frissítenie kell a portál többször előtt az elem jelenik meg a Marketplace-ről eltávolítva.

## <a name="next-steps"></a>További lépések
[Gyakori kérdések az Azure Stackben](azure-stack-faq.md)