---
title: Azure IaaS virtuális gépek áthelyezése egy másik Azure-régióba az Azure Site Recovery szolgáltatással |} A Microsoft Docs
description: Az Azure Site Recovery használatával az Azure IaaS virtuális gépek áthelyezése egy Azure-régióból a másikba.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0f73e68fd0c01d4323e8675d3fa12f7ca1051cdb
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192922"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Előfordulhat, hogy áthelyezni kívánt Azure-infrastruktúra szolgáltatási (IaaS) virtuális gépek egy adott régióban található a másikra megbízhatóságát, rendelkezésre állást, felügyeleti vagy cégirányítási javítása érdekében. Ez az oktatóanyag bemutatja, hogyan áthelyezi a virtuális gépeket egy másik régióba az Azure Site Recovery használatával. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrás virtuális gépek előkészítése
> * Készítse elő a célrégió
> * Másolja az adatokat a célrégió
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtásához
> * Vesse el az erőforrásokat a forrásrégió


> [!IMPORTANT]
> Ez a cikk ismerteti az Azure virtuális gépek áthelyezése egyik régióból egy másikba *,*. Ha a cél virtuális gépek rendelkezésre állási zónákban helyezi át az infrastruktúra rendelkezésre állásának javítására, lásd: [Azure virtuális gépek áthelyezése a rendelkezésre állási zónák](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy rendelkezik-e az Azure virtuális gépek a forrás Azure-régiót, amelyet át szeretne *a*.
- Ellenőrizze, hogy a választott [forrás régió célzott régió kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), és gondosan válassza ki a célrégióban.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Fiókengedélyek ellenőrzése. Ha az imént létrehozott ingyenes Azure-fiókjába, *,* az előfizetés rendszergazdája. Ha nem a rendszergazda, a szükséges engedélyeket a rendszergazdával együttműködve:
  -  Egy virtuális Gépet, és másolja a cél-adatok replikálásának engedélyezéséhez a Site Recovery használatával, akkor jogosultnak kell lennie a virtuális gép létrehozása az Azure-erőforrások. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel. Az engedélyek a következőket teheti:
        - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
        - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
        - Írás a kiválasztott tárfiókra.

  - A Site Recovery-műveletek kezelésére engedélyek is szükségesek. A Site Recovery-közreműködő szerepkör rendelkezik, amely az Azure Recovery Services-tárolót a Site Recovery-műveletek kezeléséhez szükséges összes engedélyt.

## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

1. Ellenőrizze, hogy az Azure virtuális gépek áthelyezését tervezi, hogy van-e a legújabb főtanúsítványok. Ha nem, biztonsági korlátozások miatt nem engedélyezhető a célrégióban az adatok másolását.

    - Windows virtuális gépek esetén telepítse a legújabb Windows-frissítéseket, a megbízható főtanúsítványok legyenek a gépen. Leválasztott környezet esetén hajtsa végre a szabványos Windows Update és a tanúsítvány-update folyamatokat a szervezet számára.
    - Linux rendszerű virtuális gépekhez útmutatást kövesse a Linux terjesztőt, hogy a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának beolvasása.
2. Győződjön meg arról, hogy nem használ hitelesítési proxyt hálózati kapcsolatok szabályozásához az áthelyezni kívánt virtuális gépek.
3. Ha az áthelyezni kívánt virtuális gép nem rendelkezik hozzáféréssel az internethez, és használ tűzfalproxyt a kimenő hozzáférés vezérlése, ellenőrizze a [követelmények](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Dokumentálja a forrás hálózati elrendezés és az összes olyan erőforrást, amely jelenleg használ, beleértve többek között a (,) terheléselosztók, a hálózati biztonsági csoportok és a nyilvános IP-címek az ellenőrzéshez.

## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Az Azure-előfizetésében győződjön meg arról, hogy létrehozhat virtuális gépeket a vészhelyreállításhoz használt célrégió. Forduljon az ügyfélszolgálathoz a szükséges kvóta engedélyezéséhez, ha szükséges.

2. Győződjön meg arról, hogy előfizetése rendelkezik-e elegendő erőforrás a forrás virtuális gépeket támogatja. A Site Recovery az adatok másolásához a cél használ, ha azt választja ki az azonos méretű vagy a legközelebbi elérhető méret a cél virtuális gépek.

3. Győződjön meg arról, hogy minden összetevő azonosított célként megadott erőforrás létrehozása a forrás hálózati elrendezés. Ez biztosítja, hogy a virtuális gépek lesz a funkciókat és szolgáltatásokat a célrégióban, hogy a forrásrégióban.

   Az Azure Site Recovery automatikusan felderíti és egy virtuális hálózati és storage-fiókot hoz létre, amikor engedélyezi a replikációt a forrás virtuális gép. Hozza létre előre ezeket az erőforrásokat is, és hozzárendelheti azokat a virtuális gép a replikáció engedélyezése lépésben részeként. Azonban manuálisan kell létrehoznia a célrégióban erőforrásokat. Tekintse meg a következő dokumentumokat a leggyakrabban használt hálózati erőforrások létrehozása a a forrás Virtuálisgép-konfiguráció:

   - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Nyilvános IP-cím](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
   Bármely más hálózati összetevők, lásd: a [az Azure hálózati dokumentációja](https://docs.microsoft.com/azure/#pivot=products&panel=network). 

4. A konfiguráció teszteléséhez mielőtt manuálisan elvégzi az áthelyezés [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban. Az éles környezet minimális megérintése a telepítés teszteléséhez hoz létre, és azt javasoljuk.
    
## <a name="copy-data-to-the-target-region"></a>Másolja az adatokat a célrégió
Az alábbi lépéseket az Azure Site Recovery használatával másolhat adatokat a célrégióban.

### <a name="create-the-vault-in-any-region-except-the-source"></a>A forrás kivételével bármelyik régióban hozza létre a tárolót

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Válassza ki **erőforrás létrehozása** > **kezelőeszközök** > **Backup és Site Recovery**.
3. A **neve**, adja meg a rövid név **ContosoVMVault**. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. Támogatott régiók megtekintéséhez [Azure Site Recovery – díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Válassza ki a Recovery Services-tárolókra **áttekintése** > **ConsotoVMVault** > **+ replikálás**.
7. A **forrás**válassza **Azure**.
8. A **forrás helye**, válassza ki a forrás Azure-régió, ahol a virtuális gépek jelenleg futnak.
9. Válassza ki az Azure Resource Manager üzemi modell. Ezután válassza ki a **forrás-előfizetés** és **forrásként szolgáló erőforráscsoportot**.
10. Válassza ki **OK** a beállítások mentéséhez.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Azure virtuális gépek replikálásának engedélyezése és az adatok másolásának indítása

A Site Recovery lekéri az előfizetésben és erőforráscsoportban társított virtuális gépek listáját.

1. Válassza ki a virtuális gép, helyezze át, és válassza ki a kívánt **OK**.
2. A **beállítások**válassza **vész-helyreállítási**.
3. A **vészhelyreállítás konfigurálása** > **célrégió**, válassza ki a célrégió azon-bA replikál.
4. Válassza ki az alapértelmezett tároló erőforrásait, vagy azokkal, amelyek az előre létrehozott használatára.
5. Válassza ki **engedélyezze a replikációt** elindítani a feladatot.

   ![A replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Nyissa meg a tárolóban. A **beállítások** > **replikált elemek**, válassza ki a virtuális gépet, amelyet szeretne áthelyezni a célként megadott régióban. Ezután válassza ki **feladatátvételi teszt**.
2. A **feladatátvételi teszt**, válasszon egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Rendszer nem tölt időt adatok feldolgozásával, így ezt a lehetőséget biztosít egy alacsony helyreállítási időre vonatkozó célkitűzés (RTO).
   - **Legutóbbi alkalmazáskonzisztens**: Összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

3. Válassza ki a cél Azure-beli virtuális hálózatot, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése.

   > [!IMPORTANT]
   > Azt javasoljuk, hogy egy különálló Azure-beli Virtuálisgép-hálózatot használja-e a teszt feladatátvételhez, nem a termelési hálózat a célrégióban.

4. Az áthelyezés tesztelése indításához válassza **OK**. Nyomon követheti, jelölje be a virtuális Gépre kattintva megnyithatja a **tulajdonságait.** Vagy válassza ki a **feladatátvételi teszt** feladat a tárolóban. Ezután válassza ki **beállítások** > **feladatok** > **Site Recovery-feladatok**.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. A tesztelési létrehozott virtuális gépek törléséhez válassza ki **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, és bármely a teszttel kapcsolatos megfigyelések mentéséhez.

## <a name="perform-the-move-and-confirm"></a>Az áthelyezés végrehajtásához, és győződjön meg róla

1. Nyissa meg a tároló **beállítások** > **replikált elemek**, a virtuális gépet, majd válassza ki és **feladatátvételi**.
1. A **feladatátvételi**válassza **legújabb**. 
2. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli a forrásoldali virtuális gép leállítása a feladatátvétel indítása előtt. De feladatátvételi továbbra is fennáll, még akkor is, ha a Leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
3. Ha a feladat befejeződött, ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
4. A **replikált elemek**, kattintson a jobb gombbal a virtuális Gépet, és válassza ki **véglegesítése**. Ez befejezi az áthelyezés. Várjon, amíg a véglegesítés feladat befejeződik.

## <a name="discard-the-resources-from-the-source-region"></a>Vesse el az erőforrásokat a forrásrégió

- Lépjen a virtuális Gépet, majd válassza a **replikálás letiltása**. Ezzel leállítja a virtuális gép számára az adatok másolásának folyamatát.

  > [!IMPORTANT]
  > Fejezze be ezt a lépést, a Site Recovery replikációjára az áthelyezés után felszámított elkerülése érdekében.

Ha nem szeretné újra felhasználhatja a forrás-erőforrásokat, kövesse az alábbi lépéseket:

1. Törölje az összes releváns hálózati erőforrást a forrásrégióban 4. lépésében szereplő [a forrás virtuális gépek előkészítése](#prepare-the-source-vms).
2. Törölje a megfelelő tárfiók a forrásrégióban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan Azure virtuális gépek áthelyezése más Azure-régióban. Vész-helyreállítási most konfigurálhatja a virtuális gépeken.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

