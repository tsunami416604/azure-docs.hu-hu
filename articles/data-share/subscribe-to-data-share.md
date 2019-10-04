---
title: 'Oktatóanyag: Fogadja el az & fogadja az Azure-adatmegosztás előzetes verzióját'
description: Oktatóanyag – az Azure-adatmegosztás előzetes verziójával fogadja el és fogadja az adatfogadást
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 235ef25b2d655c4388dee5bdcf88d179f3373697
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327394"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Oktatóanyag: Az Azure-beli adatmegosztás előzetes verziójával fogadhatja és fogadhat

Ebből az oktatóanyagból megtudhatja, hogyan fogadhatja el az adatmegosztási meghívásokat az Azure-beli adatmegosztás előzetes verziójával. Megtudhatja, hogyan fogadhatja Önnel az Önnel megosztott adatmennyiséget, valamint hogyan engedélyezheti a rendszeres frissítési időközt annak biztosítása érdekében, hogy mindig a legfrissebb pillanatképet kapjon az Önnel megosztott adatokról. 

> [!div class="checklist"]
> * Azure-beli adatmegosztás előzetes meghívásának elfogadása
> * Azure-beli adatmegosztás előnézeti fiókjának létrehozása
> * Adja meg az adatai célhelyét
> * Előfizetés létrehozása az adatmegosztásra ütemezett frissítéshez

## <a name="prerequisites"></a>Előfeltételek
Az adatmegosztási Meghívások elfogadása előtt számos Azure-erőforrást kell kiépíteni, amelyek alább láthatók. 

Az adatmegosztási Meghívások elfogadása előtt győződjön meg arról, hogy az összes előfeltétel befejeződik. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy Azure Storage-fiók: Ha még nem rendelkezik ilyennel, létrehozhat egy [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)is. 
* Adatmegosztási meghívás: Meghívót Microsoft Azure egy "Azure-adatmegosztási meghívót **<yourdataprovider@domain.com>** " címmel.
* Jogosultság a szerepkör-hozzárendelés hozzáadásához a Storage-fiókhoz, amely megtalálható a *Microsoft. Authorization/szerepkör-hozzárendelés/írás* engedélyben. Ez az engedély létezik a tulajdonosi szerepkörben. 
* A Microsoft. DataShare erőforrás-szolgáltatói regisztrációja. Ennek módjáról az [Azure Resource Providers](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) dokumentációjában talál további információt. 

> [!IMPORTANT]
> Az Azure-beli adatmegosztások elfogadásához és fogadásához először regisztrálnia kell a Microsoft. DataShare erőforrás-szolgáltatót, és annak a Storage-fióknak a tulajdonosának kell lennie, amelybe befogadja az adatgyűjtést Az adatmegosztási erőforrás-szolgáltató regisztrálásához és a Storage-fiók tulajdonosként való hozzáadásához kövesse az Azure-beli [Adatmegosztások előzetes verziójának hibakeresése](data-share-troubleshoot.md) című részben leírt utasításokat. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="open-invitation"></a>Meghívás megnyitása

Az adatszolgáltatótól érkező meghívót a Beérkezett üzenetek között találja. A meghívó Microsoft Azure származik, és az **Azure-beli adatmegosztási meghívást kapott <yourdataprovider@domain.com>** . Jegyezze fel a megosztás nevét annak biztosításához, hogy a megfelelő megosztást fogadja el, ha több meghívót is használ. 

Az Azure-beli meghívót a meghívás **megtekintése** lehetőségre kattintva tekintheti meg. Ekkor a kapott megosztások nézetre kerül.

![Meghívások](./media/invitations.png "listája") 

Válassza ki a megtekinteni kívánt megosztást. 

## <a name="accept-invitation"></a>Meghívás elfogadása
Győződjön meg arról, hogy az összes mezőt felülvizsgálják, beleértve a használati **feltételeket**is. Ha elfogadja a használati feltételeket, a jelölőnégyzet bejelölésével jelezheti, hogy elfogadja. 

