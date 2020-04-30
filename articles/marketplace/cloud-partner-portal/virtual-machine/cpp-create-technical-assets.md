---
title: Technikai eszközök létrehozása virtuálisgép-ajánlathoz az Azure Marketplace-en
description: A cikk azt ismerteti, hogyan hozhat létre egy virtuálisgép-ajánlathoz tartozó technikai eszközöket az Azure piactéren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: c6ca017e18a83f0745140cba15d9c894e5a21c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148140"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Technikai eszközök létrehozása virtuálisgép-ajánlathoz

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuális gépekre vonatkozó ajánlatoknak a partner Centerbe való áthelyezését. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az Azure-beli [virtuális gépek technikai eszközeinek létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) című témakör utasításait.

Ez a szakasz végigvezeti az Azure Marketplace-hez készült virtuálisgép-(VM-) ajánlat technikai eszközeinek létrehozásán és konfigurálásán.  A virtuális gép két összetevőt tartalmaz: a megoldás virtuális merevlemezét (VHD) és opcionálisan kapcsolódó adatlemezeket.  

- A *virtuális merevlemezek (VHD-k)*, amely tartalmazza az operációs rendszert és a megoldást, amelyet az Azure Marketplace-ajánlatával fog üzembe helyezni. A VHD előkészítési folyamata attól függően eltérő, hogy Linux-alapú, Windows-alapú vagy egyéni virtuális gép-e.
- Az *adatlemezek* dedikált, állandó tárterületet jelentenek a virtuális gépek számára. Ne *használja a* megoldás VHD-jét (például a `C:` meghajtót) az állandó információk tárolásához.

A virtuálisgép-lemezképek egy operációsrendszer-lemezt és nulla vagy több adatlemezt tartalmaznak. Lemezenként egy virtuális merevlemezre van szükség. Még az üres adatlemezek létrehozásához virtuális merevlemez szükséges.
Konfigurálnia kell a virtuális gép operációs rendszerét, a virtuális gép méretét, a megnyitni kívánt portokat és legfeljebb 15 csatlakoztatott adatlemezt.

> [!TIP] 
> A használt operációs rendszertől függetlenül csak az SKU számára szükséges adatlemezek minimális számát adja hozzá. Az ügyfelek nem tudják eltávolítani a rendszerkép részét képező lemezeket az üzembe helyezéskor, de mindig hozzáadhatnak lemezeket az üzembe helyezés során vagy azt követően is. 

> [!IMPORTANT]
> *Ne változtassa meg a lemezek darabszámát egy új rendszerkép-verzióban.* Ha újra kell konfigurálnia az adatlemezeket a rendszerképben, adjon meg egy új SKU-t. A különböző lemezekkel rendelkező új lemezképek közzétételének lehetősége lesz az új központi telepítés feltörésére az új lemezkép verziója alapján, az automatikus skálázás, a megoldások automatikus központi telepítése Azure Resource Manager sablonokon és más forgatókönyveken keresztül.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Alapvető műszaki ismeretek

Ezeknek az eszközöknek a tervezése, fejlesztése és tesztelése időt vesz igénybe, és technikai ismeretekre van szüksége az Azure platformról és az ajánlat létrehozásához használt technológiákról. A megoldás tartományán kívül a mérnöki csapatnak ismernie kell a következő Microsoft-technológiákat: 
-    Az Azure- [szolgáltatások](https://azure.microsoft.com/services/) alapszintű ismertetése 
-    [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/)
-    Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage) és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking) együttműködésének ismerete
-    [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) működésének ismerete
-    A [JSON](https://www.json.org/) működésének ismerete


## <a name="suggested-tools"></a>Javasolt eszközök 

A VHD-k és virtuális gépek kezeléséhez a következő parancsfájl-környezetek közül választhat:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Emellett javasoljuk a következő eszközök hozzáadását a fejlesztői környezethez: 

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Javasoljuk továbbá, hogy tekintse át az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon elérhető eszközöket, és ha a Visual studiót használja, a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.


## <a name="next-steps"></a>További lépések

A jelen szakasz következő cikkei végigvezetik a virtuálisgép-eszközök létrehozásának és regisztrálásának lépésein:

1. [Azure-kompatibilis virtuális merevlemez létrehozása](./cpp-create-vhd.md) azt ismerteti, hogyan hozható létre az Azure-kompatibilis Linux-vagy Windows-alapú vhd-fájl.  Ide tartozik az ajánlott eljárások, például a méretezés, a javítások és a virtuális gép előkészítése a feltöltéshez.

2. [A virtuális géphez való kapcsolódás](./cpp-connect-vm.md) elmagyarázza, hogyan csatlakozhat távolról az újonnan létrehozott virtuális géphez, és hogyan jelentkezhet be.  A cikk azt is ismerteti, hogyan állíthatja le a virtuális gépet a használati költségekre való mentéshez.

3. [A virtuális gép konfigurálása](./cpp-configure-vm.md) elmagyarázza, hogyan válassza ki a megfelelő VHD-méretet, általánosítsa a lemezképet, alkalmazza a legutóbbi frissítéseket (javítások), és ütemezze az egyéni konfigurációkat.

4. [Virtuális gép üzembe helyezése virtuális merevlemezről](./cpp-deploy-vm-vhd.md) : elmagyarázza, hogyan regisztrálhat egy virtuális gépet egy Azure-beli üzembe helyezett VHD-ről.  Felsorolja a szükséges eszközöket, valamint azt, hogy miként használhatók a felhasználói virtuálisgép-lemezképek létrehozásához, majd az [Microsoft Azure Portal](https://ms.portal.azure.com/) -vagy PowerShell-parancsfájlok használatával üzembe helyezhetők az Azure-ban. 

5. [A virtuális gép rendszerképének](./cpp-certify-vm.md) tanúsítása elmagyarázza, hogyan tesztelheti és küldheti el az Azure Marketplace minősítéshez készült virtuálisgép-rendszerképet. Ismerteti, hogy hol szerezhető be a *minősítési teszt eszköz az Azure Certified* eszközhöz, és hogyan használható az eszköz a virtuálisgép-rendszerkép tanúsítására. 

6. Az [sas URI beszerzése](./cpp-get-sas-uri.md) című cikk azt ismerteti, hogyan kérhető le a virtuálisgép-rendszerkép (ek) közös hozzáférésű aláírási (SAS) URI-ja.
 
A [közös hozzáférés-aláírási URL-címmel kapcsolatos gyakori](./cpp-common-sas-url-issues.md) problémák felsorolják az SAS URI-k és a hozzájuk tartozó lehetséges megoldások használata során felmerülő gyakori problémákat.

Az összes lépés elvégzése után készen áll a VM- [ajánlat közzétételére](./cpp-publish-offer.md) az Azure Marketplace-en.
