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
ms.openlocfilehash: 8fcc46487e7f7c2d075639f10a30cae9950ff31b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879581"
---
# <a name="lab-account-setup-guide"></a>Labor fiókbeállítási útmutatója

Első lépésként a rendszergazdák nak be kell állítaniuk egy tesztkörnyezet-fiókot az Azure-előfizetésen belül. A laborfiók egy tároló az osztályteremben laborok, és csak néhány percet vesz igénybe, hogy hozzanak létre.

## <a name="understand-your-schools-lab-account-requirements"></a>Ismerje meg az iskola laborfiókra vonatkozó követelményeit

Ha szeretné megtudni, hogyan konfigurálhatja a tesztkörnyezet-fiókot az iskola igényei alapján, vegye figyelembe ezeket a kérdéseket.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Hozzáférhetek egy Azure-előfizetéshez?

Laborfiók létrehozásához hozzáférésre van szüksége egy Azure-előfizetéshez, amely az iskolához van konfigurálva. Lehet, hogy az iskolának van egy vagy több előfizetése. Az előfizetés segítségével kezelheti az összes Azure-erőforrás és -szolgáltatás számlázását és biztonságát, beleértve a tesztkörnyezet-fiókokat is.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hány tesztkörnyezet-fiókot kell létrehozni?

A gyors kezdéshez hozzon létre egy tesztkörnyezet-fiókot, majd szükség szerint hozzon létre további tesztkörnyezet-fiókokat. Előfordulhat például, hogy részlegenként egy laborfiókkal rendelkezik.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kilegyen a laborfiók tulajdonosa és közreműködője?

A rendszergazdák általában a tulajdonosok és a közreműködők egy tesztkörnyezet-fiók. Ők felelősek a szabályzatok kezeléséért, amelyek a laborfiókban található összes laborra vonatkoznak. A tesztkörnyezet-fiókot létrehozó személy automatikusan tulajdonos. További tulajdonosokat és közreműködőket adhat hozzá, általában az Azure Active Directory (Azure AD) bérlőaz előfizetéshez társítva. Ez hasznos lehet egy tesztkörnyezet-fiók kezeléséhez a tulajdonos vagy a közreműködői szerepkör hozzárendelésével a laborfiók szintjén.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Ki hozhat létre és kezelhet laborokat?

Dönthet úgy, hogy a rendszergazdák és az oktatók laborokat hoznak létre és kezelnek. Ezek a felhasználók (általában az Azure AD-bérlő az előfizetéshez társított) vannak hozzárendelve a Lab Creator szerepkör a laborfiókban.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Szeretné megadni a laborkészítőknek a laborok között megosztható képek mentését?

A megosztott képgaléria egy tárház, amelyet képek mentésére és megosztására használhat. Ha több osztály, amelyek ugyanazt a képeket, labor alkotók hozhat létre a képet egyszer, és ossza meg a laborok között. A kezdéshez azonban nem feltétlenül van szükség megosztott képgalériára, mert később bármikor hozzáadhat egyet.

Ha igennel válaszolt erre a kérdésre, létre kell hoznia vagy csatolnia kell egy megosztott képgalériát a tesztkörnyezet-fiókhoz. Ha azt válaszoltad, hogy "nem tudom", elhalaszthatod ezt a döntést későbbre.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Mely képeket fogja használni az Azure Marketplace-en az osztálytermi laborok?

Az Azure Marketplace több száz lemezképet biztosít, amelyeket engedélyezhet, hogy a laborkészítők használhassák a lemezképet a labor létrehozásához. Egyes képek tartalmazhatnak mindent, amire egy labornak már szüksége van. Más esetekben előfordulhat, hogy egy lemezképet használ kiindulási pontként, majd a tesztkörnyezet létrehozója további alkalmazások vagy eszközök telepítésével testreszabhatja azt.

Ha nem tudja, hogy mely képeket kell használnia, később bármikor visszatérhet erre, hogy engedélyezze őket. Emellett a legjobb módja annak, hogy mely képek érhetők el, hogy először hozzon létre egy laborfiókot. Ez hozzáférést biztosít, így áttekintheti az elérhető képek listáját és azok tartalmát.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>A labor virtuális gépeinek más Azure- vagy helyszíni erőforrásokhoz kell hozzáférniük?