![Használati feltételek](./media/terms-of-use.png "használati feltételek") 

A *cél adatmegosztási fiók*területen válassza ki azt az előfizetést és erőforráscsoportot, amelybe az adatmegosztást telepíteni fogja. 

Az **adatmegosztási fiók** mezőben válassza az **új létrehozása** lehetőséget, ha nem rendelkezik meglévő adatmegosztási fiókkal. Ellenkező esetben válasszon ki egy meglévő adatmegosztási fiókot, amelyet el szeretne fogadni az adatmegosztásban. 

A *Beérkezett megosztás neve* mezőben hagyhatja az alapértelmezett által megadott értéket, vagy megadhat egy új nevet a fogadott megosztáshoz. 

![Célként megadott adatmegosztási fiók](./media/target-data-share.png "célként megadott adatmegosztási fiókja") 

Ha jóváhagyta a használati feltételeket, és megadta a megosztás helyét, válassza az *elfogadás és konfigurálás*lehetőséget. Ha ezt a beállítást választja, a rendszer létrehoz egy megosztási előfizetést, és a következő képernyő megkéri, hogy válasszon ki egy célként szolgáló Storage-fiókot, amelybe másolni kívánja az adatait. 

Az ![Accept Options](./media/accept-options.png "elfogadás beállításai") 

Ha azt szeretné, hogy a meghívót most is elfogadja, de később konfigurálja a tárhelyet, válassza az *elfogadás és konfigurálás később*lehetőséget. Ez a beállítás lehetővé teszi, hogy később konfigurálja a cél Storage-fiókot. A tárterület későbbi konfigurálásának folytatásához tekintse meg a [Storage-fiók konfigurálása](how-to-configure-mapping.md) című oldalt, amely részletesen ismerteti az adatmegosztási konfiguráció folytatásának lépéseit. 

Ha nem szeretné elfogadni a meghívót, válassza az *elutasítás*lehetőséget. 

## <a name="configure-storage"></a>A tárolás konfigurálása
A tároló *beállításai*területen válassza ki azt az előfizetést, erőforráscsoportot és Storage-fiókot, amelyet az adataihoz szeretne fogadni. 

![Cél tárolási beállítások](./media/target-storage-settings.png "célhelyének tárolója") 

Az adatok rendszeres frissítésének engedélyezéséhez győződjön meg arról, hogy engedélyezi a pillanatkép-beállításokat. Vegye figyelembe, hogy ha az adatszolgáltató belefoglalta az adatmegosztásba, csak egy pillanatkép-beállításra vonatkozó ütemterv jelenik meg. 

![Pillanatkép-beállítások](./media/snapshot-settings.png "Pillanatkép-beállításai") 

Kattintson a *Mentés* gombra. 

## <a name="trigger-a-snapshot"></a>Pillanatkép indítása

A kapott megosztások – > Részletek lapon elindíthat egy pillanatképet a **Pillanatkép indítása**lehetőség kiválasztásával. Itt aktiválhatja az adatok teljes vagy növekményes pillanatképét. Ha az első alkalommal fogadja az adatait az adatszolgáltatótól, válassza a teljes másolás lehetőséget. 

![Trigger pillanatkép]-(./media/trigger-snapshot.png "trigger pillanatképe") 

Ha a legutóbbi futtatási állapot *sikeres*, nyissa meg a Storage-fiókot a kapott adatértékek megtekintéséhez. 

A használt Storage-fiók megadásához válassza az **adatkészletek**lehetőséget. 

![Fogyasztói adatkészletek](./media/consumer-datasets.png "fogyasztói adatkészletének leképezése") 

## <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeinek megtekintéséhez navigáljon a fogadott megosztások – > előzmények elemre. Itt megtalálhatja az elmúlt 60 napban létrehozott összes pillanatkép előzményeit. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan fogadhat és fogadhat Azure-adatmegosztást. Ha többet szeretne megtudni az Azure-beli adatmegosztási fogalmakról, folytassa a [Concepts: Az Azure adatmegosztási terminológiája @ no__t-0.