---
title: Gyorsított laborfiók-beállítási útmutató az Azure Lab Services-hez
description: Ez az útmutató segít a rendszergazdáknak a tesztkörnyezet-fiók gyors beállításában az iskolájukban való használatra.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: 88b37ea4ff717689f05afbb41d33a56a8cbb2c22
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547621"
---
# <a name="lab-account-setup-guide"></a>Labor fiókbeállítási útmutatója

Az első lépés, amelyet a rendszergazdáknak el kell végezniük, hogy hozzon létre egy tesztkörnyezet-fiókot az Azure-előfizetésen belül.  A laborfiók egy tároló az osztályteremben laborok, és csak néhány percet vesz igénybe, hogy hozzanak létre.

## <a name="understand-your-schools-lab-account-requirements"></a>Ismerje meg az iskola laborfiókra vonatkozó követelményeit

Ha szeretné megtudni, hogyan konfigurálhatja a tesztkörnyezet-fiókot az iskola igényei alapján, vegye figyelembe a következő kérdéseket:

**Hozzáférhetek egy Azure-előfizetéshez?**

Laborfiók létrehozásához szüksége lesz egy Azure-előfizetés, amely az iskolához van konfigurálva; lehet, hogy az iskolában egy vagy több előfizetéssel rendelkezik.  Az előfizetés az összes azure-erőforrás\szolgáltatások, amelyek a benne használt, beleértve a tesztkörnyezet-fiókok számlázásának és biztonságának kezelésére szolgál.

**Hány tesztkörnyezet-fiókot kell létrehozni?**

A gyors kezdéshez ésszerű megközelítés egy egyetlen tesztkörnyezet-fiók létrehozása, majd később hozzon létre további Lab-fiókok szükség szerint.  Előfordulhat például, hogy részlegenként egy laborfiókkal rendelkezik.

**Kilegyen a laborfiók tulajdonosa és közreműködője?**

A rendszergazdák általában a tulajdonosok\közreműködők egy tesztkörnyezet-fiók, mivel ők felelősek a szabályzatok, amelyek a laborban található összes laborok kezelésére vonatkozik.  A tesztkörnyezet-fiókot létrehozó személy automatikusan tulajdonos.  További tulajdonosok\közreműködők hozzáadása (általában az előfizetéshez társított AAD-bérlőből) a tesztkörnyezet-fiók kezeléséhez a tulajdonos\Közreműködő szerepkör hozzárendelésével a laborfiók szintjén.

**Ki hozhat létre\Labs kezelése?**

Dönthet úgy, hogy a rendszergazdák és\vagy az oktatók laborokat hoznak létre és kezelnek; ezek a felhasználók (általában az előfizetéshez társított AAD-bérlőből) a laborkészítői szerepkörhöz vannak rendelve a laborfiókon belül.

**Szeretné megadni a laborkészítőknek a laborok között megosztható képek mentését?**

A megosztott képtár egy tárház, amelyet képek mentésére és megosztására használhat.  Ennek az az előnye, hogy ha több osztály, amelyek ugyanazt a képeket, labor alkotók hozhat létre a képet egyszer, és ossza meg a laborok között.  Azonban a kezdéshez teljesen ésszerű a megosztott képtár nélkül kezdeni; és bármikor választhatja azt, hogy később ad hozzá egyet.

Ha igennel válaszolt erre a kérdésre, akkor létre kell hoznia és\vagy csatolnia kell egy megosztott képgalériát a tesztkörnyezet-fiókhoz.  Ha azt válaszolta, hogy "nem tudom", akkor elhalaszthatja ezt a döntést későbbre.

Ha a tesztkörnyezet-fiókhoz megosztott képtár van csatolva

**Mely képeket fogja használni az Azure Piactéren az osztálytermi laborok?**

Az Azure Marketplace-en több száz lemezképet, amely et engedélyezhet, hogy a labor alkotói a képa labor létrehozásához.  Egyes képek tartalmazhatnak mindent, amire egy labornak már szüksége van.  Más esetekben egy lemezképet használhat kiindulási pontként, majd a labor készítője további alkalmazások, eszközök stb.

Ha nem tudja, hogy mely képeket kell használnia, később bármikor visszatérhet erre, hogy engedélyezze őket.  Is, a legjobb módja annak, hogy mely képek állnak rendelkezésre, hogy először hozzon létre egy labor fiókot - ez hozzáférést biztosít, így megtekintheti a rendelkezésre álló képek listáját és azok tartalmát.  További információ az alábbiakban található.
  
**A labor virtuális gépeinek (VM-eknek) más Azure- vagy előzetes erőforrásokhoz kell hozzáférniük?**