Laborfiók beállításakor is lehetősége van arra, hogy egy virtuális hálózattal társviszonyt létesítsen. Annak eldöntéséhez, hogy szüksége van-e erre, fontolja meg a következő kérdéseket:

- **Hozzáférést kell biztosítania egy licencelési kiszolgálóhoz?**
  
   Ha az Azure Marketplace-lemezképek használatát tervezi, az operációs rendszer licencének költsége a laborszolgáltatások díjszabásába kerül. Ezért nem kell licenceket biztosítania magának az operációs rendszernek. A telepített további szoftverek és alkalmazások esetében azonban szükség szerint licencet kell megadnia.

- **A tesztkörnyezet virtuális gépeinek más helyszíni erőforrásokhoz, például fájlmegosztáshoz vagy adatbázishoz kell hozzáférnie?**

   Virtuális hálózatot hoz létre, hogy hozzáférést biztosítson a helyszíni erőforrásokhoz, általában egy helyek közötti virtuális hálózati átjáró használatával. Ha nincs konfigurálva a virtuális hálózat, további időt kell befektetnie ehhez.

- **A labor virtuális gépeknek hozzá kell férnie más Azure-erőforrásokhoz, amelyek egy virtuális hálózaton belül találhatók?**

   Ha olyan Azure-erőforrásokhoz van szüksége, amelyek *nincsenek* biztosítva egy virtuális hálózaton belül, akkor ezeket az erőforrásokat a nyilvános interneten keresztül érheti el anélkül, hogy társviszony-létesítést végezne.

Ha egy vagy több kérdésre igennel válaszolt, akkor a tesztkörnyezet-fiókvirtuális hálózatra kell társviszonyt létesítenie. Ha azt válaszoltad, hogy "nem tudom", akkor elhalaszthatod ezt a döntést későbbre. A tesztkörnyezet-fiók létrehozása után bármikor kiválaszthatja, hogy egy virtuális hálózatra létesítsen-e.

## <a name="set-up-your-lab-account"></a>A tesztkörnyezet-fiók beállítása

Miután megértette a tesztkörnyezet-fiók követelményeit, készen áll a beállítására.

1. **Hozza létre a laborfiókot.** Az utasításokat a [laborfiók létrehozásáról szóló oktatóanyagban](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) találja.

   Laborfiók létrehozásakor hasznos lehet, ha megismerkedik az Azure-erőforrásokkal. További információkért tekintse át a következő cikkeket:

   - [Előfizetés](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Erőforráscsoport](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labor-fiók](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Tantermi labor](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Régió és hely kiválasztása](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Az erőforrások elnevezési útmutatója](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Felhasználók hozzáadása a tesztkörnyezet létrehozói szerepköréhez.** További információt a [Felhasználók hozzáadása a tesztkörnyezet létrehozói szerepköréhez](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role)című témakörben talál.

   A laborfiókokat és tesztkörnyezeteket kezelő felhasználókhoz rendelhető különböző szerepkörökkel kapcsolatos további információkért tekintse meg az [identitáskezeléséről szóló útmutatót.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity)

3. **Csatlakozás társvirtuális hálózathoz.** További információt [a tesztkörnyezet hálózatának társhálózati hálózattal való csatlakoztatása](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Előfordulhat, hogy [a tesztkörnyezet virtuális gépei címtartományának konfigurálására](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)vonatkozó utasításokat is meg kell adnia.

4. **Képek engedélyezése és áttekintése.** További információt az [Azure Marketplace-lemezképek engedélyezése laborkészítőkszámára.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

   Az egyes Azure Marketplace-lemezképek tartalmának áttekintéséhez válassza ki a lemezkép nevét. Például a következő képernyőkép az Ubuntu Data Science VM-lemezkép részleteit jeleníti meg:

   ![Képernyőkép az Azure Marketplace-képek áttekintéséről](../media/setup-guide/review-marketplace-images.png)

   Ha a tesztkörnyezet-fiókhoz csatolt egy megosztott képgalériát, és engedélyezni szeretné az egyéni képek megosztását a tesztkörnyezet alkotói számára, hajtsa végre az alábbi képernyőképen láthatólépésekhez hasonló lépéseket:

   ![Képernyőkép: Egyéni képek engedélyezése megosztott képgalériában](../media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>További lépések

- [Tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)

- [Tantermi laborbeállítási útmutató](setup-guide.md)
