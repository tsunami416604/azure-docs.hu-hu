---
title: "Azure virtuális gépek áttelepítése az Azure Site Recovery segítségével Azure-régiók közötti |} Microsoft Docs"
description: "Azure Site Recovery segítségével Azure IaaS virtuális gépek áttelepítése egy Azure-régió, egy másikra."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.openlocfilehash: e7b925d2daed11ee4e070cda6bcbd4a3511d9c17
ms.sourcegitcommit: 6fb44d6fbce161b26328f863479ef09c5303090f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="migrate-azure-vms-to-another-region"></a>Azure-beli virtuális gépek migrálása másik régióba

Használata mellett a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeléséhez és vészhelyreállítás Azure virtuális gépek és a helyszíni gépeket levezényelni üzleti folytonossági és vészhelyreállítási (BCDR) céljából is használhatja a helyhez Helyreállítási Azure virtuális gépeken futó távoli másodlagos régióba történő áttelepítésének kezelése. Azure virtuális gépek áttelepítéséhez engedélyezze a replikációját őket, és feladatátvételt őket a az elsődleges régióban az Ön által választott másodlagos régióba.

Az oktatóanyag bemutatja, hogyan telepítheti át az Azure virtuális gépeket egy másik régióban. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Recovery services-tároló létrehozása
> * A virtuális gépek replikálásának engedélyezése
> * A virtuális gép áttelepítése feladatátvétel futtatása

Ez az oktatóanyag feltételezi, hogy már rendelkezik Azure-előfizetéssel. Ha nem, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) megkezdése előtt.

>[!NOTE]
>
> Azure virtuális gépek helyreállítási helyreplikálásának jelenleg előzetes verzió.



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez Azure virtuális gépek Azure-régióban át szeretne kell. Emellett számos megkezdése előtt ellenőrizze a beállításokat.


### <a name="verify-target-resources"></a>Ellenőrizze a tároló erőforrásait

1. Győződjön meg arról, hogy az Azure-előfizetéshez lehetővé teszi a virtuális gépek létrehozásához használt vész-helyreállítási cél régióban. A szükséges kvóta engedélyezéséhez forduljon a támogatási szolgálathoz.

2. Ellenőrizze, hogy az előfizetés virtuális gépek támogatásához van szükség, amelyek megfelelnek a forrás virtuális gépeknek elegendő erőforrással rendelkezik. A Site Recovery szerzi méretének vagy a legközelebbi a cél virtuális gép mérete.


### <a name="verify-account-permissions"></a>Fiók engedélyek ellenőrzése

Ha most hozott létre az ingyenes Azure-fiók esetén a rendszer a rendszergazda az előfizetés. Ha nem Ön az előfizetés rendszergazdája, dolgozni a rendszergazdához, és rendelje hozzá a szükséges engedélyekkel. Új virtuális gép replikációjának engedélyezéséhez kell rendelkeznie:

1. Egy virtuális Gépet az Azure-erőforrások létrehozásához szükséges engedélyek. A "Virtuális gép közreműködő" beépített szerepkör tartalmazza azokat az engedélyeket, többek között:
    - Virtuális gép létrehozása a kijelölt erőforráscsoportban engedély
    - Engedéllyel a kiválasztott virtuális hálózatban lévő virtuális gép létrehozása
    - A kiválasztott tárolási fiók írásához engedély

2. Azure Site Recovery-műveleteket kezelése engedéllyel is kell. A "Hely helyreállítási közreműködői" szerepkör minden, a Site Recovery-műveleteket a Recovery Services-tároló felügyeletéhez szükséges engedéllyel rendelkezik.


### <a name="verify-vm-outbound-access"></a>Virtuális gép kimenő hozzáférés ellenőrzése

1. Győződjön meg arról, hogy az áttelepíteni kívánt virtuális gépek hálózati kapcsolattal vezérlésére nem használ egy hitelesítési proxy. 
2. Ebben az oktatóanyagban feltételezzük, hogy a virtuális gépeket áttelepíteni kívánt alkalmazásában hozzáférhet az interneten, és nem használ egy olyan tűzfal proxy kimenő hozzáférés vezérlése érdekében. Ha, a követelmények ellenőrzéséhez [Itt](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).

### <a name="verify-vm-certificates"></a>Virtuális gép tanúsítványok ellenőrzése

