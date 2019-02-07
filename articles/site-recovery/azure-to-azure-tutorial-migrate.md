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
ms.openlocfilehash: 70229a0b211acd08d285ad7a943f39285fad8012
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810422"
---
# <a name="move-azure-vms-to-another-region"></a>Azure-beli virtuális gépek áthelyezése egy másik régióba

Nincsenek a különböző forgatókönyvekben, ahol érdemes áthelyezheti a meglévő Azure IaaS virtuális gépek egy adott régióban található egy másikra - megbízhatóság és a rendelkezésre állás, a meglévő virtuális gépeket, vagy a kezelhetőség javítása vagy stb., mint a részletes cégirányításiokai[Itt](azure-to-azure-move-overview.md). Használata mellett a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás való kezelésére és vezénylésére vészhelyreállítása az Azure-beli és helyszíni gépek üzletmenet-folytonossági és vészhelyreállítási (BCDR) céljából is használhatja a hely Helyreállítási kezelése Azure virtuális gépek áthelyezése egy másodlagos régióba.       

Ez az oktatóanyag bemutatja, hogyan Azure virtuális gépek áthelyezése egy másik régióba az Azure Site Recovery használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * [Előfeltételek ellenőrzése](#verify-prerequisites)
> * [A forrás virtuális gépek előkészítése](#prepare-the-source-vms)
> * [Készítse elő a célrégió](#prepare-the-target-region)
> * [Másolja az adatokat a célrégió](#copy-data-to-the-target-region)
> * [A konfiguráció tesztelése](#test-the-configuration) 
> * [Az áthelyezés végrehajtásához](#perform-the-move-to-the-target-region-and-confirm) 
> * [Elveti a forrásrégióban található erőforrások](#discard-the-resource-in-the-source-region) 

> [!IMPORTANT]
> Ez a dokumentum segít az áthelyezését a Azure virtuális gépek egyik régióból a másikba van, ha a követelmény a rendelkezésre állási zónához rögzített virtuális gépeket egy másik régióban lévő virtuális gépek áthelyezése a rendelkezésre állás növelése érdekében, tekintse meg az oktatóanyag.

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

- Győződjön meg arról, hogy az Azure virtuális gépek Azure-régióban, amelyről el szeretné helyezni.
- Győződjön meg arról, ha a választott [forrásrégió - cél régió kombináció támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) és a célként megadott régióban lévő képesek tájékozott döntés
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Fiókengedélyek ellenőrzése: Ha most hozta létre az ingyenes Azure-fiókját, akkor Ön az előfizetés rendszergazdája. Ha nem Ön az előfizetés rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a fiókhoz a szükséges engedélyeket. Engedélyezze a virtuális gép replikációját, és lényegében másolja az adatokat az Azure Site Recoveryvel, kell rendelkeznie:

    1. Engedélyek virtuális gép az Azure-erőforrásokban történő létrehozásához. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel, amelyek a következők:
        - Engedély virtuális gépek a kiválasztott erőforráscsoportban történő létrehozásához
        - Engedély virtuális gépek a kiválasztott virtuális hálózaton történő létrehozásához
        - Engedély a kiválasztott tárfiókra történő íráshoz

    2. Az Azure Site Recovery-műveletek kezelésére szóló engedélyre is szüksége lesz. A „Site Recovery-közreműködő” szerepkör minden olyan engedéllyel rendelkezik, amely a Site Recovery-műveletek Recovery Services-tárolóban történő kezeléséhez szükséges.

## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

1. Ellenőrizze, hogy a legújabb főtanúsítványok megtalálhatók-e az áthelyezni kívánt Azure virtuális gépeken. Ha a legújabb főtanúsítványok nem találhatók található, az adatok másolását a célrégióban, biztonsági okokból nem lehet engedélyezni.

    - A Windows rendszerű virtuális gépek esetében az összes új Windows-frissítést telepíteni kell a virtuális gépen, így a megbízható főtanúsítványok mindegyike megtalálható lesz a számítógépen. Leválasztott környezet esetén hajtsa végre a vállalat szabványos Windows Update- és tanúsítványfrissítési folyamatait.
    - Linux rendszerű virtuális gépek esetében kövesse a Linux-terjesztőtől származó útmutatást a legújabb megbízható főtanúsítványok és a visszavont tanúsítványok listájának lekéréséhez a virtuális gépen.
2. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezni kívánt virtuális gépek hálózati kapcsolatának vezérlésére.
3. Az áthelyezni kívánt virtuális gép nem rendelkezik hozzáféréssel az internet-e használ tűzfalproxyt a kimenő hozzáférés vezérléséhez, ellenőrizze a követelmények [Itt](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity).
4. Azonosítsa a forrás hálózati elrendezés és az összes erőforrást, hogy-e jelenleg a – többek között, de nem kizárólagosan terheléselosztók, az NSG-k, a nyilvános IP-stb.

## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. a Site Recovery használatakor az adatok másolásához a cél választja, az azonos méretű vagy a cél virtuális gép a legközelebbi lehetséges méretét.

3. Győződjön meg arról, hogy minden összetevő, a forrás hálózati elrendezés azonosított célként megadott erőforrás létrehozása. Ez azért fontos, ügyeljen arra, hogy, szélesítheti közzététele a célrégióban, a virtuális gépek rendelkeznek az a funkciók és szolgáltatások, amelyek a forrás volt.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és létrehoz egy virtuális hálózatot, amikor engedélyezi a replikációt a forrás virtuális gép, vagy is előre a hálózat létrehozása és hozzárendelése a felhasználói folyamatot a replikáció engedélyezése a virtuális Géphez. Azonban más erőforrások, az alábbiakban említett kell manuálisan hozza létre a célrégióban.

     Tekintse meg a következő dokumentumokat a leggyakrabban használt hálózati erőforrások megfelelő létrehozásához, a forrás virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Nyilvános IP-cím ](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Bármely más hálózati összetevők, tekintse meg a hálózati [dokumentációját.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Manuálisan [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha a konfiguráció tesztelése, a végső kivágási keresztül a célrégióban a végrehajtása előtt. Ez az üzemi minimális megérintése hoz létre, és javasolt.
    
## <a name="copy-data-to-the-target-region"></a>Másolja az adatokat a célrégió
Az alábbi lépéseket végigvezeti Önt az Azure Site Recovery használata az adatok másolása a célrégióban.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson a **erőforrás létrehozása** > **kezelőeszközök** > **Backup és Site Recovery**.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több olyan. előfizetés, válassza ki a megfelelő.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Kattintson a Recovery Services-tárolókra **áttekintése** > **ConsotoVMVault** > **+ replikálás**.
7. A **Forrás** beállításnál válassza az **Azure** értéket.
8. A **Forrás helye** értékeként válassza ki azt az Azure-régiót, ahol az adott virtuális gépek éppen futnak.
9. Válassza ki a Resource Manager-alapú üzemi modellt. Válassza ki a **forrás-előfizetés** és **forrásként szolgáló erőforráscsoportot**.
10. Kattintson az **OK** gombra a beállítások mentéséhez.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Az Azure virtuális gépek replikálásának engedélyezése, és indítsa el az adatok másolását.

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.

1. A következő lépésben. Válassza ki az áthelyezni kívánt virtuális Gépet. Ezután kattintson az **OK** gombra.
3. A **Beállítások** területen kattintson a **Vészhelyreállítás** elemre.
4. A **Vészhelyreállítás konfigurálása** > **Célrégió** részben válassza ki a célrégiót, amelybe a replikálást végezni kívánja.
5. A jelen oktatóanyag esetében fogadja el a többi alapértelmezett értéket.
6. Kattintson a **Replikáció engedélyezése** lehetőségre. Ekkor elindul a virtuális gép replikálásának engedélyezési feladata.

    ![replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Ugrás a tárolóhoz, a **beállítások** > **replikált elemek**, kattintson a virtuális gépet szeretne áthelyezni a célrégióban, kattintson a **+ feladatátvételi teszt** ikon.
2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás minden virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

3. Válassza ki a cél Azure-beli virtuális hálózatot, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése. 

> [!IMPORTANT]
> Azt javasoljuk, hogy a teszt sikertelen, és nem az éles hálózati környezetben a cél virtuális gépen, amelybe a virtuális gépek áthelyezése végül szeretne egy különálló Azure-beli Virtuálisgép-hálózatot használja. Ha engedélyezte a replikációt, amely volt állítsa be.

4. Az áthelyezés tesztelése indításához kattintson **OK**. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelése során létrejön a virtuális gép, kattintson a **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, jegyezze fel, és mentse a teszt megfigyeléseket.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hajtsa végre az áttérés a célrégióban, és erősítse meg.

1.  Ugrás a tárolóhoz, a **beállítások** > **replikált elemek**, kattintson a virtuális gépen, majd **feladatátvételi**.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. 
4. Ha a feladat befejeződött, ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
5. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ez befejezi az áttelepítési folyamat a célként megadott régióban. Várjon, amíg a véglegesítés feladat befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás a forrásrégióban elvetése 

1. Keresse meg a virtuális Gépet.  Kattintson a **tiltsa le a replikációt**.  Ezzel leállítja a virtuális gép számára az adatok másolásának folyamatát.  

> [!IMPORTANT]
> Fontos számolják fel ASR-replikáció elkerülése érdekében ez a lépés végrehajtásához.

Abban az esetben nem szándékozik a forrás-erőforrásokat használhat újból folytassa a következő lépések készletét.

1. Lépjen tovább a törli az összes releváns hálózati erőforrást, amely a 4. lépés részeként jelenik meg a forrásrégióban [a forrás virtuális gépek előkészítése](#prepare-the-source-vms) 
2. Törölje a megfelelő tárfiók a forrásrégióban.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure virtuális Gépen egy másik régióba való áthelyezése. Vész-helyreállítási mostantól konfigurálhatja az áthelyezett virtuális gép számára.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

