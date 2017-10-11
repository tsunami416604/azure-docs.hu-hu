---
title: "Az egyéni lemezképek Azure Batch készletek kiépítése |} Microsoft Docs"
description: "Létrehozhat egy kötegelt kiépítését egyéni lemezképéről készlet számítási csomópontokra, amelyeket a szoftver- és az alkalmazáshoz szükséges adatokat tartalmazzák. Egyéni lemezkép egy hatékony módját a számítási csomópontok a Batch-alkalmazások futtatásához."
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>Egyéni lemezképet használ a virtuális gépek készlet létrehozása

Azure Batch egyes virtuális gépek létrehozásakor meg kell adnia a virtuális gép (VM) lemezkép, amely az operációs rendszer biztosítja a készlet egyes számítási csomópont. Virtuális gépek készletét hozhat létre, vagy egy Azure piactér lemezkép használatával, vagy egy egyéni előkészítette a Virtuálismerevlemez-kép megadásával. Ha egyéni lemezkép ad meg, hogy szabályozhatják, hogyan az operációs rendszer minden számítási csomópont ellátott időpontra van konfigurálva. Az egyéni lemezképet is tartalmazhatnak, alkalmazások és a rendelkezésre álló a számítási csomóponton, amint ki van építve referenciaadatok.

Egyéni lemezkép használatával is idő takarítható meg a lekérése a készlet számítási csomópontjain készen áll a Batch számítási feladatok futtatásához. Mindig Azure piactér képet, és rendelkezik a kiépítése után telepíthet szoftvereket minden számítási csomópont, lehet, hogy ez a megközelítés egyéni lemezkép használatával-nél kevésbé hatékonyak. 

Néhány ok, amiért a forgatókönyvnek konfigurált egyéni lemezkép használatával tartalmaznia kell:

- **Az operációs rendszer konfigurálása** semmiféle speciális beállítást, az operációs rendszer hajtható végre az egyéni lemezképet. 
- **Nagy alkalmazásokat telepíteni.** Egyéni alkalmazást is telepíthet hatékonyabb, mint az kiépítése után telepíteni őket minden számítási csomóponton.
- **Jelentős mennyiségű adatot másolni.** Az egyéni rendszerképet másolja az adatokat, ha csak egyszer ahelyett, hogy minden számítási csomópont, időt és sávszélességet takaríthat másolni kell.
- **A virtuális gép újraindul a telepítés során.** A virtuális gép újraindul időigényes folyamat lehet, különösen akkor, ha a számítási csomópontok száma.

## <a name="prerequisites"></a>Előfeltételek

