---
title: Közös rendszerkép-katalógus használata Azure Lab Servicesban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Lab-fiókot megosztott képkatalógus használatára úgy, hogy egy felhasználó megoszthat egy képet a másikkal, és egy másik felhasználó is használhatja a rendszerképet a sablon létrehozásához a laborban.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: spelluru
ms.openlocfilehash: 608ff7366acf655b12b5a1eb0faf540249c0c1f7
ms.sourcegitcommit: 0fa52a34a6274dc872832560cd690be58ae3d0ca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84204701"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Megosztott képgyűjtemény használata Azure Lab Services
Ebből a cikkből megtudhatja, hogy a pedagógusok/labor-rendszergazdák hogyan menthetik a sablon virtuálisgép-rendszerképeit egy [megosztott](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries) képkatalógusba, így mások is használhatják a laborokat. 

## <a name="scenarios"></a>Forgatókönyvek
Íme a funkció által támogatott néhány forgatókönyv: 

- A labor-fiók rendszergazdája egy megosztott képtárat csatol a labor-fiókhoz, és feltölt egy rendszerképet a közös rendszerkép-katalógusba egy labor környezetén kívül. Ezután a labor-készítők a megosztott képkatalógusból is használhatják ezt a rendszerképet a Labs létrehozásához. 
- A labor-fiók rendszergazdája egy megosztott képtárat csatol a labor-fiókhoz. A tesztkörnyezet létrehozója (oktató) elmenti a saját laborjának testreszabott képét a megosztott képgyűjteménybe. Ezután más labor-készítők is kiválaszthatják ezt a rendszerképet a megosztott képkatalógusból, hogy sablonokat hozzanak létre a laborokhoz. 

    Ha egy képet megosztott képkatalógusba ment, Azure Lab Services replikálja a mentett rendszerképet más, azonos [földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/)régiókban elérhető régiókba. Gondoskodik arról, hogy a rendszerkép elérhető legyen a más régiókban, ugyanabban a földrajzi régióban létrehozott laborokhoz. Ha a képeket egy megosztott képkatalógusba menti, további költségekkel jár, ami magában foglalja az összes replikált rendszerkép díját. Ez a díj eltér a Azure Lab Services használati díjaktól. A megosztott képkatalógus díjszabásával kapcsolatos további információkért lásd: [megosztott képgyűjtemény – számlázás]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).
    
## <a name="prerequisites"></a>Előfeltételek
- Hozzon létre egy megosztott képtárat [Azure PowerShell](../../virtual-machines/windows/shared-images.md) vagy az [Azure CLI](../../virtual-machines/linux/shared-images.md)használatával.
- Csatlakoztatta a megosztott képtárat a labor-fiókhoz. Részletes útmutatásért lásd: [megosztott képgyűjtemény csatolása vagy leválasztása](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Rendszerkép mentése a megosztott képtárba
A megosztott képkatalógus csatolása után a labor-fiók rendszergazdája vagy a pedagógus egy rendszerképet menthet a megosztott képkatalógusba, hogy más oktatók is felhasználhatják őket. 

1. A labor **sablon** lapján válassza az **Exportálás a közös rendszerkép** -katalógusba lehetőséget az eszköztáron.

    ![Rendszerkép mentése gomb](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. Az **Exportálás megosztott rendszerkép** -katalógusba párbeszédpanelen adja meg a **rendszerkép nevét**, majd válassza az **Exportálás**lehetőséget. 

    ![Exportálás megosztott képgyűjteménybe párbeszédpanel](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. A művelet előrehaladását a **sablon** lapon tekintheti meg. A művelet eltarthat egy ideig. 

    ![Exportálás folyamatban](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Ha az exportálási művelet sikeres, a következő üzenet jelenik meg:

    ![Az Exportálás befejeződött](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    Miután mentette a rendszerképet a megosztott rendszerkép-katalógusba, ezt a rendszerképet használhatja a katalógusból egy másik tesztkörnyezet létrehozásakor. Képeket is feltölthet a megosztott rendszerkép-katalógusba egy labor kontextusán kívül. További információ: [megosztott képgyűjtemény – áttekintés](../../virtual-machines/windows/shared-images.md). 

    > [!IMPORTANT]
    > Ha egy [tesztkörnyezet sablonjának rendszerképét](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) Azure Lab Services egy megosztott képtárba menti, a képet **speciális képként**feltölti a katalógusba. A [speciális képek](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) a gép-specifikus információkat és a felhasználói profilokat őrzik meg. Továbbra is közvetlenül feltöltheti az általánosított rendszerképet a katalógusba Azure Lab Serviceson kívül.    

## <a name="use-an-image-from-the-shared-image-gallery"></a>Rendszerkép használata a megosztott rendszerkép-gyűjteményből
A pedagógus a sablon megosztott képgalériájában elérhető egyéni rendszerképeket kiválaszthatja az új Labor létrehozása során.

![Virtuálisgép-rendszerkép használata a katalógusból](../media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> A sablon virtuális gépeket a Azure Lab Services **általánosított** és **speciális** lemezképei alapján is létrehozhatja. 


## <a name="next-steps"></a>További lépések
A megosztott képtárakkal kapcsolatos további információkért lásd a [megosztott képtárat](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries).
