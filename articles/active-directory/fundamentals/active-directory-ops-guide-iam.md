---
title: Az Azure Active Directory identitás- és hozzáférés-kezelési műveleteinek útmutatója
description: Ez a műveleti referencia-útmutató ismerteti azokat az ellenőrzéseket és műveleteket, amelyeket meg kell tennie az identitás- és hozzáférés-kezelési műveletek biztonságossá tétele érdekében
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298614"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Az Azure Active Directory identitás- és hozzáférés-kezelési műveleteinek útmutatója

Az Azure [AD-műveletek referencia-útmutatóezi](active-directory-ops-guide-intro.md) az okat az ellenőrzéseket és műveleteket, amelyeket figyelembe kell vennie az identitások és azok hozzárendeléseinek életciklusának biztonságossá tétele és kezelése érdekében.

> [!NOTE]
> Ezek az ajánlások a közzététel időpontjától aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell identitáskezelési gyakorlatukat, ahogy a Microsoft-termékek és -szolgáltatások idővel fejlődnek.

## <a name="key-operational-processes"></a>Kulcsfontosságú működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok hozzárendelése a legfontosabb feladatokhoz

Az Azure Active Directory kezeléséhez olyan kulcsfontosságú operatív feladatok és folyamatok folyamatos végrehajtására van szükség, amelyek nem feltétlenül részei a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet fenntartása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Tevékenység | Tulajdonos |
| :- | :- |
| Az Azure-előfizetések létrehozásának folyamatának meghatározása | Szervezetenként változik |
| Döntse el, ki kapja meg az Enterprise Mobility + Security licenceket | IAM műveleti csapat |
| Annak eldöntése, hogy ki kap Office 365-licenceket | Termelékenységi csapat |
| Döntse el, hogy ki kap más licenceket, például a Dynamics, a VSO | Alkalmazás tulajdonosa |
| Licencek hozzárendelése | IAM műveleti csapat |
| Licenchozzárendelési hibák elhárítása és javítása | IAM műveleti csapat |
| Identitások kiépítése az Azure AD-ben lévő alkalmazásokhoz | IAM műveleti csapat |

A lista áttekintésekor előfordulhat, hogy tulajdonost kell hozzárendelnie a tulajdonosból hiányzó feladatokhoz, vagy módosítania kell a fenti javaslatokhoz nem igazodó tulajdonossal rendelkező feladatok tulajdonjogát.