- **Batch-fiók létrehozása, a felhasználó előfizetéshez készlet foglalási mód.** Egyéni lemezkép használatával virtuális gép készletek kiépítéséhez, a Batch-fiók létrehozása a felhasználó előfizetéshez [tárolókészlet foglalási mód](batch-api-basics.md#pool-allocation-mode). Ennek a módnak a használatakor a Batch-készletek abban az előfizetésben lesznek lefoglalva, amelyben a fiók található. Tekintse meg a [fiók](batch-api-basics.md#account) szakasz [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md) a tárolókészlet foglalási mód beállításáról a Batch-fiók létrehozásakor.

- **Egy Azure Storage-fiók.** Hozzon létre egy egyéni lemezképet használó virtuális gépek készletét, egy szabványos, általános célú Azure Storage-fiókot az azonos előfizetésbe és azonos térségbe kell. Ha egy egyéni lemezképet hoz létre egy Azure virtuális Gépen, majd, másolja a lemezképet a tárfiókot, ahol a virtuális gép operációsrendszer-lemez helyezkedik el, és nem kell külön storage-fiók létrehozása. 
    
## <a name="prepare-a-custom-image"></a>Egyéni lemezkép előkészítése

Egyéni lemezkép előkészítése kötegelt való használatra, meg kell generalize Linux vagy a Windows meglévő telepítését. Normalizálása egy operációs rendszer telepítése eltávolítja a számítógép-specifikus adatait. Eredménye egy olyanra, amely a más számítógépeken és a virtuális gépek telepíthető.  

> [!IMPORTANT]
> Kötegelt jelenleg nem támogatja a Azure használatával kezelt készlet kiépítéséhez képek. Egy készlet kiépítéséhez használatos az egyéni rendszerképet az Azure Storage kell tárolni. 
>
> Ha az egyéni lemezkép előkészítése, vegye figyelembe a következő szempontokat:
> - Ellenőrizze, hogy a Batch-készletek kiépítéséhez használt alap operációsrendszer-képen nincs-e semmilyen előre telepített Azure-bővítmény, például egyéni szkriptbővítmény. Ha a rendszerkép előre telepített bővítményt tartalmaz, az Azure problémába ütközhet a virtuális gép üzembe helyezése során.
> - Ügyeljen rá, hogy az Ön által biztosított alap operációsrendszer-kép az alapértelmezett ideiglenes meghajtót használja, mivel a Batch-csomópontügynök jelenleg ezt várja el.
>
>

Egyéni lemezképként használhat meglévő előkészített Windows vagy Linux képet. Például, ha szeretné használni egy helyi lemezképet, majd feltölti a lemezképet az Azure Storage-fiók, amely ugyanazt az előfizetést és régióban legyen, mint a Batch fiók használatával [AzCopy](../storage/storage-use-azcopy.md) vagy egy másik feltöltése.

Egyéni lemezkép előkészítheti egy új vagy meglévő Azure virtuális gépről is. Új virtuális gép létrehozásakor Azure piactér lemezkép használata az egyéni lemezképet az alapjául szolgáló lemezképhez, és majd testreszabása. Testre szabhatja az alapjául szolgáló lemezképhez, hozzon létre egy Azure virtuális Gépet, és az alkalmazások és adatok hozzáadása a hozzá. Majd generalize szolgáljanak, az egyéni lemezképet, és az Azure Storage mentheti a virtuális Gépet. 

Egyéni lemezkép előkészítése az Azure virtuális gépről, kövesse az alábbi lépéseket:

1. Hozzon létre egy **nem felügyelt** Azure virtuális gép Azure Piactéri lemezképből. Az Azure piactéren olyan képek mindkét [Windows](../virtual-machines/windows/quick-create-portal.md) és [Linux](../virtual-machines/linux/quick-create-portal.md).
    
    3. lépésében a virtuális gép létrehozási folyamata, győződjön meg arról, hogy kiválasztotta **nem** a **tárolási: használata felügyelt lemezek** lehetőséget. Is tekintse meg a tárfiók nevét az operációsrendszer-lemez a virtuális Gépet, mivel ezt a tárfiókot is, ahol Azure menti az egyéni lemezképet:

    ![Hozzon létre egy nem felügyelt virtuális Gépet, és jegyezze fel a tárfiók neve](media/batch-custom-images/vm-create-storage.png)
 
2. Végezze el a virtuális gép létrehozásának folyamatán, és várja meg, amíg fel kell az Azure-ban. Íme egy olyanra, amely az Azure portálon futó állapotban lévő virtuális gép mutatja:

    ![Hozzon létre egy virtuális gép egy Piactéri rendszerképből](media/batch-custom-images/vm-status-running.png)

3. Ha a virtuális gép fut, kapcsolódni hozzá (a Windows) RDP és az SSH (linuxhoz) keresztül. Minden szükséges szoftvert telepíteni, illetve másolni kívánt adatokat, majd generalize a virtuális gép. Az ismertetett lépéseket követve [a virtuális gép Generalize](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm). 
   
4. Kövesse a lépéseket a [jelentkezzen be Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell). Azure PowerShell telepítéséhez tekintse át [áttekintés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0). 

5. A következő lépések segítségével [felszabadítani a virtuális Gépet, és állítsa a állapot általánosított](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized). 

    Az Azure portálon figyelje meg, hogy a virtuális gép felszabadítása:

    ![Győződjön meg arról, hogy a virtuális gép felszabadítása](media/batch-custom-images/vm-status-deallocated.png)

6.  Létrehozza és menti a VM-lemezkép az Azure Storage használata a [mentés-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) PowerShell-parancsmagot. Kövesse az utasításokat, leírt [lemezkép létrehozásához](../virtual-machines/windows/sa-copy-generalized.md#create-the-image).
    
    A Virtuálisgép-lemezkép menti az Azure Storage-fiók létrehozása a virtuális gép létrehozása után ez az eljárás 1. lépésben ismertetett módon. a Save-AzureRmVMImage parancsmag menti a lemezképet a **rendszer** storage-fiókhoz tartozó tárolóban. A `-DestinationContainername` paraméternév belül egy virtuális könyvtárat a **rendszer** tároló. A `-VHDNamePrefix` paraméter határozza meg a blob nevének egy előtagot. Az előtag van $a kötőjel blob neve. 

    Tegyük fel például, mentés-AzureRmVMImage hívása a következő paraméterekkel:  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    Az eredményül kapott képének mentési a hely és a blob neve itt látható:

    ![A rendszer tárolóhoz mentett VHD-fájl helyét](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Az Azure nem felügyelt virtuális gép több tárfiókok különböző célokra hoz létre. Ha a virtuális gép létrehozása az operációsrendszer-lemezképet a tároló neve nem volt megjegyzést, majd keresése a társított tárolási figyelembe, hogy tartalmazza a **rendszer** tároló. Haladjon végig a **rendszer** tárolót, hogy az egyéni lemezképet, a megadott értékek keresése a **mentés-AzureRmVMImage** parancs.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Készlet létrehozása a portál egy egyéni lemezkép alapján

Ha mentette az egyéni lemezképet, és ismeri a helyét, a Batch-készlet erről a képről is létrehozhat. Kövesse az alábbi lépéseket a készlet létrehozása az Azure-portálon:

1. Az Azure portálon lépjen Batch-fiókjára. Ezt a fiókot az egyéni lemezképet tartalmazó tárfiókkal azonos előfizetésbe és régióban kell lennie. 
2. Az a **beállítások** ablak bal oldalán válassza ki a **készletek** menüpont.
3. Az a **készletek** ablakban válassza ki a **Hozzáadás** parancsot.
4. A a **a készlet hozzáadása** ablakban válassza ki **egyéni lemezkép (Linux/Windows)** a a **képtípus** legördülő menüből. A portál megjeleníti az **Egyéni lemezkép** választóvezérlőt. Keresse meg a tárfiókot, ahol az egyéni lemezképet, és válassza ki a kívánt virtuális Merevlemezt a tárolóból. 
5. Válassza ki a megfelelő **Publisher/ajánlat/Sku** az egyéni virtuális merevlemez.
6. Adja meg a fennmaradó szükséges beállításokkal, többek között a **csomópont méretének**, **céloz dedikált csomópontok**, és **alacsony prioritású csomópont**, valamint minden szükséges, választható beállítások.

    Például egy Microsoft Windows Server Datacenter 2016 egyéni lemezképet a **a készlet hozzáadása** ablak megjelenik, amint az itt látható:

    ![Egyéni Windows-lemezkép a készlet hozzáadása](media/batch-custom-images/add-pool-custom-image.png)
  
Ellenőrizze, hogy egy meglévő készlet egyéni lemezkép alapul, tekintse meg a **operációs rendszer** tulajdonság az erőforrás összefoglaló szakaszában a **készlet** ablak. Ha az alkalmazáskészlet egyéni lemezkép alapján készült, van-e beállítva **egyéni Virtuálisgép-lemezkép**.

A készlethez társított összes egyéni virtuális merevlemezek jelenik meg a készlet **tulajdonságok** ablak.
 
## <a name="next-steps"></a>Következő lépések

- Kötegelt, áttekintéséért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md).
- Batch-fiók létrehozásával kapcsolatos további információkért lásd: [Batch-fiók létrehozása az Azure portálon](batch-account-create-portal.md).