Laborfiók beállításakor is lehetősége van arra, hogy egy virtuális hálózat (VNet) társviszony-le.  Annak eldöntéséhez, hogy szüksége van-e virtuális hálózatra, vegye figyelembe a következő kérdéseket:

- **Hozzáférést kell biztosítania egy licencelési kiszolgálóhoz?**
  
   Ha az Azure Marketplace-lemezképek használatát tervezi, az operációsrendszer-licenc költsége a Lab Services díjszabásába kerül, így *nem* kell licenceket biztosítania magának az operációs rendszernek.  A telepített további szoftverek\alkalmazások esetében azonban szükség szerint licencet kell megadnia.

- **A laborvirtuális gépeknek hozzá kell férnie más helyszíni erőforrásokhoz, például fájlmegosztáshoz, adatbázishoz stb.?**

   Virtuális hálózatot kell létrehozni, hogy hozzáférést biztosítson a helyszíni erőforrásokhoz, általában egy helyek közötti virtuális hálózati átjáró használatával.  Ha nincs konfigurálva virtuális hálózat, ehhez további időt kell befektetni.  Ennek beállításáról az alábbiakban olvashat bővebben.

- **A labor virtuális gépeknek hozzá kell férnie más Azure-erőforrásokhoz, amelyek egy virtuális hálózaton belül találhatók?**

    Ha olyan Azure-erőforrásokhoz van szüksége, amelyek *nincsenek* biztosítva egy virtuális hálózaton belül, akkor ezeket az erőforrásokat a nyilvános interneten keresztül érheti el anélkül, hogy társviszony-létesítést végezne.

    Ha egy vagy több kérdésre igennel válaszolt, akkor a tesztkörnyezet-fiókkal egy virtuális hálózatra kell társviszonyt létesítenie.  Ha azt válaszolta, "Nem tudom", akkor elhalaszthatja ezt a döntést, amíg később, mert mindig választhat, hogy a társ egy virtuális hálózat létrehozása után a laborfiók.

## <a name="set-up-your-lab-account"></a>A tesztkörnyezet-fiók beállítása

Miután megértette a tesztkörnyezet-fiók követelményeit, készen áll a beállítására.  A laborfiók beállításához kövesse az ebben a szakaszban található hivatkozásokat:

1. **A tesztkörnyezet-fiók létrehozása**

   Az utasításokat a [laborfiók létrehozásáról szóló oktatóanyagban](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) találja.

   Laborfiók létrehozásakor hasznos lehet, ha megismerkedik az Azure-erőforrásokkal; az erőforrások létrehozásával kapcsolatos további információkért és útmutatásért tekintse meg az alábbi listát:

   - [Előfizetés](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Erőforráscsoport](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labor-fiók](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Tantermi labor](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Terület\Hely kiválasztása](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Az erőforrások elnevezési útmutatója](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Felhasználók hozzáadása a Lab Creator szerepkörhöz**

   Tekintse meg az oktatóanyagot a [felhasználók hozzáadása a Lab Creator szerepkör](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) utasításokat.

   A laborlaborfiókokat és -tesztkörnyezeteket kezelő felhasználókhoz rendelhető különböző szerepkörökkel kapcsolatos további információkért tekintse meg az [identitáskezeléséről szóló útmutatót.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)

3. **Csatlakozás társvirtuális hálózathoz**

   Tekintse meg az útmutató útmutató [a labor hálózatának egy társ virtuális hálózatgal való csatlakoztatása](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) utasításokat.

   Előfordulhat, hogy [a tesztkörnyezet ben található virtuális gépek címtartományának konfigurálására](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)vonatkozó utasításokra is szüksége lehet.

4. **Képek engedélyezése és áttekintése**

    Tekintse meg az útmutató [útmutatót a Marketplace-képek engedélyezése a labor alkotók](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images) utasításokat.

    Az egyes Piactér-képek tartalmának áttekintéséhez kattintson a kép nevére.  Például tekintse meg az alábbi képernyőképet, amely az Ubuntu Data Science VM-lemezkép részleteit mutatja:

    ![Marketplace-képek áttekintése](../media/setup-guide/review-marketplace-images.png)

    Ha a tesztkörnyezet-fiókhoz megosztott képtár van csatolva, és engedélyezni szeretné az egyéni képek megosztását a laborkészítők számára, akkor hasonló lépéseket kell végrehajtania, ahogy az az alábbi képernyőképen látható:

    ![Egyéni képek engedélyezése a Megosztott képtárban](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)

- [Tantermi laborbeállítási útmutató](setup-guide.md)
