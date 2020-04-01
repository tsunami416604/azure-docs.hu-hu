---
title: Az Azure-beli virtuális gépek áthelyezése egy másik régióba az Azure Site Recovery szolgáltatással
description: Az Azure Site Recovery használatával áthelyezi az Azure IaaS virtuális gépeket egyik Azure-régióból a másikba.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: dc37cb6fa05a2be56de7bf5536d7274190257d85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303919"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Az Azure-beli virtuális gépek áthelyezése egy másik Azure-régióba

Előfordulhat, hogy az Azure-infrastruktúrát szolgáltatásként (IaaS) a másikba szeretné áthelyezni a megbízhatóság, a rendelkezésre állás, a felügyelet vagy a cégirányítási javítása érdekében. Ez az oktatóanyag bemutatja, hogyan helyezheti át a virtuális gépeket egy másik régióba az Azure Site Recovery használatával. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrásvirtuális gépek előkészítése
> * A célterület előkészítése
> * Adatok másolása a célterületre
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtása
> * Az erőforrások elvetése a forrásrégióból


> [!IMPORTANT]
> Ez a cikk azt ismerteti, hogyan helyezheti át az Azure-beli virtuális gépeket az egyik régióból *a másikba.* Ha a cél az infrastruktúra rendelkezésre állásának javítása a virtuális gépek rendelkezésre állási zónákba való áthelyezésével, olvassa el az [Azure-beli virtuális gépek áthelyezése rendelkezésre állási zónákba című témakört.](move-azure-vms-avset-azone.md)

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy rendelkezik az Azure-beli virtuális gépek a forrás Azure-régióban, *amelyből*át szeretne helyezni.
- Ellenőrizze, hogy a [kiválasztott forrásrégió-célrégió kombináció támogatott-e,](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)és gondosan válassza ki a célrégiót.
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Tekintse át a [támogatási korlátokat és követelményeket](azure-to-azure-support-matrix.md) ismertető részt.
- Ellenőrizze a fiókengedélyeket. Ha most hozta létre az ingyenes Azure-fiókját, *ön* az előfizetés rendszergazdája. Ha nem Ön a rendszergazda, a rendszergazdával együttműködve szerezd meg a szükséges engedélyeket:
  -  A virtuális gép replikációjának engedélyezéséhez és az adatok másolásához a Site Recovery használatával, rendelkeznie kell engedélyekkel, hogy hozzon létre egy virtuális gép az Azure-erőforrásokban. A „Virtuális gépek közreműködője” beépített szerepkör rendelkezik ezekkel az engedélyekkel. Az engedélyekkel a következőket teheti:
        - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
        - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
        - Írás a kiválasztott tárfiókra.

  - A Webhely-helyreállítási műveletek kezeléséhez engedélyekre is szükség van. A Site Recovery Contributor szerepkör rendelkezik az Azure Recovery Services-tárolóban a Site Recovery Services-tárolóban a Site Recovery Services-műveletek kezeléséhez szükséges összes engedéllyel.

## <a name="prepare-the-source-vms"></a>A forrásvirtuális gépek előkészítése

1. Ellenőrizze, hogy az áthelyezni kívánt Azure-beli virtuális gépek rendelkeznek-e a legújabb főtanúsítványokkal. Ha nem, biztonsági korlátok miatt nem engedélyezheti az adatok másolását a célterületre.

    - Windows virtuális gépek esetén telepítse a legújabb Windows-frissítéseket, hogy az összes megbízható főtanúsítvány a számítógépen található. Leválasztott környezetben kövesse a windows Update és a tanúsítványfrissítési folyamatokat a szervezet számára.
    - Linuxos virtuális gépek esetén kövesse a Linux-forgalmazó útmutatását a legújabb megbízható főtanúsítványok és visszavont tanúsítványok listájának lekéréséhez.
