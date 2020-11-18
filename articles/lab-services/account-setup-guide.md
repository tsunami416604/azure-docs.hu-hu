---
title: Gyorsított labor-fiók telepítési útmutatója Azure Lab Services
description: Ez az útmutató segítséget nyújt a rendszergazdáknak az iskolán belüli használatra szolgáló labor-fiókok gyors létrehozásában.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: aa3e7b586546b3d87f5c6029b284eeb1402ed171
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659862"
---
# <a name="lab-account-setup-guide"></a>Lab-fiók telepítési útmutatója
A Azure Lab Services-környezet beállításához a rendszergazdáknak először be kell állítania egy **labor-fiókot** az Azure-előfizetésében. A labor-fiók a laborok tárolója, és csak néhány percet vesz igénybe.

Ez az útmutató három szakaszt tartalmaz:
-  Az első szakasz azokat az előfeltételeket ismerteti, amelyeket el kell végeznie a labor-fiók beállítása *előtt* .
-  A második szakasz útmutatást nyújt a labor-fiókok beállításainak megtervezéséhez.
-  A harmadik szakasz részletes útmutatást nyújt a labor-fiókok beállításához.

## <a name="prerequisites-for-setting-up-your-lab-account"></a>A labor-fiók beállításának előfeltételei
Ez a szakasz ismerteti azokat az előfeltételeket, amelyeket el kell végeznie, mielőtt beállít egy labor-fiókot.

