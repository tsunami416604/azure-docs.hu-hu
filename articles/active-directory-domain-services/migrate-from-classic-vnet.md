---
title: Az Azure AD tartományi szolgáltatások áttelepítése klasszikus virtuális hálózatról | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti át a meglévő Azure AD tartományi szolgáltatások által felügyelt tartományi példányt a klasszikus virtuális hálózati modellből egy Erőforrás-kezelő alapú virtuális hálózatba.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 6acf9301367ae2c6947f6935c43f420d3d7cac65
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655016"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Az Azure AD tartományi szolgáltatások áttelepítése a klasszikus virtuális hálózati modellről az Erőforrás-kezelőre

Az Azure Active Directory tartományi szolgáltatások (AD DS) támogatja az egyszeri lépés az ügyfelek jelenleg a klasszikus virtuális hálózati modell t használja az Erőforrás-kezelő virtuális hálózati modell. Az Azure AD DS által felügyelt tartományok, amelyek az Erőforrás-kezelő telepítési modell további funkciókat, például részletes jelszóházirendet, naplónaplókat és fiókzároláselleni védelmet biztosítanak.

Ez a cikk ismerteti az előnyeit és szempontjait az áttelepítés, majd a szükséges lépéseket egy meglévő Azure AD DS-példány sikeres áttelepítése.

