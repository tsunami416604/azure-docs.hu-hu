---
title: Hozzon létre egy virtuálisgép-ajánlat technikai eszközök az Azure Marketplace-en |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre egy virtuálisgép-ajánlat a technikai eszközök az Azure piactéren.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 6f1a93c3d3059e612d8c309b263e263dbb84c67f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050101"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Hozzon létre egy virtuálisgép-ajánlat technikai eszközök

Ez a szakasz végigvezeti létrehozásáról és konfigurálásáról a technikai eszközök esetében a virtuális gép (VM) az ajánlat az Azure Marketplace-en.  Virtuális gép két összetevőkből áll: a megoldás virtuális merevlemez (VHD) és a kapcsolódó opcionális adatlemezeket.  

- *Virtuális merevlemezek (VHD)*, amely tartalmazza az operációs rendszer és a megoldás, amely központilag telepíti az Azure Marketplace-ajánlat a. A folyamat, a virtuális merevlemez előkészítésére eltér attól függően, hogy-e egy Linux-alapú, Windows-alapú, vagy egy egyéni-alapú virtuális Gépet.
- *Adatlemezek* dedikált, állandó tároló virtuális gép jelölik. Tegye *nem* a megoldás virtuális merevlemez használata (például a `C:` meghajtó) az állandó adatok tárolására.

Virtuálisgép-rendszerkép tartalmaz egy operációsrendszer-lemezt és nulla vagy több adatlemezeket. Lemezenként egy virtuális merevlemez szükséges. Még az üres lemezekhez hozható létre egy virtuális Merevlemezt.
Konfigurálnia kell a virtuális gép operációs rendszeren a virtuális gép méretét, a portok megnyitásához, és legfeljebb 15 csatlakoztatott adatlemezekkel.

> [!TIP] 
> A használt operációs rendszertől függetlenül csak a minimális számú adatlemez szükséges a termékváltozathoz. Ügyfelek nem távolítható el a lemezeket, amelyek egy lemezkép részei, a telepítéshez, de ezek mindig hozzáadhat lemezeket során, vagy üzembe helyezés után. 

> [!IMPORTANT]
> *Az új lemezkép verzióban lemezek száma nem változik.* A kép adatlemezek újra kell konfigurálnia, ha egy új Termékváltozat meghatározása. Közzététel egy másik lemez számokkal új lemezkép verziója lesz a használhatatlanná tévő új üzembe helyezési azokban az esetekben az automatikus méretezés, automatikus központi telepítését az Azure Resource Manager-sablonokkal és az egyéb forgatókönyvek megoldásokat az új lemezkép-verzió alapján lehetséges.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Alapvető technikai ismereteit

Tervezése, létrehozása és tesztelése, ezek az eszközök időt vesz igénybe, és az Azure platform és a hozhat létre az ajánlat technológiák műszaki ismerete szükséges. A megoldás tartomány mellett a mérnöki csapathoz a következő Microsoft-technológián Tudásbázis kell rendelkeznie: 
-   Alapvető ismeretekkel [Azure-szolgáltatások](https://azure.microsoft.com/services/) 
-   Hogyan [tervezését és tervezhet meg Azure-alkalmazások](https://azure.microsoft.com/solutions/architecture/)
-   Gyakorlati ismerete [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és [az Azure-hálózatot](https://azure.microsoft.com/services/?filter=networking)
-   Gyakorlati ismerete [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Gyakorlati ismerete [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Javasolt eszközök 

Válassza ki az egyik vagy mindkét virtuális merevlemezek és a virtuális gépek kezeléséhez a következő parancsfájl-kezelési környezetekben:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Emellett javasoljuk, hogy a következő eszközöket ad hozzá a fejlesztési környezetet: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Mellék: [Azure Resource Manager-eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Mellék: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Mellék: [JSON prettify](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Emellett javasoljuk, hogy az elérhető eszközök áttekintése a [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldal és a Visual Studio használata a [Visual Studio-piactér](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>További lépések

Ebben a szakaszban a következő cikkek végigvezetik Önt a lépéseken, és ezeket a virtuális gép eszközöket:

1. [Hozzon létre egy Azure-kompatibilis virtuális merevlemezt](./cpp-create-vhd.md) azt ismerteti, hogyan hozhat létre, vagy egy Linux - vagy Windows-alapú virtuális Merevlemezt, amely az Azure-ral kompatibilis.  Ez magában foglalja a jelenségeket, például a méretezést, javítását és feltöltését a virtuális gép előkészítése.

2. [Csatlakozzon a virtuális géphez](./cpp-connect-vm.md) távolról az újonnan létrehozott virtuális gép csatlakozik, és jelentkezzen be, ismerteti.  Ez a cikk azt is bemutatja, hogyan leállítani a virtuális Gépet a használati költségekre.

3. [A virtuális gép konfigurálásához](./cpp-configure-vm.md) azt ismerteti, hogyan válassza ki a megfelelő virtuális merevlemez méretét, a lemezkép általánossá tétele, alkalmazza a legújabb frissítéseket (javítások) vagy egyéni konfigurációk ütemezése.

4. [Virtuális gép üzembe helyezése egy virtuális merevlemezről](./cpp-deploy-vm-vhd.md) azt ismerteti, hogyan kell regisztrálni a virtuális gép Azure által üzembe helyezett virtuális merevlemezről.  Felsorolja a szükséges eszközöket és azok használatát egy felhasználói Virtuálisgép-lemezkép létrehozásához, majd üzembe helyezése az Azure-ban vagy a [Microsoft Azure-portálon](https://ms.portal.azure.com/) vagy a PowerShell-parancsfájlokat. 

5. [Virtuálisgép-lemezkép tanúsítása](./cpp-certify-vm.md) azt ismerteti, hogyan tesztelheti, és küldje el a Virtuálisgép-lemezkép minősítésre Azure Marketplace-en. Letöltés helye ismerteti a *Minősítőeszköz tesztelése az Azure Certified* eszközt, és ez az eszköz használata a Virtuálisgép-lemezkép tanúsítására. 

6. [SAS URI-JÁNAK beolvasása](./cpp-get-sas-uri.md) azt ismerteti, hogyan tehet szert a közös hozzáférésű jogosultságkód (SAS) URI-azonosítóját a Virtuálisgép-rendszerképeket.
 
Támogató cikként [közös hozzáférési jogosultságkód URL-cím problémák megosztott](./cpp-common-sas-url-issues.md) néhány gyakori problémát tapasztalhat az SAS URI-k és a megfelelő lehetséges megoldásokat tartalmazza.

Miután végrehajtotta ezeket a lépéseket, akkor készen áll a [a Virtuálisgép-ajánlat közzététele](./cpp-publish-offer.md) az Azure piactéren.