Ellenőrizze, hogy a legújabb legfelső szintű tanúsítványokat az áttelepíteni kívánt Azure virtuális gépeken. Ha nem a legújabb legfelső szintű tanúsítványok, a virtuális Gépet nem lehet regisztrálni a Site Recovery szolgáltatásban, biztonsági korlátozások miatt.

- Windows virtuális gépek, a legújabb Windows frissítések telepítése a virtuális Gépen, így a megbízható legfelső szintű tanúsítványok a számítógépen. Leválasztott környezet esetén hajtsa végre a szabványos Windows Update és a szervezet tanúsítvány frissítés folyamatait.
- Linux virtuális gépekhez kövesse az útmutatást, a Linux terjesztő, a virtuális Gépre a legújabb megbízható legfelső szintű tanúsítványok és a visszavont tanúsítványok listájának eléréséhez által biztosított.



## <a name="create-a-vault"></a>Tároló létrehozása

A tároló létrehozása minden régióban, kivéve a forrás-régió.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Új** > **Felügyelet és kezelés** > **Backup és Site Recovery** lehetőségre.
3. A **neve**, adja meg a rövid név **ContosoVMVault**. Ha egynél több előfizetéssel rendelkezik, jelölje ki a megfelelő.
4. Hozzon létre egy erőforráscsoportot **ContosoRG**.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Gyors hozzáférést a tárolóhoz az irányítópultról, kattintson a **rögzítés az irányítópulton** majd **létrehozása**.

   ![Új tároló](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

Az új tároló hozzáadódik a **irányítópult** alatt **összes erőforrás**, majd a fő **Recovery Services-tárolók** lap.






## <a name="select-the-source"></a>A forrás kiválasztása

1. A Recovery Services-tárolók, kattintson **ConsotoVMVault** > **+ replikálás**.
2. A **forrás**, jelölje be **Azure - előzetes**.
3. A **adatforrásról**, válasszon ki forrást az Azure-régió, ahol a virtuális gépek futnak.
4. Válassza ki a Resource Manager üzembe helyezési modellben. Válassza ki a **forrás-erőforráscsoporton**.
5. Kattintson az **OK** gombra a beállítások mentéséhez.


## <a name="enable-replication-for-azure-vms"></a>Azure virtuális gépek replikálásának engedélyezése

A Site Recovery lekéri az előfizetést és az erőforráscsoporthoz társított virtuális gépek listáját.


1. Az Azure portálon kattintson **virtuális gépek**.
2. Válassza ki az áttelepíteni kívánt virtuális gép. Ezután kattintson az **OK** gombra.
3. A **beállítások**, kattintson a **vész-helyreállítási (előzetes verzió)**.
4. A **katasztrófa utáni helyreállítás konfigurálása** > **cél régió** , amelyhez replikálni fogja a cél régiót.
5. Ebben az oktatóanyagban fogadja el az alapértelmezett beállításokat.
6. Kattintson a **engedélyezze a replikálást**. Ekkor elindul egy feladat a virtuális gép replikációjának engedélyezéséhez.

    ![Replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

>[!NOTE]
  >
  > Jelenleg a felügyelt lemezek Azure virtuális gépek replikálása nem támogatott. 

## <a name="run-a-failover"></a>Feladatátvétel futtatása

1. A **beállítások** > **replikált elemek**, kattintson a gépre, és kattintson a **feladatátvételi**.
2. A **feladatátvételi**, jelölje be **legújabb**. A titkosítási kulcs beállítás nem releváns ebben a forgatókönyvben.
3. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt**. A Site Recovery megkísérli indítására, a feladatátvétel előtt állítsa le a virtuális gép – forrásként. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
4. Ellenőrizze, hogy az Azure virtuális gép megjelenik az Azure-ban várt módon.
5. A **replikált elemek**, kattintson a jobb gombbal a virtuális gép > **az áttelepítés végrehajtásához**. Az áttelepítési folyamat befejeződik, és leállítja a virtuális gép replikálása.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áttelepített az Azure virtuális gép egy másik Azure-régiót. Vész-helyreállítási most már konfigurálhatja az áttelepített virtuális gép számára.

> [!div class="nextstepaction"]
> [Az áttelepítés után a vész-helyreállítási beállítása](azure-to-azure-quickstart.md)