> [!NOTE]
> 2017-ben az Azure AD tartományi szolgáltatások elérhetővé vált az Azure Resource Manager-hálózatban való üzemeltetéshez. Azóta egy biztonságosabb szolgáltatást tudtunk készíteni az Azure Resource Manager modern képességeinek használatával. Mivel az Azure Resource Manager központi telepítései teljes mértékben helyettesítik a klasszikus üzemelő példányokat, az Azure AD DS klasszikus virtuális hálózati telepítései 2023.
>
> További információkért lásd a [hivatalos epreprecation](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="overview-of-the-migration-process"></a>Az áttelepítési folyamat áttekintése

Az áttelepítési folyamat egy klasszikus virtuális hálózatban futó meglévő Azure AD DS-példányt vesz igénybe, és áthelyezi azt egy meglévő Resource Manager virtuális hálózatba. Az áttelepítés a PowerShell használatával történik, és a végrehajtás két fő szakaszával rendelkezik - *az előkészítés* és az *áttelepítés.*

![Az Azure AD DS áttelepítési folyamatának áttekintése](media/migrate-from-classic-vnet/migration-overview.png)

Az *előkészítési* szakaszban az Azure AD DS biztonsági másolatot készít a tartományról, hogy a felügyelt tartománnyal szinkronizált felhasználók, csoportok és jelszavak legfrissebb pillanatképét kapja. A szinkronizálás ezután le van tiltva, és az Azure AD DS felügyelt tartományt tartalmazó felhőszolgáltatás törlődik. Az előkészítési szakaszban az Azure AD DS felügyelt tartomány nem tudja hitelesíteni a felhasználókat.

![Az Azure AD DS áttelepítésének előkészítése](media/migrate-from-classic-vnet/migration-preparation.png)

Az *áttelepítési* szakaszban a klasszikus Azure AD DS felügyelt tartomány tartományvezérlőinek alapjául szolgáló virtuális lemezeket a rendszer másolja a virtuális gépek létrehozásához az Erőforrás-kezelő telepítési modell használatával. Az Azure AD DS felügyelt tartomány ezután újra, amely tartalmazza az LDAPS és a DNS-konfiguráció. Az Azure AD-vel való szinkronizálás újraindul, és az LDAP-tanúsítványok visszaállnak. Nincs szükség arra, hogy egyetlen gépet is újra csatlakoztasson egy Azure AD DS által felügyelt tartományhoz – továbbra is csatlakoznak a felügyelt tartományhoz, és módosítások nélkül futnak.

![Az Azure AD DS áttelepítése](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>Migrációs előnyök

Ha egy Azure AD DS felügyelt tartományt helyez át ezzel az áttelepítési folyamattal, elkerüli, hogy újra csatlakozzon a gépekhez a felügyelt tartományhoz, vagy törölje az Azure AD DS-példányt, és hozzon létre egyet a semmiből. A virtuális gépek továbbra is csatlakoznak az Azure AD DS által felügyelt tartományhoz az áttelepítési folyamat végén.

Az áttelepítés után az Azure AD DS számos olyan szolgáltatást kínál, amelyek csak az Erőforrás-kezelő virtuális hálózatait használó tartományok számára érhetők el, például:

* Részletes jelszóházirend-támogatás.
* AD-fiók zárolásának védelme.
* E-mail értesítések az Azure AD DS felügyelt tartományban riasztások.
* Naplók naplózása az Azure Monitor használatával.
* Azure Files integráció
* HD Insights integráció

Az Azure AD DS által felügyelt tartományok, amelyek egy Resource Manager virtuális hálózat segítségével naprakész a legújabb új funkciókkal. Az Azure AD DS klasszikus virtuális hálózatok használatával nyújtott támogatást a jövőben el kell akadni.

## <a name="example-scenarios-for-migration"></a>Példa áttelepítési forgatókönyvekre

Néhány gyakori forgatókönyv az Azure AD DS felügyelt tartomány áttelepítéséhez a következő példák.

> [!NOTE]
> Ne konvertálja a klasszikus virtuális hálózatot, amíg nem erősítette meg a sikeres áttelepítést. A virtuális hálózat konvertálása eltávolítja a lehetőséget, hogy állítsa vissza, vagy állítsa vissza az Azure AD DS felügyelt tartomány, ha bármilyen probléma merül fel az áttelepítés i és ellenőrzési szakaszok ban.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Az Azure AD DS áttelepítése meglévő Erőforrás-kezelő virtuális hálózatra (ajánlott)

Gyakori forgatókönyv, hogy már áthelyezte a többi klasszikus erőforrást egy Erőforrás-kezelő telepítési modelljébe és virtuális hálózatába. A társviszony-létesítésezés ezután az Erőforrás-kezelő virtuális hálózatból a klasszikus virtuális hálózathoz kapcsolódik, amely továbbra is futtatja az Azure AD DS-t. Ez a megközelítés lehetővé teszi, hogy az Erőforrás-kezelő alkalmazások és -szolgáltatások az Azure AD DS felügyelt tartomány hitelesítési és felügyeleti funkcióját használják a klasszikus virtuális hálózatban. Az áttelepítés után az összes erőforrás az Erőforrás-kezelő telepítési modelljével és a virtuális hálózattal fut.

![Az Azure AD DS áttelepítése meglévő Erőforrás-kezelő virtuális hálózatra](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

Ebben a példában az áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részeket tartalmazzák:

1. Távolítsa el a klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárókat vagy virtuális hálózati társviszony-létesítést.
1. Telepítse át az Azure AD DS felügyelt tartományt a cikkben ismertetett lépések használatával.
1. Tesztelje és erősítse meg a sikeres áttelepítést, majd törölje a klasszikus virtuális hálózatot.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Több erőforrás áttelepítése, beleértve az Azure AD DS-t is

Ebben a példában az Azure AD DS-t és más kapcsolódó erőforrásokat a klasszikus üzembe helyezési modellből az Erőforrás-kezelő üzembe helyezési modelljébe telepíti át. Ha egyes erőforrások továbbra is futnak a klasszikus virtuális hálózat mellett az Azure AD DS felügyelt tartományban, akkor az összes részesülhetnek a Resource Manager telepítési modell.

![Több erőforrás áttelepítése az Erőforrás-kezelő telepítési modelljébe](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

Ebben a példában az áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részeket tartalmazzák:

1. Távolítsa el a klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárókat vagy virtuális hálózati társviszony-létesítést.
1. Telepítse át az Azure AD DS felügyelt tartományt a cikkben ismertetett lépések használatával.
1. Virtuális hálózati társviszony-létesítés beállítása a klasszikus virtuális hálózat és az Erőforrás-kezelő hálózat között.
1. Tesztelje és erősítse meg a sikeres áttelepítést.
1. [További klasszikus erőforrások, például virtuális gépek áthelyezése.][migrate-iaas]

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Az Azure AD DS áttelepítése, de más erőforrások megtartása a klasszikus virtuális hálózaton

Ebben a példában forgatókönyv, akkor a minimális állásidő egy munkamenetben. Az Azure AD DS-t csak egy Erőforrás-kezelő virtuális hálózatba telepítheti át, és a meglévő erőforrásokat a klasszikus telepítési modellen és a virtuális hálózaton tartja meg. A következő karbantartási időszakban a klasszikus üzembe helyezési modellből és a virtuális hálózatból igény szerint áttelepítheti a további erőforrásokat.

![Csak az Azure AD DS áttelepítése az Erőforrás-kezelő telepítési modelljére](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

Ebben a példában az áttelepítési forgatókönyvben szereplő magas szintű lépések a következő részeket tartalmazzák:

1. Távolítsa el a klasszikus virtuális hálózaton konfigurált meglévő VPN-átjárókat vagy virtuális hálózati társviszony-létesítést.
1. Telepítse át az Azure AD DS felügyelt tartományt a cikkben ismertetett lépések használatával.
1. Virtuális hálózati társviszony-létesítés beállítása a klasszikus virtuális hálózat és az új Erőforrás-kezelő virtuális hálózat között.
1. Később szükség szerint [telepítse át a további erőforrásokat][migrate-iaas] a klasszikus virtuális hálózatból.

## <a name="before-you-begin"></a>Előkészületek

Az Azure AD DS felügyelt tartományelőkészítése és áttelepítése során a hitelesítési és felügyeleti szolgáltatások rendelkezésre állásával kapcsolatban néhány szempontot figyelembe kell venni. Az Azure AD DS felügyelt tartomány a migrálás során egy ideig nem érhető el. Az Azure AD DS-re támaszkodó alkalmazások és szolgáltatások az áttelepítés során állásidőt tapasztalnak.

> [!IMPORTANT]
> Az áttelepítési folyamat megkezdése előtt olvassa el az áttelepítési cikk et és útmutatást. Az áttelepítési folyamat hatással van az Azure AD DS tartományvezérlők rendelkezésre állására. A felhasználók, a szolgáltatások és az alkalmazások nem hitelesíthetők a felügyelt tartománnyal szemben az áttelepítési folyamat során.

### <a name="ip-addresses"></a>IP-címek

Az Azure AD DS által felügyelt tartomány tartományvezérlő IP-címei az áttelepítés után változnak. Ez a módosítás magában foglalja a biztonságos LDAP-végpont nyilvános IP-címét. Az új IP-címek az Erőforrás-kezelő virtuális hálózat új alhálózatának címtartományán belül vannak.

Visszaállítás esetén az IP-címek a visszaállítás után változhatnak.

Az Azure AD DS általában az első két elérhető IP-címet használja a címtartományban, de ez nem garantált. Jelenleg nem adhatja meg az áttelepítés után használandó IP-címeket.

### <a name="downtime"></a>Állásidő

Az áttelepítési folyamat során a tartományvezérlők egy ideig offline állapotban vannak. A tartományvezérlők nem érhetők el, amíg az Azure AD DS átkerül az Erőforrás-kezelő telepítési modelljébe és a virtuális hálózatba. Átlagosan az állásidő körülbelül 1-3 óra. Ez az időszak a tartományvezérlők offline állapotba kerülésétől addig a pillanatig van, amíg az első tartományvezérlő újra online állapotba kerül. Ez az átlag nem tartalmazza a második tartományvezérlő replikálásának idejét, sem azt, hogy milyen ideig tarthat további erőforrások áttelepítése az Erőforrás-kezelő telepítési modelljébe.

### <a name="account-lockout"></a>Fiók zárolása

A klasszikus virtuális hálózatokon futó Azure AD DS által kezelt tartományok nem rendelkeznek AD-fiókzárolási szabályzatokkal. Ha a virtuális gépek ki vannak téve az internetnek, a támadók jelszó-spray módszerekkel találgatásos módon kényszeríthetik be a fiókokba. Nincs fiókzárolási házirend a kísérletek leállításához. Az Azure AD DS által felügyelt tartományok, amelyek a Resource Manager telepítési modell és a virtuális hálózatok, AD-fiók zárolási szabályzatok elleni védelem ezeket a jelszó-spray támadások.

Alapértelmezés szerint 5 rossz jelszó kísérlet 2 perc alatt zárja ki a fiókot 30 percig.

A zárolt fiók nem használható a bejelentkezéshez, ami zavarhatja az Azure AD DS felügyelt tartomány vagy a fiók által kezelt alkalmazások kezelésének lehetőségét. Az Azure AD DS felügyelt tartomány ának áttelepítése után a fiókok megtapasztalhatják, hogy milyen érzés, mint egy állandó zárolás miatt ismételt sikertelen bejelentkezési kísérletek. Az áttelepítés után két gyakori forgatókönyv a következő:

* Lejárt jelszót használó szolgáltatásfiók.
    * A szolgáltatásfiók ismételten megpróbál bejelentkezni egy lejárt jelszóval, amely zárolja a fiókot. A probléma megoldásához keresse meg az alkalmazást vagy a virtuális gép lejárt hitelesítő adatokkal, és frissítse a jelszót.
* Egy rosszindulatú entitás találgatásos kísérleteket használ a fiókokba való bejelentkezéshez.
    * Amikor a virtuális gépek ki vannak téve az internetnek, a támadók gyakran próbálnak általános felhasználónév- és jelszókombinációkat, amikor aláírásra kísérelnek. Ezek az ismétlődő sikertelen bejelentkezési kísérletek zárolhatják a fiókokat. Nem ajánlott általános nevű rendszergazdai fiókokat használni, például *rendszergazdai* vagy *rendszergazdai*neveket, például a rendszergazdai fiókok kizártságának minimalizálása érdekében.
    * Az internetnek kitett virtuális gépek számának minimalizálása. Az [Azure Bastion][azure-bastion] segítségével biztonságosan csatlakozhat a virtuális gépekhez az Azure Portalon keresztül.

Ha azt gyanítja, hogy egyes fiókok az áttelepítés után zárolhatók, a végső áttelepítési lépések ismertetik, hogyan engedélyezheti a naplózást, illetve hogyan módosíthatja a részletes jelszóházirend-beállításokat.

### <a name="roll-back-and-restore"></a>Visszaállítás és visszaállítás

Ha az áttelepítés sikertelen, van folyamat az Azure AD DS felügyelt tartomány visszaállítása vagy visszaállítása. A visszaállítás egy önkiszolgáló lehetőség, amelynek célja, hogy az áttelepítési kísérlet előtt azonnal visszaadja a felügyelt tartomány állapotát. Az Azure támogatási mérnökei végső megoldásként visszaállíthatnak egy felügyelt tartományt a biztonsági mentésből. További információt a [sikertelen áttelepítés ből történő visszaállítás vagy visszaállítás című témakörben](#roll-back-and-restore-from-migration)talál.

### <a name="restrictions-on-available-virtual-networks"></a>Az elérhető virtuális hálózatokra vonatkozó korlátozások

Vannak bizonyos korlátozások a virtuális hálózatok, amelyek az Azure AD DS felügyelt tartományban lehet áttelepíteni. A célerőforrás-kezelő virtuális hálózatának a következő követelményeknek kell megfelelnie:

* A Resource Manager virtuális hálózatnak ugyanabban az Azure-előfizetésben kell lennie, mint az a klasszikus virtuális hálózat, amelyben az Azure AD DS jelenleg telepítve van.
* Az Erőforrás-kezelő virtuális hálózatának ugyanabban a régióban kell lennie, mint a klasszikus virtuális hálózatnak, amelyben az Azure AD DS jelenleg telepítve van.
* Az Erőforrás-kezelő virtuális hálózat alhálózatának legalább 3-5 elérhető IP-címmel kell rendelkeznie.
* Az Erőforrás-kezelő virtuális hálózat alhálózatának dedikált alhálózatnak kell lennie az Azure AD DS számára, és nem üzemeltethet más számítási feladatokat.

A virtuális hálózati követelményekkel kapcsolatos további információkért olvassa el a [Virtuális hálózat tervezési szempontjait és konfigurációs beállításait.][network-considerations]

## <a name="migration-steps"></a>A migrálás lépései

Az Erőforrás-kezelő telepítési modelljére és a virtuális hálózatra történő áttelepítés 5 fő lépésre van felosztva:

| Lépés    | Végzett keresztül  | Becsült idő  | Állásidő  | Vissza-visszaállítás? |
|---------|--------------------|-----------------|-----------|-------------------|
| [1. lépés - Frissítse és keresse meg az új virtuális hálózat](#update-and-verify-virtual-network-settings) | Azure Portal | 15 perc | Nincs szükség állásidőre | N/A |
| [2. lépés – Az Azure AD DS felügyelt tartományának előkészítése áttelepítésre](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 perc átlagosan | Az Azure AD DS állásideje a parancs befejezése után indul el. | Visszaállíthatja és visszaállíthatja a rendelkezésre álló adatokat. |
| [3. lépés – Az Azure AD DS felügyelt tartományának áthelyezése egy meglévő virtuális hálózatra](#migrate-the-managed-domain) | PowerShell | 1 – 3 óra átlagosan | A parancs befejezése után egy tartományvezérlő érhető el, az állásidő befejeződik. | Hiba esetén mind a visszaállítás (önkiszolgáló) és a visszaállítás elérhető. |
| [4. lépés - A replika tartományvezérlő tesztelése és megvárása](#test-and-verify-connectivity-after-the-migration)| PowerShell és Azure portal | 1 óra vagy több, a vizsgálatok számától függően | Mindkét tartományvezérlő elérhető, és normálisan kell működnie. | N/A. Az első virtuális gép áttelepítése sikeresen megtörtént, nincs lehetőség a visszaállításra vagy visszaállításra. |
| [5. lépés – Választható konfigurációs lépések](#optional-post-migration-configuration-steps) | Azure Portal és virtuális gépek | N/A | Nincs szükség állásidőre | N/A |

> [!IMPORTANT]
> További állásidő elkerülése érdekében olvassa el ezt az áttelepítési cikket és útmutatást az áttelepítési folyamat megkezdése előtt. Az áttelepítési folyamat hatással van az Azure AD DS tartományvezérlők rendelkezésre állására egy ideig. A felhasználók, a szolgáltatások és az alkalmazások nem hitelesíthetők a felügyelt tartománnyal szemben az áttelepítési folyamat során.

## <a name="update-and-verify-virtual-network-settings"></a>Virtuális hálózati beállítások frissítése és ellenőrzése

Az áttelepítési folyamat megkezdése előtt végezze el a következő kezdeti ellenőrzéseket és frissítéseket. Ezek a lépések az áttelepítés előtt bármikor megfordulhatnak, és nincsenek hatással az Azure AD DS felügyelt tartományműködésére.

1. Frissítse a helyi Azure PowerShell-környezetet a legújabb verzióra. Az áttelepítési lépések végrehajtásához legalább *a 2.3.2-es*verzióra van szükség.

    A PowerShell-verzió ellenőrzéséről és frissítésével kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell áttekintését.][azure-powershell]

1. Hozzon létre vagy válasszon ki egy meglévő erőforrás-kezelői virtuális hálózatot.

    Győződjön meg arról, hogy a hálózati beállítások nem blokkolja a szükséges portok az Azure AD DS. A portoknak nyitva kell lenniük mind a klasszikus virtuális hálózaton, mind az Erőforrás-kezelő virtuális hálózaton. Ezek a beállítások magukban foglalják az útvonaltáblákat (bár nem ajánlott útvonaltáblákat használni) és a hálózati biztonsági csoportokat.

    A szükséges portok megtekintéséhez olvassa el a [Hálózati biztonsági csoportok és a szükséges portok című témakört.][network-ports] A hálózati kommunikációs problémák minimalizálása érdekében ajánlott várni, és alkalmazni egy hálózati biztonsági csoport vagy útvonaltáblát az Erőforrás-kezelő virtuális hálózatára az áttelepítés sikeres befejezése után.

    Jegyezze fel ezt a célerőforrás-csoportot, a célvirtuális hálózatot és a virtuális hálózati alhálózatot. Ezek az erőforrásnevek az áttelepítési folyamat során használatosak.

1. Ellenőrizze az Azure AD DS felügyelt tartomány állapotát az Azure Portalon. Ha bármilyen riasztást a felügyelt tartomány, oldja meg őket, mielőtt az áttelepítési folyamat.
1. Ha azt szeretné, hogy más erőforrásokat helyezzen át az Erőforrás-kezelő telepítési modelljébe és a virtuális hálózatba, ellenőrizze, hogy ezek az erőforrások áttelepíthetők-e. További információ: [Az IaaS-erőforrások platform által támogatott áttelepítése klasszikusról erőforrás-kezelőre.][migrate-iaas]

    > [!NOTE]
    > Ne konvertálja a klasszikus virtuális hálózatot Erőforrás-kezelő virtuális hálózattá. Ha igen, nincs lehetőség az Azure AD DS felügyelt tartomány visszaállítása vagy visszaállítása.

## <a name="prepare-the-managed-domain-for-migration"></a>A felügyelt tartomány előkészítése az áttelepítésre

Az Azure PowerShell az Azure AD DS felügyelt tartományának áttelepítésre való előkészítésére szolgál. Ezek a lépések közé tartozik a biztonsági mentés, a szinkronizálás szüneteltetése és az Azure AD DS-t üzemeltető felhőszolgáltatás törlése. Ha ez a lépés befejeződik, az Azure AD DS offline állapotba kerül egy ideig. Ha az előkészítési lépés sikertelen, [visszaállíthatja az előző állapotot.](#roll-back)

Az Azure AD DS felügyelt tartományának áttelepítésre való előkészítéséhez hajtsa végre az alábbi lépéseket:

1. Telepítse `Migrate-Aaads` a parancsfájlt a [PowerShell-galériából.][powershell-script] Ez a PowerShell-áttelepítési parancsfájl az Azure AD mérnöki csapata által digitálisan aláírt.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Hozzon létre egy változót az áttelepítési parancsfájl hitelesítő adatainak tárolásához a [Get-Credential][get-credential] parancsmag használatával.

    A megadott felhasználói fióknak *globális rendszergazdai* jogosultságokra van szüksége az Azure AD-bérlőben az Azure AD DS engedélyezéséhez, majd az Azure-előfizetésben *lévő közreműködői* jogosultságokat a szükséges Azure AD DS-erőforrások létrehozásához.

    Amikor a rendszer kéri, adja meg a megfelelő felhasználói fiókot és jelszót:

    ```powershell
    $creds = Get-Credential
    ```

1. Most futtassa a `Migrate-Aadds` parancsmagát a *-Prepare* paraméter rel. Adja meg a *-ManagedDomainFqdn* saját Azure AD DS felügyelt tartományához, például *a aaddscontoso.com:*

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>A felügyelt tartomány áttelepítése

Az Azure AD DS felügyelt tartomány előkészített és biztonsági másolatot készített, a tartomány áttelepíthető. Ez a lépés újralétrehozza az Azure AD tartományi szolgáltatások tartományvezérlő virtuális gépeit az Erőforrás-kezelő telepítési modelljével. Ez a lépés 1-3 órát vehet igénybe.

Futtassa a `Migrate-Aadds` parancsmast a *-Commit* paraméter rel. Adja meg a *-ManagedDomainFqdn* a saját Azure AD DS felügyelt tartomány készített az előző szakaszban, például *a aaddscontoso.com:*

Adja meg azt a célerőforrás-csoportot, amely azt a virtuális hálózatot tartalmazza, amelybe át szeretné telepíteni az Azure AD DS szolgáltatást, például *a myResourceGroup*szolgáltatást. Adja meg a cél virtuális hálózatot, például *a myVnet*és az alhálózatot, például *a DomainServices szolgáltatást.*

A parancs futtatása után nem állíthatja vissza:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

Miután a parancsfájl ellenőrzi a felügyelt tartomány előkészítése áttelepítés, adja meg *az Y* az áttelepítési folyamat elindításához.

> [!IMPORTANT]
> Ne konvertálja a klasszikus virtuális hálózatot erőforrás-kezelői virtuális hálózattá az áttelepítési folyamat során. Ha konvertálja a virtuális hálózatot, nem állíthatja vissza vagy állíthatja vissza az Azure AD DS felügyelt tartományt, mivel az eredeti virtuális hálózat már nem létezik.

Az áttelepítési folyamat során kétpercenként egy állapotjelző jelenti az aktuális állapotot, amint az a következő példa kimenetben látható:

![Az Azure AD DS áttelepítésének előrehaladási mutatója](media/migrate-from-classic-vnet/powershell-migration-status.png)

Az áttelepítési folyamat továbbra is fut, még akkor is, ha bezárja a PowerShell-parancsfájlt. Az Azure Portalon a felügyelt tartományjelentések állapota *áttelepítés.*

Ha az áttelepítés sikeresen befejeződött, megtekintheti az első tartományvezérlő IP-címét az Azure Portalon vagy az Azure PowerShellen keresztül. A rendelkezésre álló második tartományvezérlő niidő-becslése is látható.

Ebben a szakaszban tetszés szerint áthelyezhet más meglévő erőforrásokat a klasszikus telepítési modellből és a virtuális hálózatból. Vagy megtarthatja az erőforrásokat a klasszikus üzembe helyezési modellen, és társviszonyban tarthatja a virtuális hálózatokat egymással az Azure AD DS-áttelepítés befejezése után.

## <a name="test-and-verify-connectivity-after-the-migration"></a>A kapcsolat tesztelése és ellenőrzése az áttelepítés után

Eltarthat egy ideig, amíg a második tartományvezérlő sikeresen üzembe helyezése, és elérhető az Azure AD DS felügyelt tartományban.

A Resource Manager üzembe helyezési modell, az Azure AD DS felügyelt tartomány hálózati erőforrásait az Azure Portalon vagy az Azure PowerShellben jelennek meg. Ha többet szeretne megtudni arról, hogy mik ezek a hálózati erőforrások, és tegye, olvassa el [az Azure AD DS által használt hálózati erőforrásokat.][network-resources]

Ha legalább egy tartományvezérlő elérhető, hajtsa végre az alábbi konfigurációs lépéseket a virtuális gépekkel való hálózati kapcsolathoz:

* **DNS-kiszolgáló beállításainak frissítése** Ha azt szeretné, hogy az Erőforrás-kezelő virtuális hálózatának más erőforrásai feloldódjanak, és az Azure AD DS felügyelt tartományát használhassa, frissítse a DNS-beállításokat az új tartományvezérlők IP-címeivel. Az Azure Portal automatikusan konfigurálhatja ezeket a beállításokat az Ön számára. Ha többet szeretne tudni arról, hogyan konfigurálhatja az Erőforrás-kezelő virtuális hálózatát, olvassa el [az Azure virtuális hálózat DNS-beállításainak frissítése][update-dns]című témakört.
* **Tartományhoz csatlakozó virtuális gépek újraindítása** – Az Azure AD DS tartományvezérlők DNS-kiszolgálójának IP-címei módosításakor indítsa újra a tartományhoz csatlakozó virtuális gépeket, hogy azok az új DNS-kiszolgáló-beállításokat használják. Ha az alkalmazások vagy a virtuális gépek manuálisan konfigurálták a DNS-beállításokat, manuálisan frissítse őket az Azure Portalon megjelenő tartományvezérlők új DNS-kiszolgálóIP-címeivel.

Most tesztelje a virtuális hálózati kapcsolatot és a névfeloldást. Az Erőforrás-kezelő virtuális hálózatához csatlakoztatott vagy társviszonyba kapcsolt virtuális gépen próbálkozzon a következő hálózati kommunikációs tesztekkel:

1. Ellenőrizze, hogy pingelheti-e az egyik tartományvezérlő IP-címét, például`ping 10.1.0.4`
    * A tartományvezérlők IP-címei az Azure AD DS felügyelt **tartományának Tulajdonságok** lapján jelennek meg az Azure Portalon.
1. A felügyelt tartomány névfeloldásának ellenőrzése, például`nslookup aaddscontoso.com`
    * Adja meg a DNS-nevet a saját Azure AD DS felügyelt tartomány, hogy ellenőrizze, hogy a DNS-beállítások helyesek, és feloldja.

A második tartományvezérlőnek az áttelepítési parancsmag befejezése után 1-2 órával elérhetőnek kell lennie. Annak ellenőrzéséhez, hogy a második tartományvezérlő elérhető-e, tekintse meg az Azure AD DS felügyelt **tartományának Tulajdonságok** lapját az Azure Portalon. Ha két IP-cím látható, a második tartományvezérlő készen áll.

## <a name="optional-post-migration-configuration-steps"></a>Az áttelepítés utáni választható konfigurációs lépések

Az áttelepítési folyamat sikeres befejezése után néhány választható konfigurációs lépés közé tartozik a naplónaplók vagy az e-mail értesítések engedélyezése, vagy a részletes jelszóházirend frissítése.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Előelőfizetés a naplónaplókra az Azure Monitor használatával

Az Azure AD DS a tartományvezérlőkön történt események hibaelhárítása és megtekintése érdekében teszi elérhetővé a naplózási naplókat. További információt a Naplózási naplók engedélyezése és használata című [témakörben talál.][security-audits]

A sablonok segítségével figyelheti a naplókban elérhető fontos információkat. Például a napló munkafüzet sablon figyelheti a lehetséges fiókzárolások az Azure AD DS felügyelt tartományban.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>Az Azure AD tartományi szolgáltatások e-mail értesítéseinek konfigurálása

Ha értesítést szeretne kapni, ha problémát észlel az Azure AD DS felügyelt tartományban, frissítse az e-mail értesítési beállításokat az Azure Portalon. További információt az [Értesítési beállítások konfigurálása][notifications]című témakörben talál.

### <a name="update-fine-grained-password-policy"></a>Részletes jelszóházirend frissítése

Szükség esetén frissítheti a részletes jelszóházirendet, hogy kevésbé legyen korlátozó, mint az alapértelmezett konfiguráció. A naplók segítségével megállapíthatja, hogy egy kevésbé korlátozó beállításnak van-e értelme, majd szükség szerint konfigurálja a házirendet. Az áttelepítés után többször zárolt fiókok házirend-beállításainak áttekintéséhez és frissítéséhez kövesse az alábbi magas szintű lépéseket:

1. [Jelszóházirend konfigurálása][password-policy] kevesebb korlátozást az Azure AD DS felügyelt tartományban, és figyelje meg az eseményeket a naplónaplókban.
1. Ha valamelyik szolgáltatásfiók lejárt jelszavakat használ a naplónaplókban megadottak szerint, frissítse ezeket a fiókokat a megfelelő jelszóval.
1. Ha egy virtuális gép elérhetővé válik az interneten, tekintse át az általános fióknevek, például *rendszergazda*, *felhasználó,* vagy a magas bejelentkezési kísérletekkel rendelkező *vendég.* Ahol lehetséges, frissítse ezeket a virtuális gépeket, hogy kevésbé általánosan elnevezett fiókokat használjon.
1. A virtuális gép hálózati nyomkövetése segítségével keresse meg a támadások forrását, és tiltsa le az IP-címeket, hogy megkísérelhetia a bejelentkezéseket.
1. Ha minimális zárolási problémák merülnek fel, frissítse a részletes jelszóházirendet, hogy az a szükséges mértékben korlátozó legyen.

### <a name="creating-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

Az Azure AD DS-nek szüksége van egy hálózati biztonsági csoportra a felügyelt tartományhoz szükséges portok védelméhez és az összes többi bejövő forgalom blokkolásához. Ez a hálózati biztonsági csoport egy további védelmi rétegként működik a felügyelt tartományhoz való hozzáférés zárolásához, és nem jön létre automatikusan. A hálózati biztonsági csoport létrehozásához és a szükséges portok megnyitásához tekintse át az alábbi lépéseket:

1. Az Azure Portalon válassza ki az Azure AD DS-erőforrást. Az áttekintő lapon egy gomb jelenik meg egy hálózati biztonsági csoport létrehozásához, ha nincs társítva az Azure AD tartományi szolgáltatásokhoz.
1. Ha biztonságos LDAP-t használ, adjon hozzá egy szabályt a hálózati biztonsági csoporthoz a *636-os* *TCP-port* bejövő forgalmának engedélyezéséhez. További információt a [Biztonságos LDAP konfigurálása][secure-ldap]című témakörben talál.

## <a name="roll-back-and-restore-from-migration"></a>Visszaállítás és visszaállítás az áttelepítésből

Az áttelepítési folyamat egy bizonyos pontjáig dönthet úgy, hogy visszaállítja vagy visszaállítja az Azure AD DS felügyelt tartományát.

### <a name="roll-back"></a>Visszaállítás

Ha hiba történik, amikor a PowerShell-parancsmag futtatásakor előkészíti az áttelepítést a 2. Ehhez a visszaállításhoz az eredeti Klasszikus virtuális hálózatra van szükség. Ne feledje, hogy az IP-címek a visszaállítás után is változhatnak.

Futtassa a `Migrate-Aadds` parancsmamot a *-Abort* paraméter rel. Adja meg a *-ManagedDomainFqdn* a saját Azure AD DS felügyelt tartomány készített egy előző szakaszban, például *aaddscontoso.com*, és a klasszikus virtuális hálózat neve, például *a myClassicVnet:*

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>Visszaállítás

Végső megoldásként az Azure AD tartományi szolgáltatások visszaállíthatók az utolsó rendelkezésre álló biztonsági mentés. Az áttelepítés 1. Ez a biztonsági mentés 30 napig tárolódik.

Az Azure AD DS felügyelt tartományának biztonsági mentésből történő visszaállításához [nyisson meg egy támogatási esetjegyet az Azure Portal használatával.][azure-support] Adja meg a címtárazonosítót, a tartománynevet és a visszaállítás okát. A támogatási és visszaállítási folyamat több napot is igénybe vehet.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az Erőforrás-kezelő telepítési modelljére való áttelepítés után problémákmerülnek fel, tekintse át az alábbi gyakori hibaelhárítási területeket:

* [Tartománycsatlakozási problémák elhárítása][troubleshoot-domain-join]
* [Fiókzárolási problémák elhárításának elhárítása][troubleshoot-account-lockout]
* [Fiókbejelentkezési problémák elhárítása][troubleshoot-sign-in]
* [Biztonságos LDAP-csatlakozási problémák elhárítása][tshoot-ldaps]

## <a name="next-steps"></a>További lépések

Az Azure AD DS felügyelt tartományát áttelepítve az Erőforrás-kezelő telepítési modell, [hozzon létre és tartomány-csatlakozás a Windows virtuális gép,][join-windows] majd [telepítse a felügyeleti eszközöket.][tutorial-create-management-vm]

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