### <a name="obtain-an-azure-subscription"></a>Azure-előfizetés beszerzése
Labor-fiók létrehozásához hozzá kell férnie egy Azure-előfizetéshez, amely be van állítva az iskolájában. Az iskolája egy vagy több előfizetéssel rendelkezhet. Az előfizetés segítségével kezelheti az Azure-erőforrások és-szolgáltatások, például a labor-fiókok számlázási és biztonsági szolgáltatásait.  Az Azure-előfizetéseket általában az informatikai részleg kezeli.  További információért olvassa el a következő témakört:
 - [Rendszergazdai útmutató – előfizetés](./administrator-guide.md#subscription)

### <a name="estimate-the-number-of-vms-and-vm-sizes-that-you-need"></a>Becsülje meg a virtuális gépek és a szükséges virtuálisgép-méretek számát
Meg kell becsülnie a virtuális gépek (VM-EK) számát és az iskolai igényeknek megfelelő virtuálisgép- [méreteket](./administrator-guide.md#vm-sizing) .  Olvassa el a következő blogbejegyzéset, amely útmutatást nyújt a labs\images. szerkezetének megadásához  Ez a blogbejegyzés a virtuális gépek számának és a szükséges virtuálisgép-méreteknek a meghatározásához is segítséget nyújt:
- [Áthelyezés fizikai laborból a Azure Lab Servicesba](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

Tekintse meg ezt a cikket is, amely további útmutatást nyújt a laborok struktúrájával kapcsolatban:
- [Rendszergazdai útmutató – labor](./administrator-guide.md)

### <a name="understand-subscription-vm-limits-and-regional-vm-capacity"></a>Az előfizetés virtuális gépek korlátai és a regionális virtuálisgép-kapacitás ismertetése
A virtuális gépek számának és a laborok virtuálisgép-méretének becslése után a következőket kell tennie:

- Győződjön meg arról, hogy az Azure-előfizetése kapacitásának korlátja lehetővé teszi a virtuális gépek számát és a laborban használni kívánt virtuálisgép-méretet.

- Hozza létre a labor-fiókját egy olyan régióban, amelyen elegendő virtuálisgép-kapacitás érhető el.

További információért olvassa el a következő blogbejegyzéset: [virtuális gépek előfizetési korlátai és regionális kapacitás](https://techcommunity.microsoft.com/t5/azure-lab-services/vm-subscription-limits-and-regional-capacity/ba-p/1845553).

### <a name="decide-how-many-lab-accounts-to-create"></a>Döntse el, hogy hány Lab-fiókot hoz létre

A gyors kezdéshez hozzon létre egyetlen Lab-fiókot a saját erőforráscsoporthoz.  Később szükség szerint további labor-fiókokat (és erőforráscsoportokat) is létrehozhat. Előfordulhat például, hogy végül egy labor-fiókkal és egy erőforráscsoport-erőforrással rendelkezik, így egyértelműen elkülönítheti a költségeket.  A következő cikkekből többet tudhat meg a labor-fiókokról, az erőforráscsoportokről és az elválasztó költségekről:
- [Rendszergazdai útmutató – erőforráscsoport](./administrator-guide.md#resource-group)
- [Rendszergazdai útmutató – Lab-fiók](./administrator-guide.md#lab-account) 
- [Cost Management Azure Lab Services](./cost-management-guide.md)

## <a name="planning-your-lab-accounts-settings"></a>A labor-fiók beállításainak megtervezése

A labor-fiók beállításainak megtervezéséhez vegye figyelembe az alábbi kérdéseket.

### <a name="who-should-be-owners-and-contributors-of-the-lab-account"></a>Kinek kell a labor-fiók tulajdonosai és közreműködői?

   Az iskola informatikai rendszergazdái általában a labor-fiókok tulajdonosai és közreműködői. Ők felelősek a labor-fiókban található összes laborhoz tartozó szabályzatok kezeléséért. A labor-fiókot létrehozó személy automatikusan tulajdonosa. További tulajdonosokat és közreműködőket adhat hozzá az előfizetéséhez társított Azure Active Directory (AD) bérlőhöz. A labor-fiók tulajdonosi és közreműködői szerepköreivel kapcsolatos további információkért olvassa el a következőt:
   -  [Rendszergazdai útmutató – identitás kezelése](./administrator-guide.md#manage-identity).

   [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]

   A labor felhasználóinak csak egyetlen listát kell látniuk a virtuális gépekről, amelyekhez a Azure Lab Serviceson belüli bérlők között férhet hozzá.

### <a name="who-will-be-allowed-to-create-labs"></a>Kik számára engedélyezett a laborok létrehozása?

   Dönthet úgy is, hogy az informatikai és oktatói tagok létrehozzák a laborokat. Amikor egy felhasználó létrehoz egy labort, a rendszer automatikusan a labor tulajdonosaként rendeli hozzá őket.  A Labs létrehozásához a felhasználókat (általában az előfizetéséhez társított Azure AD-bérlőből) hozzá kell rendelni a Lab-fiókon belül a labor létrehozói szerepkörhöz.  A labor létrehozói szerepkörről a következő témakörben olvashat bővebben:
   -  [Rendszergazdai útmutató – identitás kezelése](./administrator-guide.md#manage-identity)

### <a name="who-will-be-allowed-to-own-and-manage-labs"></a>Kik is jogosultak a laborok felügyeletére?

   AZT is megteheti, hogy a own\manage Labs-t és oktatókat is kiválaszthatja *anélkül* , hogy a laborokat létre tudja hozni.  Ebben az esetben az előfizetéséhez tartozó Azure AD-bérlőhöz tartozó felhasználókat a meglévő Labs tulajdonosának vagy közreműködőinek kell megadnia.  A labor tulajdonosi és közreműködői szerepköreivel kapcsolatos további információkért olvassa el a következőt:
   - [Rendszergazdai útmutató – identitás kezelése](./administrator-guide.md#manage-identity)

### <a name="do-you-want-to-save-images-that-can-be-shared-across-labs"></a>Szeretné menteni a laborok között megosztható képeket?
A megosztott képgyűjtemény olyan tárház, amely a képek mentésére és megosztására használható. Azon osztályok esetében, amelyeknek ugyanazt a képet kell megadniuk, a labor létrehozói létrehozzák a rendszerképet, majd exportálják azt a megosztott képgyűjteménybe.  A lemezképek megosztott képkatalógusba való exportálása után új Labs létrehozásához is használható.

Emellett előfordulhat, hogy létre szeretné hozni a lemezképeket a fizikai környezetében, majd importálja őket a megosztott rendszerkép-katalógusba.  A folyamattal kapcsolatos további információkért olvassa el a következő blogbejegyzést: 
- [Egyéni rendszerkép importálása a megosztott rendszerkép-gyűjteménybe](https://techcommunity.microsoft.com/t5/azure-lab-services/import-custom-image-to-shared-image-gallery/ba-p/1777353)

Ha úgy dönt, hogy megosztott képtárat szeretne használni, létre kell hoznia vagy csatolnia kell egy megosztott rendszerkép-tárat a labor-fiókjához. Azt is megteheti, hogy később elhalasztotta ezt a döntést, mivel egy labor-fiókhoz bármikor csatolható.  A megosztott képkatalógussal kapcsolatos további információkért olvassa el a következőt:
- [Rendszergazdai útmutató – megosztott rendszerkép-gyűjtemény](./administrator-guide.md#shared-image-gallery)
- [Rendszergazdai útmutató – megosztott rendszerkép-katalógus – díjszabás](./administrator-guide.md#shared-image-gallery-2)

### <a name="which-images-in-azure-marketplace-will-your-labs-use"></a>Milyen rendszerképeket használnak a laborok az Azure Marketplace-en?
Az Azure Marketplace több száz olyan képet biztosít, amely lehetővé teszi, hogy a labor létrehozói a rendszerképet használják a labor létrehozásához. Egyes képek tartalmazhatnak mindent, amit a labor már igényel. Más esetekben a rendszerképeket kiindulási pontként használhatja, majd a tesztkörnyezet létrehozója testre is szabhatja azt további alkalmazások vagy eszközök telepítésével.

Ha nem tudja, hogy mely rendszerképekre van szüksége, később is visszatérhet, hogy engedélyezze őket. Emellett a legjobb lehetőség, hogy megtekintse, mely képek érhetők el, először hozzon létre egy Lab-fiókot. Ez hozzáférést biztosít, így áttekintheti az elérhető rendszerképek és azok tartalmának listáját.  További információ a Piactéri lemezképekről:
- [A tesztkörnyezet-készítők számára elérhető Piactéri lemezképek meghatározása](./specify-marketplace-images.md)
  
### <a name="do-the-labs-vms-need-to-have-access-to-other-azure-or-on-premises-resources"></a>Kell-e a labor virtuális gépei hozzáférni más Azure-vagy helyszíni erőforrásokhoz?
Labor-fiók létrehozásakor a labor-fiókját egy virtuális hálózattal (VNet) is elvégezheti.  Ne feledje, hogy a VNet és a labor-fióknak ugyanabban a régióban kell lennie.  Annak eldöntéséhez, hogy szükség van-e egy VNet, vegye figyelembe a következő forgatókönyveket:

- **Hozzáférés egy licencelési kiszolgálóhoz**
  
   Az Azure Marketplace-rendszerképek használata esetén az operációs rendszer licencének díja a labor Services díjszabásában van beépítve. Nem kell azonban licenceket megadnia magának az operációs rendszernek. A telepített további szoftverek és alkalmazások esetében azonban szükség szerint meg kell adnia egy licencet.  Licencelési kiszolgáló elérése:
   - Dönthet úgy, hogy egy helyszíni licencelési kiszolgálóhoz csatlakozik.  A helyszíni licencelési kiszolgálóhoz való csatlakozáshoz további beállítások szükségesek.
   - Egy másik lehetőség, amely gyorsabban beállítható, egy Azure-beli virtuális gépen futtatott licenckiszolgáló létrehozása.  Az Azure-beli virtuális gép egy olyan virtuális hálózaton belül található, amelyet Ön a labor-fiókjához tartozó fiókkal.

- **Hozzáférés más helyi erőforrásokhoz, például fájlmegosztás vagy adatbázis**

   Hozzon létre egy VNet, amely hozzáférést biztosít a helyszíni erőforrásokhoz, általában egy helyek közötti virtuális hálózati átjáró használatával. Az ilyen típusú környezet beállítása további időt is igénybe vesz.

- **Hozzáférés más Azure-erőforrásokhoz, amelyek a VNet kívül találhatók**

   Ha olyan Azure-erőforrások elérésére van szüksége, amelyek *nem* biztonságosak a VNet belül, akkor a nyilvános interneten keresztül hozzáférhet ezekhez az erőforrásokhoz anélkül, hogy bármilyen műveletet kellene elvégeznie.

A virtuális hálózatokkal kapcsolatos további információkért olvassa el a következőt:
- [Architektúra alapjai – Virtual Network](./classroom-labs-fundamentals.md#virtual-network)
- [Kapcsolódás virtuális hálózathoz](./how-to-connect-peer-virtual-network.md)
- [Tesztkörnyezet létrehozása megosztott erőforrással Azure Lab Services](./how-to-create-a-lab-with-shared-resource.md)

## <a name="set-up-your-lab-account"></a>A labor-fiók beállítása

A tervezés befejezése után készen áll a labor-fiók beállítására.  Ugyanezek a lépések alkalmazhatók a laborok [Azure Lab Services csapatokkal való](./lab-services-within-teams-overview.md)beállítására.

1. **Hozza létre a labor-fiókját.** Az utasításokért tekintse meg a [labor-fiók létrehozásáról](./tutorial-setup-lab-account.md#create-a-lab-account) szóló oktatóanyagot.
   
    Az elnevezéssel kapcsolatos további útmutatásért tekintse meg a következő cikket:

   - [Az erőforrások elnevezési útmutatója](./administrator-guide.md#naming)

2. **Adja hozzá a felhasználókat a labor létrehozói szerepkörhöz.** Útmutatásért lásd: [felhasználók hozzáadása a labor Creator szerepkörhöz](./tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


3. **Kapcsolódjon egy társ virtuális hálózathoz.** Útmutatásért lásd: [a tesztkörnyezet hálózatának összekapcsolása egyenrangú virtuális hálózattal](./how-to-connect-peer-virtual-network.md).

   Előfordulhat, hogy [a labor virtuális gépek címtartomány konfigurálására](./how-to-configure-lab-accounts.md)vonatkozó utasításokra is szüksége lesz.

4. **Képek engedélyezése és áttekintése.** Útmutatásért lásd: [Azure Marketplace-rendszerképek engedélyezése labor-készítőknek](./specify-marketplace-images.md).

   Az egyes Azure Marketplace-képek tartalmának áttekintéséhez válassza ki a rendszerkép nevét. Az alábbi képernyőkép például az Ubuntu Data Science VM rendszerképének részleteit jeleníti meg:

   ![Az Azure Marketplace-rendszerképek áttekintése – képernyőkép](./media/setup-guide/review-marketplace-images.png)

   Ha egy megosztott képtárat csatolnak a labor-fiókjához, és engedélyezni szeretné az egyéni lemezképek megosztását a labor-készítők számára, hajtsa végre a hasonló lépéseket az alábbi képernyőképen látható módon:

   ![Képernyőkép – Egyéni rendszerképek engedélyezése egy megosztott rendszerkép-gyűjteményben](./media/setup-guide/enable-sig-custom-images.png)

## <a name="next-steps"></a>Következő lépések

A labor környezet beállításának következő lépései:

- [Tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labor telepítési útmutatója](setup-guide.md)