---
title: Oktatóanyag – fogadja el, és az Azure Data megosztás előzetes verzió használata az adatok fogadása
description: Oktatóanyag – fogadja el, és az Azure Data megosztás előzetes verzió használata az adatok fogadása
author: joannapea
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 2dc4994d88fc03c23a6d5722d6018c926e7d6b8c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788163"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share-preview"></a>Oktatóanyag: Fogadja el, és az Azure Data megosztás előzetes verzió használata az adatok fogadása

Ebben az oktatóanyagban megtudhatja hogyan fogadja el az Azure-adatok megosztása előzetes verziójával adatok megosztás meghívót. Megtudhatja, hogyan fogadhat adatokat osztanak meg Önnel, valamint annak biztosítása érdekében, hogy mindig legyen az Önnel megosztott adatok legutóbbi pillanatképe rendszeresen frissüljön a engedélyezése. 

> [!div class="checklist"]
> * Hogyan lehet Azure Adatelőnézet megosztási meghívás elfogadása
> * Egy megosztás Adatelőnézet Azure-fiók létrehozása
> * Adja meg az adatok cél
> * Az adatok megosztása az ütemezett frissítéshez előfizetés létrehozása

## <a name="prerequisites"></a>Előfeltételek
Mielőtt egy adatok megosztási meghívás elfogadásához egy Azure-erőforrások, az alább felsorolt száma kell kiépítenie. 

Győződjön meg arról, hogy teljesüljenek teljes egy adatok megosztási meghívás elfogadása előtt. 

* Azure-előfizetés: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Azure Storage-fiók: Ha még nem rendelkezik egy, létrehozhat egy [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Egy Data megosztási meghívót: Egy című témát a Microsoft Azure meghívót "az Azure az adatok megosztása meghívó **<yourdataprovider@domain.com>** ".

> [!IMPORTANT]
> Fogadja el, és a egy Azure-adatok megosztása fogadására, először regisztrálnia kell az Microsoft.DataShare erőforrás-szolgáltató, és fogadja el az adatokat tároló tárfiók tulajdonosának kell lennie. Kövesse az utasításokat, dokumentált [hibaelhárítása Azure-adatok megosztása – előzetes](data-share-troubleshoot.md) az adatok megosztása erőforrás-szolgáltató regisztrálása, valamint a storage-fiók tulajdonosai adja hozzá saját magát. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="open-invitation"></a>Nyissa meg a meghívót

Nézze meg a postafiókját az adatszolgáltató a meghívót. A meghívó van a Microsoft Azure című **Azure az adatok megosztása felkérését <yourdataprovider@domain.com>** . Jegyezze fel a megosztás neve, annak érdekében, hogy a megfelelő megosztási már fogad, ha több meghívók. 

Válassza ki a **meghívó megtekintése** megtekintéséhez a meghívást, az Azure-ban. Ezzel továbblép a megosztások kapott nézetben.

![Meghívók](./media/invitations.png "meghívások listája") 

Válassza ki a megtekinteni kívánt megosztást. 

## <a name="accept-invitation"></a>Meghívás elfogadása
Ellenőrizze, hogy minden mező felül, beleértve a **használati**. Ha elfogadja a használati feltételeket, a lesz szükséges a jelölőnégyzet bejelölésével jelezze, hogy elfogadja. 

![Használati feltételek](./media/terms-of-use.png "használati feltételeket") 

A *adatok megosztás célfiók*, válassza ki az előfizetést és erőforráscsoportot, amely lesz az adatok megosztása az üzembe. 

Az a **Data megosztás fiók** mezőben válassza **új létrehozása** Ha nem rendelkezik meglévő fiókot az adatok megosztása. Ellenkező esetben válassza ki az adatok megosztása egy meglévő fiókot, amely fogadja el az adatok megosztása be szeretné. 

Az a *megosztási név kapott* mezőt, előfordulhat, hogy hagyja meg az adatokat adja meg a megadott alapértelmezett, vagy adjon meg egy új nevet a fogadott megosztás. 

![Adatok megosztása célfiók](./media/target-data-share.png "cél adatok megosztásához") 

Miután beleegyezett abba, hogy a használati feltételeket, és egy helyet a megosztást, válassza ki a megadott *elfogadás és konfigurálás*. Ha ezt a lehetőséget választja, olyan megosztás előfizetést hoz létre, és a következő képernyő fog kérni, hogy az adatok másolását a cél tárfiók kiválasztása. 

![Fogadja el a beállítások](./media/accept-options.png "fogadja el a beállítások") 

Ha most a meghívás elfogadásához, de a tárolás konfigurálása egy későbbi időpontban, válassza inkább *fogadja el, és a beállítás később*. Ez a beállítás lehetővé teszi, hogy később konfigurálhatja a célként megadott tárfiók. A tárolás konfigurálása a később a folytatáshoz tekintse meg a [konfigurálása a tárfiók](how-to-configure-mapping.md) oldala részletes lépéseit az adatok folytatása oszthatnak meg konfigurációkat. 

Ha nem szeretné a meghívás elfogadásához válassza *elutasítása*. 

## <a name="configure-storage"></a>A tárolás konfigurálása
A *cél tárolási beállítások*, válassza ki az előfizetést, erőforráscsoportot és a storage-fiókot, amelyet meg szeretne kapni az adatokat. 

![Tárolási beállítások cél](./media/target-storage-settings.png "adatméretét") 

Rendszeresen frissíti az adatokat kap, ellenőrizze, hogy engedélyezte a pillanatkép-beállítások. Vegye figyelembe, hogy a pillanatkép-beállítás ütemezés csak akkor jelenik meg, ha az adatszolgáltató beillesztette azt az adatok megosztása. 

![Pillanatkép-beállítások](./media/snapshot-settings.png "pillanatkép-beállítások") 

Kattintson a *Mentés* gombra. 

## <a name="trigger-a-snapshot"></a>Pillanatkép-trigger

Is aktiválhatja a kapott megosztások a pillanatkép -> Részletek lap választásával **eseményindító pillanatkép**. Itt az adatok teljes vagy növekményes pillanatképet is indíthat. Ha most először adatokat fogad az adatszolgáltató, válassza ki a teljes másolatát. 

![Az eseményindító pillanatkép](./media/trigger-snapshot.png "eseményindító pillanatkép") 

Ha a legutóbbi futtatás állapota van *sikeres*, nyissa meg a tárfiók fogadott adatok megtekintéséhez. 

Melyik használt tárfiók ellenőrzéséhez válassza a **adatkészletek**. 

![Fogyasztói adatkészletek](./media/consumer-datasets.png "fogyasztói adatkészlet leképezés") 

## <a name="view-history"></a>Előzmények megtekintése
A pillanatképek előzményeinek megtekintéséhez navigáljon a kapott megosztások -> előzményei. Itt találja az elmúlt 60 nap során létrehozott összes pillanatképet előzményeit. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban tanulással fogadja el, valamint egy Azure-adatok megosztása fogadása. Az Azure az adatok megosztása fogalmak kapcsolatos további információkért folytassa [fogalmak: Az Azure Data megosztása terminológia](terminology.md).