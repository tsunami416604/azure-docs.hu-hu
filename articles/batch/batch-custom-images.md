---
title: Az egyéni lemezképek Azure Batch készletek kiépítése |} Microsoft Docs
description: Létrehozhat egy kötegelt kiépítését egyéni lemezképéről készlet számítási csomópontokra, amelyeket a szoftver- és az alkalmazáshoz szükséges adatokat tartalmazzák. Egyéni lemezkép egy hatékony módját a számítási csomópontok a Batch-alkalmazások futtatásához.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 78bc50a1189d8f42281f81643a5e907d94480082
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-a-managed-custom-image-to-create-a-pool-of-virtual-machines"></a>Felügyelt egyéni lemezképet használ a virtuális gépek készlet létrehozása 

A virtuálisgép-konfiguráció használata Azure Batch-készlet létrehozásakor meg kell adnia egy Virtuálisgép-lemezkép az operációs rendszer biztosít minden számítási csomópont a készletben. Virtuális gépek készletét hozhat létre, vagy Azure Piactéri lemezképhez, vagy egy egyéni lemezképpel (a Virtuálisgép-lemezkép létrehozott és saját kezűleg konfigurálva). Az egyéni lemezképet kell egy *felügyelt képre* erőforrás az azonos Azure-előfizetés és a régióban legyen, mint a Batch-fiók.

## <a name="why-use-a-custom-image"></a>Egyéni lemezkép miért érdemes használni?
Ha ad meg egy egyéni lemezképet, befolyásolni az operációs rendszer konfigurációs és operációs rendszer és az adatlemezek használandó. Az egyéni lemezképet alkalmazások és a referenciaadatok válnak elérhetővé a kötegelt készlet összes csomópontjára, amint kiépítésüket tartalmazhatnak.

Egyéni lemezkép használatával időt takaríthat meg a Batch számítási feladatok futtatásához a készlet számítási csomópontok előkészítése. Az Azure piactér lemezképét használat, és minden számítási csomópont kiépítése után telepíthet szoftvereket, hatékonyabb egyéni lemezkép használatával lehet.

A forgatókönyvnek konfigurált egyéni lemezkép használatával több előnyöket biztosítja:

- **Az operációs rendszer konfigurálása**. Az egyéni lemezkép operációsrendszer-lemez végezheti el az operációs rendszer speciális beállítást. 
- **A telepítés előtti alkalmazások.** Létrehozhat egyéni lemezkép előre telepített alkalmazásokkal az operációsrendszer-lemez, amely hatékony és kevesebb a hibalehetőség mint az alkalmazások telepítése a számítási csomópontok StartTask használatával kiépítése után.
- **Rendszer újraindítása mentéskor virtuális gépeken.** Alkalmazások telepítése általában igényelnek, a virtuális gép újraindul, ami időigényes. Újraindítás időt takaríthat meg, előre az alkalmazás telepítését. 
- **Másolja a nagyon nagy mennyiségű adat egyszer.** Egy felügyelt képre adatlemezek másolásával teheti a kezelt egyéni kép statikus adatok egy részét. Ez csak egyszer kell végrehajtani, és elérhetővé teszi adatokat a készlet minden egyes csomópontra.
- **A választott lemeztípusokat.** A virtuális merevlemez, egy Azure virtuális gép, ezek a lemezek és a saját Linux vagy a Windows telepítési konfigurált pillanatkép felügyelt lemezes felügyelt egyéni kép is létrehozhat. Azt, hogy prémium szintű storage használata az operációsrendszer-lemezképet és az adatok lemezre.
- **Növelje a készletek tetszőleges méretű.** Használatakor a felügyelt egyéni lemezkép-készlet létrehozása a készletben növelhető tetszőleges méretű kér le. Nem kell példányt készítsen lemezképet blob VHD, hogy megfeleljen a virtuális gépek számát. 


## <a name="prerequisites"></a>Előfeltételek

- **Egy felügyelt képre erőforrás**. Hozzon létre egy egyéni lemezképet használó virtuális gépek készletét, hozzon létre egy felügyelt képre erőforrást az azonos Azure-előfizetés és a régióban legyen, mint a Batch-fiókhoz kell. Egy felügyelt képre előkészítéséhez beállítások a következő részben található.
- **Az Azure Active Directory (AAD) hitelesítési**. A kötegelt ügyfél API-ja AAD-alapú hitelesítést kell használnia. Az aad-ben az Azure Batch támogatási részletes ismertetését lásd: [hitelesítéséhez kötegelt szolgáltatási megoldások és az Active Directory](batch-aad-auth.md).

    
## <a name="prepare-a-custom-image"></a>Egyéni lemezkép előkészítése
Egy felügyelt képre előkészítheti a virtuális merevlemez, egy Azure virtuális gép felügyelt lemezzel, vagy egy virtuális gép pillanatképből. A kötegelt ajánlott, felügyelt lemezkép létrehozása a virtuális gép és a felügyelt lemezek vagy a virtuális gép pillanatfelvétele. A felügyelt képre és az alapjául szolgáló erőforrás rendelkeznie kell meglévő méretezést kívánó készletek és a készlet törlése után lehet eltávolítani. 

Ha a lemezkép előkészítése, vegye figyelembe a következő szempontokat:

* Ellenőrizze, hogy a Batch-készletek kiépítéséhez használt alap operációsrendszer-képen nincs-e semmilyen előre telepített Azure-bővítmény, például egyéni szkriptbővítmény. Ha a rendszerkép előre telepített bővítményt tartalmaz, az Azure problémába ütközhet a virtuális gép üzembe helyezése során.
* Győződjön meg arról, hogy megadta alap operációsrendszer-lemezképet használja az alapértelmezett ideiglenes meghajtón. A kötegelt csomópont ügynök jelenleg vár az alapértelmezett ideiglenes meghajtón.
* A Batch-készlet által hivatkozott felügyelt kép típusú erőforrást élettartama idején a készlet nem törölhető. A felügyelt képre erőforrás törlése, majd a készlet nem tudja növelni a további. 

### <a name="to-create-a-managed-image"></a>Felügyelt lemezkép létrehozása
Bármely létező előkészített Windows vagy Linux operációsrendszer-lemez hozhat létre egy felügyelt képre. Például ha szeretné használni egy helyi lemezképet, majd töltse fel a helyi lemez egy Azure Storage-fiók, amely az előfizetés és a régióban van, mint a Batch-fiók AzCopy vagy egy másik feltöltés használatával. A virtuális merevlemez feltöltéséhez, és hozzon létre egy felügyelt képre, lásd az útmutató a [Windows](../virtual-machines/windows/upload-generalized-managed.md) vagy [Linux](../virtual-machines/linux/upload-vhd.md) virtuális gépeket.

Új vagy meglévő Azure virtuális gép vagy Virtuálisgép-pillanatképet készítenie egy felügyelt képre is. 

* Ha egy új virtuális Gépet hoz létre, Azure piactér lemezkép használata a felügyelt képre az alapjául szolgáló lemezképhez, és majd testreszabása. 

* Ha azt tervezi, hogy a lemezképet, a portál használatával, ügyeljen arra, hogy a virtuális Gépet felügyelt lemezes hozza létre. Ez az alapértelmezett tároló beállítása, a virtuális gépek létrehozásakor.

* Ha a virtuális gép fut, kapcsolódni hozzá (a Windows) RDP és az SSH (linuxhoz) keresztül. Minden szükséges szoftvert telepíteni, illetve másolni kívánt adatokat, majd generalize a virtuális gép.  

Egy Azure virtuális gép generalize, és hozzon létre egy felügyelt képre lépéseiért lásd: a útmutatást [Windows](../virtual-machines/windows/capture-image-resource.md) vagy [Linux](../virtual-machines/linux/capture-image.md) virtuális gépeket.

Attól függően, hogy hogyan kívánja Batch-készlet létrehozása a lemezképpel a következő azonosító a kép szüksége:

* Ha azt tervezi, hogy a készlet létrehozása a lemezképpel a kötegelt API-k, a **erőforrás-azonosító** a kép, amely az űrlap `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. 
* Ha azt tervezi, használhatja a portált, a **neve** kép. 





## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Készlet létrehozása a portál egy egyéni lemezkép alapján

Ha mentette az egyéni lemezképet, és ismeri az erőforrás-Azonosítóval vagy névvel, a Batch-készlet erről a képről is létrehozhat. A következő lépések bemutatják a készlet létrehozása az Azure-portálról.

> [!NOTE]
> Ha a készlet valamelyik a kötegelt API-k segítségével hoz létre, győződjön meg arról, hogy az AAD-hitelesítés használja identitás jogosult legyen a lemezkép-erőforrást. Lásd: [hitelesítéséhez kötegelt szolgáltatási megoldások és az Active Directory](batch-aad-auth.md).
>

1. Az Azure portálon lépjen Batch-fiókjára. Ezt a fiókot az egyéni lemezképet tartalmazó erőforráscsoportot, az előfizetés és a régióban kell lennie. 
2. Az a **beállítások** ablak bal oldalán válassza ki a **készletek** menüpont.
3. Az a **készletek** ablakban válassza ki a **Hozzáadás** parancsot.
4. A a **a készlet hozzáadása** ablakban válassza ki **egyéni lemezkép (Linux/Windows)** a a **képtípus** legördülő menüből. Az a **egyéni Virtuálisgép-lemezkép** legördülő menüben válassza ki a lemezkép nevét (rövid alak: az erőforrás-azonosító).
5. Válassza ki a megfelelő **Publisher/ajánlat/Sku** a egyéni lemezkép számára.
6. Adja meg a fennmaradó szükséges beállításokkal, többek között a **csomópont méretének**, **céloz dedikált csomópontok**, és **alacsony prioritású csomópont**, valamint minden szükséges, választható beállítások.

    Például egy Microsoft Windows Server Datacenter 2016 egyéni lemezképet a **a készlet hozzáadása** ablak a lent látható módon:

    ![Egyéni Windows-lemezkép a készlet hozzáadása](media/batch-custom-images/add-pool-custom-image.png)
  
Ellenőrizze, hogy egy meglévő készlet egyéni lemezkép alapul, tekintse meg a **operációs rendszer** tulajdonság az erőforrás összefoglaló szakaszában a **készlet** ablak. Ha az alkalmazáskészlet egyéni lemezkép alapján készült, van-e beállítva **egyéni Virtuálisgép-lemezkép**.

A készlethez társított összes egyéni lemezképek jelenik meg a készlet **tulajdonságok** ablak.
 
## <a name="next-steps"></a>További lépések

- Kötegelt, áttekintéséért lásd: [Develop nagyméretű párhuzamos számítási solutions a kötegelt](batch-api-basics.md).
