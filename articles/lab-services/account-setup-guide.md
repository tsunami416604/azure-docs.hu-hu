---
title: Gyorsított labor-fiók telepítési útmutatója Azure Lab Services
description: Ez az útmutató segítséget nyújt a rendszergazdáknak az iskolán belüli használatra szolgáló labor-fiókok gyors létrehozásában.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c186560b27ebcb543a23785dc5fbc556614f64b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445423"
---
# <a name="lab-account-setup-guide"></a>Lab-fiók telepítési útmutatója

Első lépésként a rendszergazdáknak be kell állítania egy Lab-fiókot az Azure-előfizetésében. A labor-fiók a tantermi laborok tárolója, és a beállítás csak néhány percet vesz igénybe.

## <a name="understand-your-schools-lab-account-requirements"></a>Az iskolai labor-fiókra vonatkozó követelmények megismerése

Ha meg szeretné tudni, hogyan konfigurálhatja a labor-fiókját az iskolai igények alapján, érdemes megfontolnia ezeket a kérdéseket.

### <a name="do-i-have-access-to-an-azure-subscription"></a>Van hozzáférése egy Azure-előfizetéshez?

Labor-fiók létrehozásához hozzá kell férnie az iskolához konfigurált Azure-előfizetéshez. Lehet, hogy az iskolája egy vagy több előfizetéssel rendelkezik. Az előfizetés segítségével kezelheti az Azure-erőforrások és-szolgáltatások, például a labor-fiókok számlázási és biztonsági szolgáltatásait.

### <a name="how-many-lab-accounts-need-to-be-created"></a>Hány labor-fiókot kell létrehozni?

A gyors kezdéshez hozzon létre egy Lab-fiókot, majd később szükség szerint hozzon létre további labor-fiókokat. Előfordulhat például, hogy végül egy labor-fiókkal rendelkezik egy részlegen.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kinek kell a labor-fiók tulajdonosai és közreműködői?

A rendszergazdák általában a labor-fiókok tulajdonosai és közreműködői. Ők felelősek a labor-fiókban található összes laborhoz tartozó szabályzatok kezeléséért. A labor-fiókot létrehozó személy automatikusan tulajdonosa. További tulajdonosokat és közreműködőket adhat hozzá, általában az előfizetéséhez társított Azure Active Directory (Azure AD) bérlőtől. Ez akkor lehet hasznos, ha a labor fiók szintjén a tulajdonos vagy közreműködő szerepkör hozzárendelésével segíti a tesztkörnyezet felügyeletét.

### <a name="who-will-be-allowed-to-create-and-manage-labs"></a>Kik számára engedélyezett a laborok létrehozása és kezelése?

Dönthet úgy is, hogy a rendszergazdák és az oktatók tagjai létrehozzák és kezelik a laborokat. Ezek a felhasználók (általában az előfizetéséhez társított Azure AD-bérlőből) a labor-fiókon belül vannak hozzárendelve a labor létrehozói szerepkörhöz.

### <a name="do-you-want-to-give-lab-creators-the-ability-to-save-images-that-can-be-shared-across-labs"></a>Szeretné, hogy a labor-készítők képesek legyenek menteni a laboratóriumokban megosztható képeket?

A megosztott képgyűjtemény olyan tárház, amely a képek mentésére és megosztására használható. Ha több olyan osztálya van, amelynek ugyanazokat a képeket kell megadnia, akkor a labor-létrehozók egyszer is létrehozhatják a lemezképet, és megoszthatják azt a A kezdéshez azonban nincs szükség megosztott képkatalógusra, mert később bármikor hozzáadhat egyet.

Ha erre a kérdésre igennel válaszolt, létre kell hoznia vagy csatolnia kell egy megosztott rendszerkép-tárat a labor-fiókjához. Ha azt válaszolta, hogy "nem tudom," elhalasztotta ezt a döntést a későbbiekben.

### <a name="which-images-in-azure-marketplace-will-your-classroom-labs-use"></a>Milyen rendszerképeket használnak az Azure Marketplace-en a tanterem Labs?

Az Azure Marketplace több száz olyan képet biztosít, amely lehetővé teszi, hogy a labor létrehozói a rendszerképet használják a labor létrehozásához. Egyes képek tartalmazhatnak mindent, amit a labor már igényel. Más esetekben a rendszerképeket kiindulási pontként használhatja, majd a tesztkörnyezet létrehozója testre is szabhatja azt további alkalmazások vagy eszközök telepítésével.

Ha nem tudja, hogy mely rendszerképeket kell használnia, később bármikor visszatérhet, hogy engedélyezze őket. Emellett a legjobb lehetőség, hogy megtekintse, mely képek érhetők el, először hozzon létre egy Lab-fiókot. Ez hozzáférést biztosít, így áttekintheti az elérhető rendszerképek és azok tartalmának listáját.
  