2. Győződjön meg arról, hogy nem használ hitelesítési proxyt az áthelyezést kívánt virtuális gépek hálózati kapcsolatának szabályozására.
3. Ha az áthelyezni kívánt virtuális gép nem fér hozzá az internethez, és tűzfalproxyt használ a kimenő hozzáférés szabályozására, ellenőrizze a [követelményeket.](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)
4. Dokumentálja a forráshálózati elrendezést és az összes jelenleg használt erőforrást, beleértve (de nem kizárólagosan) a terheléselosztókat, a hálózati biztonsági csoportokat és a nyilvános IP-címeket ellenőrzés céljából.

## <a name="prepare-the-target-region"></a>A célterület előkészítése

1. Az Azure-előfizetésben ellenőrizze, hogy létrehozhat-e virtuális gépeket a vész-helyreállítási célrégióban. Szükség esetén lépjen kapcsolatba a támogatási szolgálattal a szükséges kvóta engedélyezéséhez.

2. Győződjön meg arról, hogy az előfizetés elegendő erőforrással rendelkezik a forrás virtuális gépek támogatásához. Ha a Site Recovery segítségével másolja az adatokat a cél, akkor kiválasztja az azonos méretű vagy a legközelebbi rendelkezésre álló mérete a cél virtuális gépek.

