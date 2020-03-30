---
title: Technikai eszközök létrehozása az Azure Piactér virtuálisgép-ajánlatához
description: Bemutatja, hogyan hozhat létre technikai eszközöket egy virtuálisgép-ajánlathoz az Azure Marketplace-en.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: 57f56a341cfc3db6a5f0664503809e6ab6cf3d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278024"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Technikai eszközök létrehozása virtuálisgép-ajánlathoz

Ez a szakasz bemutatja az Azure Piactér virtuális gép (VM) technikai eszközeinek létrehozását és konfigurálását.  A virtuális gép két összetevőt tartalmaz: a megoldás virtuális merevlemez (VHD) és a választható kapcsolódó adatlemezek.  

- *Virtuális merevlemezek (VHDs),* amely tartalmazza az operációs rendszer és a megoldás, amely az Azure Marketplace-ajánlattal telepíti. A virtuális merevlemez előkészítésének folyamata attól függően változik, hogy Linux-alapú, Windows-alapú vagy egyéni virtuális gépről van-e szó.
- *Az adatlemezek* dedikált, állandó tárolót jelentenek egy virtuális gép számára. *Ne* használja a megoldás Virtuális merevlemez `C:` (például a meghajtó) tartós adatok tárolására.

A virtuális géplemez egy operációsrendszer-lemezt és nulla vagy több adatlemezt tartalmaz. Lemezenként egy virtuális merevlemezszükséges. Még az üres adatlemezek hez is létre kell hozni egy virtuális merevlemezt.
Konfigurálnia kell a virtuális gép operációs rendszert, a virtuális gép méretét, a megnyitáshoz a portokat és legfeljebb 15 csatolt adatlemezt.

> [!TIP] 
> Függetlenül attól, hogy melyik operációs rendszert használja, csak a termékváltozat számára szükséges minimális adatlemezek et adja hozzá. Az ügyfelek nem tudják eltávolítani azokat a lemezeket, amelyek a lemezkép részét képezik a telepítés időpontjában, de a telepítés során vagy azt követően bármikor hozzáadhatnak lemezeket. 

> [!IMPORTANT]
> *Ne módosítsa a lemezszámot új lemezverzióban.* Ha újra kell konfigurálnia az adatlemezeket a lemezképben, adjon meg egy új termékváltozatot. Egy új lemezszámmal rendelkező új lemezverzió közzététele az új lemezverzión alapuló új központi telepítés megbontásával járhat automatikus skálázás, a megoldások Azure Resource Manager-sablonokon és más forgatókönyveken keresztül történő automatikus üzembe helyezése esetén.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, létrehozása és tesztelése időt vesz igénybe, és mind az Azure platform, mind az ajánlat létrehozásához használt technológiák műszaki ismerete szükséges. A megoldástartománymellett a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat: 
-   Az [Azure-szolgáltatások](https://azure.microsoft.com/services/) alapvető ismerete 
-   [Azure-alkalmazások tervezése és megtervezése](https://azure.microsoft.com/solutions/architecture/)
-   Az [Azure virtuális gépek](https://azure.microsoft.com/services/virtual-machines/), az Azure [Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking munkaismerete](https://azure.microsoft.com/services/?filter=networking)
-   Az [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) munkaismerete
-   A [JSON](https://www.json.org/) munkaismerete


## <a name="suggested-tools"></a>Javasolt eszközök 

Válassza ki az alábbi parancsfájlok futtatásához tervezett környezetek egyikét vagy mindkettőt a virtuális gépek és a virtuális gépek kezeléséhez:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Ezenkívül azt javasoljuk, hogy adja hozzá a következő eszközöket a fejlesztői környezethez: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio kód](https://code.visualstudio.com/)
    *   Bővítmény: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Kiterjesztés: [Szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Kiterjesztés: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át a rendelkezésre álló eszközöket az [Azure Developer Tools](https://azure.microsoft.com/tools/) lapon, és ha a Visual Studio t használja, a Visual Studio [Piacteret.](https://marketplace.visualstudio.com/)


## <a name="next-steps"></a>További lépések

Ebben a szakaszban az alábbi cikkek végigvezeti kutatja a virtuális gép eszközök létrehozásának és regisztrálásának lépéseit:

1. [Hozzon létre egy Azure-kompatibilis virtuális merevlemezt,](./cpp-create-vhd.md) amely bemutatja, hogyan hozhat létre az Azure-ral kompatibilis Linux- vagy Windows-alapú virtuális merevlemezt.  Ez magában foglalja az ajánlott eljárásokat, például a méretezés, javítás és a virtuális gép feltöltésre való előkészítése.

2. [Csatlakozzon a virtuális géphez](./cpp-connect-vm.md) elmagyarázza, hogyan csatlakozhat távolról az újonnan létrehozott virtuális géphez, és jelentkezzen be.  Ez a cikk azt is ismerteti, hogyan állíthatja le a virtuális gép a használati költségek megtakarítása érdekében.

3. [Konfigurálja a virtuális gépet,](./cpp-configure-vm.md) amely bemutatja, hogyan kell kiválasztani a megfelelő virtuális merevlemez méretét, általánosítani a lemezképet, alkalmazni a legújabb frissítéseket (javításokat) és egyéni konfigurációkat ütemezni.

4. [Virtuális gép üzembe helyezése virtuális merevlemezről](./cpp-deploy-vm-vhd.md) elmagyarázza, hogyan regisztrálhat virtuális gépet egy Azure által telepített virtuális merevlemezről.  Felsorolja a szükséges eszközöket, és hogyan használhatja őket egy felhasználói virtuálisgép-lemezkép létrehozásához, majd üzembe helyezheti azt az Azure-ba a [Microsoft Azure Portalon](https://ms.portal.azure.com/) vagy a PowerShell-parancsfájlok használatával. 

5. [A virtuálisgép-lemezkép hitelesítése](./cpp-certify-vm.md) ismerteti, hogyan tesztelheti és küldheti el a virtuális géplemezképét az Azure Marketplace-tanúsítványhoz. Bemutatja, hogy hol szerezheti be az Azure Certified eszköz *minősítési teszteszközét,* és hogyan használhatja ezt az eszközt a virtuális gép lemezképének hitelesítéséhez. 

6. [A SAS URI-k lekérnie](./cpp-get-sas-uri.md) a közös hozzáférésű hozzáférési aláírás (SAS) URI-ját a virtuális gép lemezkép(ek) számára.
 
Támogató cikkként [a közös megosztott hozzáférésű aláírás URL-címével kapcsolatos problémák](./cpp-common-sas-url-issues.md) felsorolnak néhány gyakori problémát, amelyekkel a SAS URI-k és a megfelelő lehetséges megoldások használatával találkozhat.

Miután elvégezte ezeket a lépéseket, készen áll a [virtuális gép ajánlatának közzétételére](./cpp-publish-offer.md) az Azure Marketplace-en.