#### <a name="assigning-owners-recommended-reading"></a>A tulajdonosok ajánlott olvasásának hozzárendelése

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Irányítás az Azure-ban](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Helyszíni identitás-szinkronizálás

### <a name="identify-and-resolve-synchronization-issues"></a>Szinkronizálási problémák azonosítása és megoldása

A Microsoft azt javasolja, hogy jó alapkonfigurációval és a helyszíni környezetben felmerülő problémák megértésével, amelyek szinkronizálási problémákat eredményezhetnek a felhőben. Mivel az olyan automatizált eszközök, mint [az IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) és az [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) nagy mennyiségű hamis pozitív eredményt generálhatnak, azt javasoljuk, hogy azonosítsa azokat a szinkronizálási hibákat, amelyeket több mint 100 napig nem címeztek, ha hibaként tisztítja meg ezeket az objektumokat. A hosszú távú megoldatlan szinkronizálási hibák támogatási incidenseket okozhatnak. [A szinkronizálás során előforduló hibák elhárítása](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) áttekintést nyújt a szinkronizálási hibák különböző típusairól, néhány lehetséges forgatókönyvről, amelyek ezeket a hibákat okozzák, és a hibák lehetséges módjairól.

### <a name="azure-ad-connect-sync-configuration"></a>Az Azure AD Connect szinkronizálásának konfigurációja

Az összes hibrid élmény, az eszközalapú biztonsági állapot és az Azure AD-vel való integráció engedélyezéséhez szinkronizálnia kell azokat a felhasználói fiókokat, amelyeket az alkalmazottak az asztalukra való bejelentkezéshez használnak.

Ha nem szinkronizálja az erdő felhasználói nak bejelentkezését, akkor módosítania kell a szinkronizálást úgy, hogy az a megfelelő erdőből származzon.

#### <a name="synchronization-scope-and-object-filtering"></a>Szinkronizálási hatókör és objektumszűrés

A szinkronizálandó, nem szükséges objektumok ismert gyűjtőinek eltávolítása a következő működési előnyökkel jár:

- Kevesebb szinkronizálási hibaforrás
- Gyorsabb szinkronizálási ciklusok
- Kevesebb "szemét" átvitele a helyszíni, például a környezetszennyezés a globális címlista a helyszíni szolgáltatás fiókok, amelyek nem relevánsak a felhőben

> [!NOTE]
> Ha úgy találja, hogy sok olyan objektumot importál, amelyek nem exportálva vannak a felhőbe, szűrje a szervezeti egység vagy az adott attribútumok szerint.

Példák a kizárandó objektumokra:

- A felhőalapú alkalmazásokhoz nem használt szolgáltatásfiókok
- Olyan csoportok, amelyeket nem felhőalapú forgatókönyvekben, például az erőforrásokhoz való hozzáférés biztosításához használt csoportokban kell használni
- Az Azure AD B2B együttműködéssel képviselendő külső identitások vagy kapcsolattartók
- Olyan számítógépfiókok, amelyeken az alkalmazottak nem használhatják a felhőalapú alkalmazásokat, például kiszolgálókról

> [!NOTE]
> Ha egyetlen emberi identitáshoz több fiók is tartozik, például örökölt tartományáttelepítésből, összeolvadásból vagy felvásárlásból, akkor csak a felhasználó által napi szinten használt fiókot szinkronizálja, például azt, hogy mit használnak a számítógépükre való bejelentkezéshez. .

Ideális esetben el kell érnie az egyensúlyt a szinkronizálandó objektumok számának csökkentése és a szabályok összetettsége között. Általában a szervezeti egység/tároló [szűrése](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) és a cloudFiltered attribútum egyszerű attribútumleképezése kombinációja hatékony szűrési kombináció.

> [!IMPORTANT]
> Ha a csoportszűrést használja éles környezetben, át kell térnie egy másik szűrési megközelítésre.

#### <a name="sync-failover-or-disaster-recovery"></a>Szinkronizálási feladatátvétel vagy vész-helyreállítás

Az Azure AD Connect kulcsfontosságú szerepet játszik a kiépítési folyamatban. Ha a szinkronizálási kiszolgáló bármilyen okból offline állapotba kerül, a helyszíni módosítások nem frissíthetők a felhőben, és hozzáférési problémákat okozhatnak a felhasználók számára. Ezért fontos olyan feladatátvételi stratégia definiálása, amely lehetővé teszi a rendszergazdák számára a szinkronizálás gyors folytatását, miután a szinkronizálási kiszolgáló offline állapotba kerül. Az ilyen stratégiák a következő kategóriákba sorolhatók:

- **Az Azure AD Connect Server(ek) telepítése átmeneti módban** – lehetővé teszi a rendszergazda számára, hogy egy egyszerű konfigurációs kapcsolóval "elősegítse" az átmeneti kiszolgálót az éles környezetben.
- **Virtualization használata** – Ha az Azure AD-connect egy virtuális gépen (VM) van telepítve, a rendszergazdák a virtualizációs verem segítségével az élő áttelepítésvagy a virtuális gép gyors újratelepítése, és így folytatja a szinkronizálást.

Ha a szervezet nem rendelkezik vész-helyreállítási és feladatátvételi stratégia szinkronizálás, ne habozzon telepíteni az Azure AD Connect átmeneti módban. Hasonlóképpen, ha az éles és átmeneti konfiguráció között eltérés van, újra kell alapvonalba hozni az Azure AD Connect átmeneti módot, hogy megfeleljen az éles konfigurációnak, beleértve a szoftververziókat és konfigurációkat is.

![Képernyőkép az Azure AD Connect átmeneti mód konfigurációjáról](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Legyen naprakész

A Microsoft rendszeresen frissíti az Azure AD Connectet. Maradjon naprakész, hogy kihasználhassa az egyes új verziók által nyújtott teljesítménybeli fejlesztéseket, hibajavításokat és új funkciókat.

Ha az Azure AD Connect verziója több mint hat hónappal le van maradva, frissítenie kell a legújabb verzióra.

#### <a name="source-anchor"></a>Forráshorgony

Az **ms-DS-consistencyguid** [forráshorgonyként](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) való használata lehetővé teszi az objektumok erdők és tartományok közötti áttelepítését, ami gyakori az AD tartomány konszolidációjában/karbantartásában, az egyesülésekben, az akvizíciókban és az elidegenítésekben.

Ha jelenleg az **ObjectGuid-ot** használja forráshorgonyként, javasoljuk, hogy váltson **az ms-DS-ConsistencyGuid**elemre.

#### <a name="custom-rules"></a>Egyéni szabályok

Az Azure AD Connect egyéni szabályai lehetővé teszik az attribútumok áramlását a helyszíni objektumok és a felhőbeli objektumok között. Az egyéni szabályok túlzott használata vagy helytelen használata azonban a következő kockázatokat jelentheti:

- Az összetettség hibáinak elhárítása
- A teljesítmény romlása összetett műveletek objektumok közötti végrehajtásakor
- Nagyobb a valószínűsége az éles kiszolgáló és az átmeneti kiszolgáló közötti konfigurációdiverzifikációnak
- További többletterhelés az Azure AD Connect frissítésekor, ha egyéni szabályok jönnek létre a 100-nál nagyobb prioritáson belül (beépített szabályok használják)

Ha túlságosan összetett szabályokat használ, vizsgálja meg az összetettség okait, és keressen lehetőséget az egyszerűsítésre. Hasonlóképpen, ha 100-nál nagyobb prioritású egyéni szabályokat hozott létre, javítsa ki a szabályokat, hogy ne legyenek veszélyben vagy ütközzenek az alapértelmezett készlettel.

Az egyéni szabályokkal való visszahasználata például a következő:

- **A címtárban lévő szennyezett adatok kompenzálása** – Ebben az esetben ajánlott az AD-csoport tulajdonosaival együttműködni, és a címtárban lévő adatok szervizelési feladatként történő karbantartása, valamint a folyamatok módosítása a rossz adatok újbóli bevezetésének elkerülése érdekében.
- **Az egyes felhasználók egyszeri szervizelése** – Gyakori, hogy olyan szabályokat találnak, amelyek kiugró értékeket jelentenek, általában egy adott felhasználóval kapcsolatos probléma miatt.
- **Túlbonyolított "CloudFiltering"** - Bár az objektumok számának csökkentése egy jó gyakorlat, fennáll a veszélye létrehozása és túlbonyolított szinkronizálási hatókör segítségével sok szinkronizálási szabályokat. Ha a szervezeti egység szűrésén kívül összetett logika is szerepel,/zárható ki, ajánlott ezt a logikát a szinkronizáláson kívül kezelni, és az objektumokat egy egyszerű "cloudFiltered" attribútummal címkézni, amely egyszerű szinkronizálási szabállyal is folyhat.

#### <a name="azure-ad-connect-configuration-documenter"></a>Az Azure AD Connect konfigurációs dokumentumkezelője

Az [Azure AD Connect konfigurációs dokumentumkészítő](https://github.com/Microsoft/AADConnectConfigDocumenter) egy olyan eszköz, amellyel az Azure AD Connect-telepítés dokumentációját hozhatja létre, hogy jobban megértse a szinkronizálási konfigurációt, bizalmat építsen a dolgok helyes beírásában, és hogy megtudja, mi változott az Azure AD Connect új buildjének vagy konfigurációjának alkalmazásakor, illetve hozzáadott vagy frissített egyéni szinkronizálási szabályok hozzáadásakor. Az eszköz jelenlegi képességei a következők:

- Az Azure AD Connect szinkronizálásteljes konfigurációjának dokumentációja.
- Két Azure AD Connect szinkronizálási kiszolgáló konfigurációjának változásainak vagy egy adott konfigurációs alapkonfiguráció változásainak dokumentálása.
- PowerShell-telepítési parancsfájl létrehozása a szinkronizálási szabályok különbségeinek vagy testreszabásainak egyik kiszolgálóról a másikra történő áttelepítéséhez.

## <a name="assignment-to-apps-and-resources"></a>Hozzárendelés alkalmazásokhoz és erőforrásokhoz

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Csoportalapú licencelés a Microsoft felhőszolgáltatásaihoz

Az Azure Active Directory a Microsoft felhőszolgáltatásainak [csoportalapú licencelésével](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) egyszerűsíti a licencek kezelését. Ily módon az IAM biztosítja a csoport infrastruktúráját és a csoportok delegált kezelését a szervezetek megfelelő csoportjai számára. Az Azure AD-ben többféleképpen is beállíthatja a csoportok tagságát, többek között a következőket:

- **A helyszíni** szinkronizálás – A csoportok helyszíni könyvtárakból származhatnak, ami jól illeszkedhet az olyan szervezetek számára, amelyek olyan csoportkezelési folyamatokat hoztak létre, amelyek kiterjeszthetők az Office 365-ben lévő licencek hozzárendeléséhez.

- **Attribútumalapú / dinamikus** – A csoportok a felhőben a felhasználói attribútumokon alapuló kifejezés alapján hozhatók létre, például a Részleg egyenlő az "értékesítéssel". Az Azure AD fenntartja a csoport tagjait, és konzisztens marad a definiált kifejezéssel. Ha ezt a fajta csoportot licenc-hozzárendeléshez használja, az attribútumalapú licenc-hozzárendelést tesz lehetővé, amely jól illeszkedik a címtárukban magas adatminőséggel rendelkező szervezetek számára.

- **Delegált tulajdonjog** – Csoportok hozhatók létre a felhőben, és kijelölhetők tulajdonosok. Így felhatalmazhatja a vállalkozások tulajdonosait, például az együttműködési csapatot vagy a BI-csapatot, hogy meghatározzák, kinek kell hozzáféréssel rendelkeznie.

Ha jelenleg manuális eljárást használ a licencek és összetevők felhasználókhoz rendelésére, javasoljuk, hogy hajtsa végre a csoportalapú licencelést. Ha az aktuális folyamat nem figyeli a licencelési hibákat, vagy azt, hogy mi van hozzárendelve, és mi érhető el, meg kell határoznia a folyamat fejlesztéseit a licencelési hibák kezelése és a licencelési hozzárendelés figyelése érdekében.

A licenckezelés másik aspektusa a szolgáltatási csomagok (a licenc összetevői) definíciója, amelyet engedélyezni kell a szervezet feladatfunkciói alapján. A szükségtelen szolgáltatási csomagokhoz való hozzáférés megadásával a felhasználók olyan eszközöket láthatnak az Office-portálon, amelyekhez nem képezték ki őket, vagy amelyeket nem kellene használniuk. További ügyfélszolgálati kötetet, szükségtelen kiépítést és a megfelelőségés a cégirányítási kockázatot jelenthet, például amikor olyan személyeknek biztosít ki, akik esetleg nem oszthatnak meg tartalmakat.

Az alábbi irányelvek segítségével határozza meg a felhasználók nak szóló szolgáltatási csomagokat:

- A rendszergazdáknak meg kell határozniuk a felhasználóknak felajánlandó szolgáltatási csomagok "csomagjait" a szerepkörük alapján, például a fehérgalléros dolgozó és a padlódolgozó között.
- Csoportok létrehozása fürt szerint, és a licenc hozzárendelése a szolgáltatási csomaggal.
- Szükség esetén egy attribútum definiálható a csomagok tárolására a felhasználók számára.

> [!IMPORTANT]
> Az Azure AD csoportalapú licencelése a felhasználók licencelési hibaállapotú koncepcióját mutatja be. Ha licencelési hibát észlel, azonnal [azonosítsa és megoldja](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) a licenchozzárendelési problémákat.

![A képernyőkép a számítógép képernyőjénAutomatikusan generált leírás](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Életciklus-kezelés

Ha jelenleg olyan eszközt használ, például [a Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) vagy a külső rendszer, amely egy helyszíni infrastruktúrára támaszkodik, javasoljuk, hogy a meglévő eszközből kiszervezi a hozzárendelést, valósítsa meg a csoportalapú licencelést, és [csoportalapú](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups)csoportéletciklus-kezelést határozzon meg. Hasonlóképpen, ha a meglévő folyamat nem veszi figyelembe az új alkalmazottak vagy alkalmazottak, hogy hagyja el a szervezetet, telepítenie kell a csoportalapú licencelés dinamikus csoportok alapján, és meghatározza a csoport tagságéletciklusát. Végül, ha a csoportalapú licencelés olyan helyszíni csoportok ellen van telepítve, amelyek nem rendelkeznek életciklus-felügyelettel, fontolja meg a felhőcsoportok használatát olyan képességek engedélyezéséhez, mint például a delegált tulajdonjog vagy az attribútumalapú dinamikus tagság.

### <a name="assignment-of-apps-with-all-users-group"></a>Alkalmazások hozzárendelése a "Minden felhasználó" csoporttal

Az erőforrás-tulajdonosok úgy érezhetik, hogy a **Minden felhasználó** csoport csak **vállalati alkalmazottakat** tartalmaz, ha azok ténylegesen tartalmazhatnak **vállalati alkalmazottakat** és **vendégeket is.** Ennek eredményeképpen különös figyelmet kell fordítania arra, hogy a **Minden felhasználó** csoportot használja az alkalmazás-hozzárendeléshez, és hozzáférést biztosítson az erőforrásokhoz, például a SharePoint-tartalomhoz vagy az alkalmazásokhoz.

> [!IMPORTANT]
> Ha a **Minden felhasználó** csoport engedélyezve van, és feltételes hozzáférési szabályzatokhoz, alkalmazás- vagy erőforrás-hozzárendeléshez van használva, győződjön meg arról, hogy [biztonságossá teszi a csoportot,](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) ha nem szeretné, hogy vendégfelhasználókat tartalmazzon. Ezenkívül a licencelési hozzárendeléseket úgy kell kijavítania, hogy olyan csoportokat hoz létre és rendel hozzá, amelyek csak **vállalati alkalmazottakat tartalmaznak.** Ha viszont úgy találja, hogy a **Minden felhasználó** csoport engedélyezve van, de nem használható az erőforrásokhoz való hozzáférés engedélyezésére, győződjön meg arról, hogy a szervezet működési útmutatója a csoport szándékos használata (amely magában foglalja a **vállalati alkalmazottakat** és **a vendégeket**is).

### <a name="automated-user-provisioning-to-apps"></a>Automatikus felhasználói kiépítés az alkalmazásokba

[Az alkalmazásokautomatikus felhasználói kiépítés](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) a legjobb módja annak, hogy egységes kiépítést, megszüntetést és az identitások életciklusát hozzon létre több rendszeren keresztül.

Ha jelenleg ad-hoc módon épít ki alkalmazásokat, vagy például CSV-fájlokat, jit-fájlokat vagy olyan helyszíni megoldást használ, amely nem foglalkozik az életciklus-kezeléssel, javasoljuk, hogy [valósítsa meg az alkalmazások azure](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) AD-vel való kiépítését a támogatott alkalmazásokhoz, és egységes mintát határozzon meg az Azure AD által még nem támogatott alkalmazásokhoz.

![Azure AD-kiépítési szolgáltatás](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect különbözeti szinkronizálási ciklus alapkonfigurációja

Fontos megérteni a szervezetben végrehajtott változások mennyiségét, és győződjön meg arról, hogy nem tart túl sokáig a kiszámítható szinkronizálási idő.

Az [alapértelmezett különbözeti szinkronizálási](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) gyakoriság 30 perc. Ha a különbözeti szinkronizálás konzisztensen 30 percnél tovább tart, vagy jelentős eltérések vannak az előkészítés és az éles környezet különbözeti szinkronizálási teljesítménye között, meg kell vizsgálnia és át kell [tekintenie az Azure AD Connect teljesítményét befolyásoló tényezőket.](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors)

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Az Azure AD Connect hibaelhárítása az ajánlott olvasással kapcsolatban

- [Címtárattribútumok előkészítése az Office 365-tel való szinkronizáláshoz az IdFix eszközzel - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Hibaelhárítás a szinkronizálás során](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúra öt szempontból áll. Ez a lista segít gyorsan megtalálni és megtenni a szükséges lépéseket az identitások és jogosultságaik életciklusának és a szervezeten belüli életciklusának biztonságossá tétele és kezelése érdekében.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- A szinkronizálási problémák megkeresése és megoldása.
- Feladatátvételi stratégia meghatározása a vész-helyreállítási.
- Egyszerűsítse a licencek kezelését és az alkalmazások hozzárendelését.
- Automatizálhatja a felhasználók alkalmazásokba való kiépítését.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a [hitelesítéskezelési ellenőrzésekkel és műveletekkel.](active-directory-ops-guide-auth.md)