3. Győződjön meg arról, hogy a forráshálózati elrendezésben azonosított összes összetevőhöz létrehoz egy célerőforrást. Ez biztosítja, hogy a virtuális gépek minden funkcióval és funkcióval rendelkezik a célrégióban, amely a forrásrégióban rendelkezik.

   Az Azure Site Recovery automatikusan felderíti és létrehozza a virtuális hálózati és tárfiókot, amikor engedélyezi a replikációt a forrás virtuális gép. Ezeket az erőforrásokat előre is létrehozhatja, és az enable-replikációs lépés részeként hozzárendelheti őket a virtuális géphez. De manuálisan kell létrehoznia bármely más erőforrást a célrégióban. A következő dokumentumokból megtudhatja, hogy a leggyakrabban használt hálózati erőforrásokat a virtuális gép forráskonfigurációja alapján hozza létre:

   - [Network security groups (Hálózati biztonsági csoportok)](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer)
   - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
   Az egyéb hálózati összetevőkről az [Azure hálózati dokumentációjában](https://docs.microsoft.com/azure/?pivot=products&panel=network)olvashat. 

4. Ha az áthelyezés végrehajtása előtt szeretné tesztelni a konfigurációt, manuálisan [hozzon létre egy nem éles hálózatot](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban. A telepítés tesztelése minimális interferenciát okoz az éles környezetben, ezért azt javasoljuk.
    
## <a name="copy-data-to-the-target-region"></a>Adatok másolása a célterületre
A következő lépések az Azure Site Recovery használatával másolja az adatokat a célrégióba.

### <a name="create-the-vault-in-any-region-except-the-source"></a>A tároló létrehozása a forrás kivételével bármely régióban

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **helyreállítási szolgáltatásokba.**
2. Válassza **az Erőforráskezelő** > **eszközök** > **biztonsági másolatkészítése és a Webhely-helyreállítás lehetőséget.**
3. A **Name (Név) mezőbe**adja meg a **ContosoVMVault**rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók ellenőrzéséhez olvassa el [az Azure Site Recovery díjszabási részletei című témakört.](https://azure.microsoft.com/pricing/details/site-recovery/)
6. A Helyreállítási szolgáltatások tárolóihoz válassza **az Áttekintés** > **ConsotoVMVault** > **+Replicate**lehetőséget.
7. Forrás **esetén**válassza az **Azure**lehetőséget.
8. A **forrás helyéhez**válassza ki azt a forrásazure-régiót, ahol a virtuális gépek jelenleg futnak.
9. Válassza ki az Azure Resource Manager telepítési modelljét. Ezután válassza ki a **Forrás-** és **Forrás erőforráscsoportot.**
10. A beállítások mentéséhez válassza az **OK gombot.**

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Engedélyezze az Azure virtuális gépeit replikációs célokra, és kezdje meg az adatok másolását

A Site Recovery lekéri az előfizetéshez és az erőforráscsoporthoz társított virtuális gépek listáját.

1. Jelölje ki az áthelyezni kívánt virtuális gép kiválasztását, majd kattintson **az OK gombra.**
2. A **Beállítások csoportban**válassza a **Vészhelyreállítás**lehetőséget.
3. A **Vész-helyreállítási** > **célterület**konfigurálása területen válassza ki azt a célterületet, amelybe replikál.
4. Válassza ki, hogy az alapértelmezett célerőforrásokat vagy az előre létrehozott erőforrásokat használja.Choose to use the default target resources or that you pre-created.
5. Válassza **a Replikáció engedélyezése** lehetőséget a feladat elindításához.

   ![A replikáció engedélyezése](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Menj a páncélterembe. A**Replikált elemek** **beállításai** > ban válassza ki azt a virtuális gépet, amelyet át szeretne helyezni a célterületre. Ezután válassza a **Feladatátvétel tesztelése**lehetőséget.
2. A **Feladatátvétel tesztben**válassza ki a feladatátvételhez használandó helyreállítási pontot:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Nincs idő az adatok feldolgozásával, így ez a beállítás alacsony helyreállítási időt (RTO) biztosít.
   - **Legújabb alkalmazáskonzisztens:** A legújabb alkalmazáskonzisztens helyreállítási pontnak az összes virtuális gép átfelel. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki a cél Azure virtuális hálózat, amelyhez át szeretné helyezni az Azure virtuális gépek a konfiguráció teszteléséhez.

   > [!IMPORTANT]
   > Azt javasoljuk, hogy egy külön Azure virtuálisgép-hálózat a teszt feladatátvétel, nem az éles hálózat a célrégióban.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson az **OK gombra.** A folyamat nyomon követéséhez válassza a virtuális gép a **tulajdonságok megnyitásához.** Vagy válassza ki a **teszt feladatátvételi** feladat a tárolóban. Ezután válassza a **Beállítások** > **a** > **webhely-helyreállítási feladatokat.**
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. A teszteléshez létrehozott virtuális gép törléséhez válassza a Replikált elem **karbantartása teszt feladatátvétel** lehetőséget. A **Notes**alkalmazásban jegyezze fel és mentse a teszttel kapcsolatos megfigyeléseket.

## <a name="perform-the-move-and-confirm"></a>Az áthelyezés végrehajtása és megerősítése

1. Nyissa meg a tárolót a**Replikált elemek** **beállításai** > között, jelölje ki a virtuális gépet, és válassza a **Feladatátvétel**lehetőséget.
1. A **Feladatátvétel**csoportban válassza a **Legújabb**lehetőséget. 
2. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megpróbálja leállítani a forrás virtuális gép, mielőtt a feladatátvétel. A feladatátvétel azonban akkor is folytatódik, ha a leállítás sikertelen. A feladatátvételi folyamatot a Feladatok lapon **követheti.**
3. A feladat befejezése után ellenőrizze, hogy a virtuális gép a várt módon jelenik-e meg a cél Azure-régióban.
4. A **Replikált elemek alkalmazásban**kattintson a jobb gombbal a virtuális gépre, és válassza a **Véglegesítés parancsot.** Ezzel vége a költözésnek. Várjon, amíg a véglegesítési feladat befejeződik.

## <a name="discard-the-resources-from-the-source-region"></a>Az erőforrások elvetése a forrásrégióból

- Nyissa meg a virtuális gép, és válassza **a Replikáció letiltása**lehetőséget. Ez leállítja a virtuális gép adatainak másolását.

  > [!IMPORTANT]
  > Hajtsa végre ezt a lépést, hogy elkerülje a site recovery replikációs díjának felszámítását az áthelyezés után.

Ha nem tervezi a forráserőforrások újbóli felhasználását, kövesse az alábbi lépéseket:

1. Törölje az összes vonatkozó hálózati erőforrást a forrásrégióban, amely a [Forrás virtuális gépek előkészítése](#prepare-the-source-vms)4.
2. Törölje a megfelelő tárfiókot a forrásrégióban.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan helyezheti át az Azure-beli virtuális gépeket egy másik Azure-régióba. Most konfigurálhatja a vész-helyreállítási az ilyen virtuális gépek.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)