### <a name="do-the-labs-virtual-machines-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Szükség van-e a labor virtuális gépei más Azure-vagy helyszíni erőforrásokhoz való hozzáférésre?

Labor-fiók beállításakor lehetősége van arra is, hogy a virtuális hálózattal is egyenrangú legyen. Ha szeretné eldönteni, hogy szüksége van-e erre, vegye figyelembe a következő kérdéseket:

- **Hozzáférést kell biztosítania a licencelési kiszolgálóhoz?**
  
   Ha azt tervezi, hogy az Azure Marketplace-rendszerképeket használja, az operációs rendszer licencének díja a labor Services díjszabásában található. Ezért nem szükséges licenceket megadnia magának az operációs rendszernek. A telepített további szoftverek és alkalmazások esetében azonban szükség szerint meg kell adnia egy licencet.

- **Szükség van-e a tesztkörnyezet virtuális gépei más helyszíni erőforrásokra, például fájlmegosztás vagy adatbázis elérésére?**

   Hozzon létre egy virtuális hálózatot, amely hozzáférést biztosít a helyszíni erőforrásokhoz, általában egy helyek közötti virtuális hálózati átjáró használatával. Ha nem rendelkezik konfigurált virtuális hálózattal, további időt kell megadnia ehhez.

- **A labor virtuális gépeknek hozzá kell férniük más Azure-erőforrásokhoz, amelyek a virtuális hálózaton belül találhatók?**

   Ha olyan Azure-erőforrások elérésére van szüksége, amelyek *nem* biztonságosak a virtuális hálózaton belül, akkor a nyilvános interneten keresztül hozzáférhet ezekhez az erőforrásokhoz anélkül, hogy bármilyen műveletet végrehajtaná.

Ha az "igen" értéket adta meg egy vagy több kérdésnek, akkor a tesztkörnyezet fiókját egy virtuális hálózathoz kell összeállítania. Ha a "nem tudom," lehetőséget választotta, akkor később is elhalaszthatja ezt a döntést. A labor-fiók létrehozása után mindig dönthet úgy, hogy a virtuális hálózatot is létrehozta.

## <a name="set-up-your-lab-account"></a>A labor-fiók beállítása

Miután megértette a labor-fiókjára vonatkozó követelményeket, készen áll a beállításra.

1. **Hozza létre a labor-fiókját.** Az utasításokért tekintse meg a [labor-fiók létrehozásáról](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#create-a-lab-account) szóló oktatóanyagot.

   Labor-fiók létrehozásakor hasznos lehet megtekinteni az érintett Azure-erőforrásokat. További információkért tekintse át a következő cikkeket:

   - [Előfizetés](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#subscription)
   - [Erőforráscsoport](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#resource-group)
   - [Labor-fiók](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#lab-account)
   - [Tantermi labor](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#classroom-lab)
   - [Régió és hely kiválasztása](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)
   - [Az erőforrások elnevezési útmutatója](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#naming)

2. **Adja hozzá a felhasználókat a labor létrehozói szerepkörhöz.** Útmutatásért lásd: [felhasználók hozzáadása a labor Creator szerepkörhöz](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role).

   Továbbá a labor-fiókokat és a laborokat kezelő felhasználókhoz hozzárendelt különböző szerepkörökkel kapcsolatos további információkért tekintse meg az [identitás kezelése című útmutatót](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#manage-identity).

3. **Kapcsolódjon egy társ virtuális hálózathoz.** Útmutatásért lásd: [a tesztkörnyezet hálózatának összekapcsolása egyenrangú virtuális hálózattal](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network).

   Előfordulhat, hogy [a labor virtuális gépek címtartomány konfigurálására](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-lab-accounts#specify-an-address-range-for-vms-in-the-lab)vonatkozó utasításokra is szüksége lesz.

4. **Képek engedélyezése és áttekintése.** Útmutatásért lásd: [Azure Marketplace-rendszerképek engedélyezése labor-készítőknek](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images).

   Az egyes Azure Marketplace-képek tartalmának áttekintéséhez válassza ki a rendszerkép nevét. Az alábbi képernyőkép például az Ubuntu Data Science VM rendszerképének részleteit jeleníti meg:

   ![Az Azure Marketplace-rendszerképek áttekintése – képernyőkép](./media/setup-guide/review-marketplace-images.png)

   Ha rendelkezik a labor-fiókhoz csatolt megosztott képkatalógussal, és engedélyezni szeretné az egyéni lemezképek megosztását a labor-készítők számára, hajtsa végre a következő képernyőképen láthatóhoz hasonló lépéseket:

   ![Képernyőkép – Egyéni rendszerképek engedélyezése egy megosztott rendszerkép-gyűjteményben](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>További lépések

- [Tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)

- [A tanterem Lab telepítési útmutatója](setup-guide.md)
