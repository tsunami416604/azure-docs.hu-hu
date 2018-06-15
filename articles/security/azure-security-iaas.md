---
title: Ajánlott biztonsági eljárások IaaS munkaterhelések az Azure-ban |} Microsoft Docs
description: " Az áttelepítés a Azure IaaS munkaterhelések számos lehetőséget kínál, hogy újra kiértékelje a tervek lehetőségek "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 1a6ff01274c4a47730ffe45275aed9d122994260
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366273"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Ajánlott biztonsági eljárások az Azure IaaS munkaterhelések

Munkaterhelések áthelyezése az Azure-infrastruktúra (IaaS) szolgáltatás továbbléphetnek használatához, akkor valószínűleg is tudjuk, hogy tisztában legyenek-e azt is számításba kell. Előfordulhat, hogy már nincs a virtuális környezetek biztonságossá tétele. Amikor Azure IaaS helyezi át, a virtuális környezetek biztonságossá tétele a szakértelem alkalmazása, és válasszon egy új beállításokat biztonságossá a eszközöket.

Kezdjük, hogy azt nem kell látnia a helyszíni erőforrásokhoz, mint egy az egyhez típusú kerüljön Azure kimondásával. Az új kihívásokat és az új beállítások hozza újra a meglévő kiértékelje lehetőséget deigns, eszközök és dolgozza fel.

A biztonsági felelős alapul a felhőalapú szolgáltatás. Az alábbi táblázat összefoglalja a Microsoft és az Ön felelőssége egyenleg:


![A felelősségi területeket](./media/azure-security-iaas/sec-cloudstack-new.png)


Az, amelyik segíthet a vállalat biztonsági követelményeinek Azure-ban rendelkezésre álló beállítások némelyike mutatjuk. Ne feledje, hogy a biztonsági követelmények eltérőek lehetnek, munkaterhelések különböző típusú. Az alábbi gyakorlati tanácsok nem egyik önmagában biztonságossá teheti a számítógépeken. Mint bármely más, a biztonsági akkor válassza ki a megfelelő beállításokat, és tekintse meg, hogyan megoldások is kiegészíteni egymással hézagok kitöltésével.

## <a name="use-privileged-access-workstations"></a>Szintű hozzáféréssel rendelkező munkaállomások használata

A szervezetek gyakran tartoznak kihasználják a cyberattacks mert rendszergazdák műveleteket emelt szintű jogosultságokkal rendelkező fiókok használata során. Általában ez nem történik ártó, de mivel a meglévő konfiguráció és folyamatok engedélyezi-e. Ezek a felhasználók a legtöbb megismeréséhez ezeket a műveleteket fogalmi szempontból kockázatát, de továbbra is szeretné őket.

Például az e-mailek ellenőrzése és a webböngészés látszólag elég tűnve történt. De esetleg felfedi a rosszindulatú szereplője által behatolhat emelt szintű fiókok. Böngészés a tevékenységek, kifejezetten kialakított e-maileket és egyéb technikák hozzáférhetnek a vállalati használható. Erősen ajánlott az összes Azure felügyeleti feladatok végrehajtása biztonságos felügyeleti munkaállomás (fűrészek) használatát. Fűrészek, amelyek egy véletlen biztonsági sérülését veszélyeztetettségének csökkentése.

Jogosultsági szintű hozzáféréssel rendelkező munkaállomások (láb) egy dedikált operációs rendszer bizalmas feladatokhoz – egyet, amely védett az Internet támadások és fenyegetési vektoroknak enged utat adja meg. Ezekről a feladatokról bizalmas és fiókokat a napi használható munkaállomásokat és eszközök elválasztó erős védelmet biztosít. Ez az elkülönítés korlátozza adathalász támadások, alkalmazás és az operációs rendszer biztonsági rések, különböző megszemélyesítési támadásokkal szemben, és hitelesítő adatokkal való visszaéléseket támadások hatását. (bevitt billentyűleütések naplózása, a Pass-the-Hash és Pass-the-Ticket)

A PAW megoldás, külön-külön hozzárendelt rendszergazdai fiók használatára neves és ajánlott eljárás kiterjesztését. A rendszergazdai fiók nem azonos a normál felhasználói fiókok. Egy PAW megbízható munkaállomás olyan kényes fiókok számára biztosít.

További információk és megvalósítási útmutatóért lásd: [jogosultsági szintű hozzáféréssel rendelkező munkaállomások](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Többtényezős hitelesítés használata

A múltban a hálózati szegélyhálózati használt vállalati adatok hozzáférésének vezérléséhez. A felhő-first, a mobileszköz-első világ identitás a vezérlő vezérlősík: az eszköz használatával IaaS-szolgáltatásokhoz való hozzáférés szabályozása bármilyen eszközről. Akkor is használhatja látható, és betekintést hol és hogyan az adatok használatban van-e. A legfontosabb feladatai közé tartoznak az előfizetések származó adatokkal való visszaéléshez és egyéb cybercrimes védelme védelme a digitális identitásokat az Azure felhasználó.

A legtöbb előnyös lépéseket, amelyek egy fiók biztonságos egyik kéttényezős hitelesítést. Kéttényezős hitelesítés módja a hitelesítő valami mellett jelszó használatával. Segít mérsékelni valaki más jelszó segítségével kezeli, aki hozzáféréssel.

[Az Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md) segítségével megakadályozhatja a adatokhoz és alkalmazásokhoz való hozzáférés mellett egyszerű bejelentkezési folyamatot a felhasználó igény szerint. Egyszerű hitelesítési beállítások – a telefonhívás, szöveges üzenetet vagy mobilalkalmazásban megjelenő értesítést számos erős hitelesítés biztosítja. Előnyben részesített módszere a felhasználók megadhatják.

Többtényezős hitelesítés használatára legegyszerűbb módja a Microsoft Authenticator mobilalkalmazás használható a Windows, iOS és Android rendszerű mobileszközök. A legújabb kiadásra frissüljön a Windows 10 és a helyszíni Active Directory, az Azure Active Directoryval (Azure AD) integrálása [vállalati Windows Hello](../active-directory/active-directory-azureadjoin-passport-deployment.md) használhat a zökkenőmentes egyszeri bejelentkezés az Azure-erőforrások. Ebben az esetben a Windows 10-es eszközök használatos a második tényezőként a hitelesítéshez.

Azure-előfizetés kezelése fiókok és a fiókok, amelyek a virtuális gépek való bejelentkezés multi-factor Authentication használata lehetővé teszi az sokkal nagyobb biztonsági szintet mint csak a jelszavak használata. Kéttényezős hitelesítés más formában is előfordulhat, hogy alkalmazhatók, de a központi telepítés bonyolult lehet, ha azok még nem éles környezetben.

Az alábbi képernyőfelvételen néhány a Azure multi-factor Authentication rendelkezésre álló lehetőségeket mutatja be:

![Többtényezős hitelesítés beállításai](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Korlátozza és a hozzáférési rendszergazdai hozzáféréssel

A fiókok, kezelheti az Azure-előfizetéshez védelme rendkívül fontos. Ezek a fiókok veszélyeztetheti ellentettjét adja, minden a más lépéseket, amelyek esetleg védelme érdekében a értékének és az adatok sértetlenségét. A közelmúltban illusztrált által a [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) belső támadások fenyegetést hatalmas bármely szervezet általános biztonságosságát.

Rendszergazdai jogok az egyéni felhasználók számára a kiértékelési hasonló ezeket a következő feltételeknek:

- Végzi, akkor a rendszergazdai jogosultságokat igénylő feladatok?
- Milyen gyakran történik a feladatokat?
- Miért a feladatok nem hajtható végre, a nevében egy másik rendszergazda valamiért van?

A dokumentum minden egyéb ismert alternatív módszer a jogosultságot, és miért minden nem elfogadható.

Csak időben történő felügyeletre használatát megakadályozza, hogy a időszakokban, ha ezeket a jogokat nincs szükség emelt szintű jogosultságokkal rendelkező fiókok szükségtelen megléte. Fiókok rendelkezik emelt szintű jogosultságokkal korlátozott ideig, hogy a rendszergazdák teheti a munkájukat. Ezeket a jogokat, majd a shift vagy a feladat végrehajtásának végén törlődnek.

Használhat [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) kezelésére, megfigyelés és vezérlés hozzáférni a szervezetében. A program segít egyéni felhasználók számára a szervezetében műveletei tudomást maradnak. Is jelent just-felügyelet az Azure AD jogosult rendszergazdák fogalma szemben. Ezek a fiókok rendszergazdai jogokkal kell rendelkeznie a lehetséges rendelkező egyének. Felhasználók típusai Lépkedjen végig az aktiválási folyamat, és jogosultságokat admin adott időtartamra.


## <a name="use-devtest-labs"></a>Használja a DevTest Labs szolgáltatásban

Az Azure labs és fejlesztői környezetek lehetővé teszi a szervezetek könnyen reagálhat tesztelési és fejlesztési nyerni számítógépnél véve a Várakozás, amely a hardver beszerzése vezet be. Sajnos Azure vagy az elfogadását gyorsítása érdekében kell ismeretét hiánya vezethet a rendszergazdát, hogy túlságosan megengedett jogok hozzárendelésekor. A kockázat véletlenül esetleg felfedi a szervezet a belső támadásokkal szemben. Egyes felhasználók előfordulhat, hogy hozzáférést sokkal több mint kell rendelkezniük.

A [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) használja [átruházásához hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC). RBAC használatával akkor is elkülönítse feladatokat a munkacsoporton belül a szerepköröket, amelyek csak a hozzáférési szintet, a felhasználóknak a munkájuk elvégzéséhez szükséges megadni. Az RBAC előre definiált szerepkörök (tulajdonos, lab-felhasználó és közreműködő) tartalmaz. Ezek a szerepkörök segítségével is külső partnerekkel jogosultságokat rendelhet hozzá, és nagy mértékben egyszerűsítheti az együttműködés.

Mivel a DevTest Labs RBAC használ, azt is létrehozhat további, [egyéni szerepkörök](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs nem csak egyszerűbbé teszi a engedélyek, egyszerűbbé teszi a kiépített környezetek folyamatának. Emellett segítséget nyújt az egyéb csoportok fejlesztési és tesztelési környezetben működő tipikus kihívásaival kezelésére. Bizonyos előkészületeket igényel, de a hosszú távú, azt fogja egyszerűbbé a csapat számára.

Az Azure DevTest Labs funkciók a következők:

- A felhasználók számára elérhető lehetőségekhez képest felügyeletét. A rendszergazda központilag kezelheti többek között az engedélyezett Virtuálisgép-méretek, a virtuális gépek és a virtuális gépek elindításakor maximális számát és a Leállítás.
- Tesztlabor-környezet létrehozása automatizálását.
- Költségek nyomon követése.
- Virtuális gépek egyszerűsített eloszlásáról együttműködési végzett munkához.
- Önkiszolgáló, amely lehetővé teszi a felhasználók a tesztkörnyezetek kiépítéséhez sablonok használatával.
- Kezelése és a használat korlátozása.

![Labor létrehozása a DevTest Labs segítségével](./media/azure-security-iaas/devtestlabs.png)

További költség nélkül társítva a DevTest Labs használatát. Felszabadul labs, házirendek, sablonok és összetevők létrehozását. Csak az Azure-erőforrások a labs, például a virtuális hálózatok, virtuális gépek és tárfiókok használt fizetnie.



## <a name="control-and-limit-endpoint-access"></a>Vezérlő és a limit végpont hozzáférés

Labs vagy éles rendszerek esetén az Azure-ban üzemeltető azt jelenti, hogy a rendszer kell lennie az interneten. Alapértelmezés szerint egy új Windows virtuális gép számára elérhető az internetről érkező RDP-portjára, és egy Linux virtuális gép számára az SSH-port megnyitásához. Lépések megtétele kitett korlát végpontokkal való jogosulatlan hozzáférés kockázatának minimalizálása érdekében szükség.

Az Azure-ban technológiák segítségével mindegyik felügyeleti végpont való hozzáférés korlátozásához. Használhatja az Azure [hálózati biztonsági csoportok](../virtual-network/security-overview.md) (NSG-k). Központi telepítés Azure Resource Manager használatakor az NSG-k korlátozhatják a minden hálózati csak a felügyeleti végpontok (RDP- vagy SSH-). Ha úgy gondolja, hogy az NSG-k, gondolja, hogy útválasztó hozzáférés-vezérlési listák. A mértékben vezérelhetik a különböző részeit az Azure-hálózatok közötti hálózati kommunikáció használhatja őket. Ez hasonlít-hálózatok létrehozásának szegélyhálózat vagy más elkülönített hálózatok. Nem vizsgálja meg a forgalmat, de a hálózati szegmentálást segítenek.


Azure, konfigurálhat egy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) a helyi hálózatról. A telephelyek közötti VPN kiterjeszti a felhőbe a helyszíni hálózat. Ez lehetővé teszi egy másik arra, hogy az NSG-k, mert az NSG-ket nem engedélyezi a hozzáférést bármely más, mint a helyi hálózaton is módosíthatja. Majd megkövetelheti, hogy a felügyeleti szolgáltatáshoz csatlakozva első az Azure-hálózatot VPN-en keresztül történik.

A pont-pont VPN beállítás akkor lehet legtöbb vonzó azokban az esetekben, ahol éles rendszerek esetén a helyszíni erőforrások az Azure-ban szorosan integrált üzemeltet.

Másik lehetőségként használhatja a [pont-pont](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) olyan esetekben, ahol a helyszíni erőforrásokhoz való hozzáférés nem igénylő rendszerek kezelni kívánt beállítást. Ezekhez a rendszerekhez el lehet különíteni a saját Azure virtuális hálózatban. A rendszergazdák is az az Azure VPN környezet a felügyeleti munkaállomás üzemeltetni.

>[!NOTE]
>Bármelyik VPN-beállítás segítségével konfigurálja újra az ACL-ek az NSG-ket nem való hozzáférés engedélyezése a felügyeleti végpontok az internetről.

Érdemes fontolóra veheti, hogy egy másik lehetőség egy [távoli asztali átjáró](../active-directory/authentication/howto-mfaserver-nps-rdg.md) központi telepítés. A központi telepítés segítségével biztonságosan csatlakozzon a távoli asztal-kiszolgálók a HTTPS PROTOKOLLOKON keresztül közben részletesebb vezérlők alkalmazása az ezeket a kapcsolatokat.

Felvenni rendelkezik elérésére funkciókat:

- Rendszergazdai beállítások kérésekre adott rendszerekből korlátozására.
- Intelligens kártyás hitelesítést vagy az Azure multi-factor Authentication.
- Ellenőrzés, hogy mely rendszerek keresztül valaki csatlakozhat az átjárón keresztül.
- Eszköz és a lemez átirányítás vezérlése.

## <a name="use-a-key-management-solution"></a>A kulcskezelési megoldással

A biztonságos kulcskezelésnek fontos szerepe van a felhőben tárolt adatok védelmében. A [Azure Key Vault](../key-vault/key-vault-whatis.md), tudja biztonságosan tárolni a titkosítási kulcsokat, és kis titkos kulcsok, például jelszavak, a hardveres biztonsági modulokkal (HSM). A még nagyobb biztonság érdekében lehetőség van arra is, hogy kulcsokat importáljon és generáljon a hardveres biztonsági modulokban.

Microsoft folyamatok a kulcsokat a FIPS 140-2 2 ellenőrzött hardveres biztonsági modulok (a hardver és a belső vezérlőprogram) szinten. A figyelő és naplózási kulcs használata Azure naplózással: átadhatja a naplókat további elemzés és a fenyegetések észlelésére az Azure vagy a biztonsági adatai és az esemény felügyeleti SIEM-rendszerbe.

Bárki, aki az Azure-előfizetés hozhat létre és használhat kulcstárolót. Bár a Key Vault elsősorban a fejlesztők és rendszergazdák, megvalósítva, és a szervezet Azure-szolgáltatások kezelése felelős rendszergazda felügyeli.


## <a name="encrypt-virtual-disks-and-disk-storage"></a>Virtuális lemezek és a lemezes tárolás titkosítása

[Az Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) címek a fenyegetését, hogy az adatlopással, illetve az, hogy érhető el, egy lemezt áthelyezésével történő jogosulatlan hozzáférést. A lemez csatolható másik rendszer egyéb biztonsági vezérlőt kihagyásával módja. Titkosítás által használt lemezre [BitLocker](https://technet.microsoft.com/library/hh831713) a Windows és a DM-crypt program segítségével a Linux operációs rendszer és az adatmeghajtók titkosítására. Az Azure Disk Encryption integrálható a Key Vault ellenőrzése és a titkosítási kulcsok kezeléséhez. Érhető el a szokásos virtuális gépek és virtuális gépek prémium szintű Storage.

További információkért lásd: [Windows és Linux IaaS virtuális gépeket az Azure Disk Encryption](azure-security-disk-encryption.md).

[Az Azure Storage szolgáltatás titkosítási](../storage/common/storage-service-encryption.md) az inaktív adatok védelmét. Ez a tárolási fiók szintjén engedélyezhető. Azt titkosítani fogja az adatokat adatközpontjainkba van írva, és a rendszer a hozzáféréskor automatikusan visszafejti. Ez a következő szituációkat ismerteti:

- Titkosítási blokkblobokat, hozzáfűző blobokat és lapblobokat
- Az archivált virtuális merevlemezek és a helyszíni Azure állapotba sablonok titkosítása
- IaaS virtuális gépeket, amelyet a VHD-k használatával hozott létre az alapul szolgáló operációsrendszer- és adatlemezek titkosítása

Mielőtt továbblép az Azure Storage Encryption, két korlátozások figyelembe vennie:

- Nem érhető el a klasszikus tárfiókokat.
- Csak a titkosítás engedélyezése után írt adatok, az titkosítja.

## <a name="use-a-centralized-security-management-system"></a>A központi biztonsági rendszer használata

A kiszolgálók javítását, konfigurációs, eseményeket, és a tevékenységek, amelyek a biztonsági aggályokon tekinthető figyelni szeretne. E szempontok kielégítéséhez, használhatja a [Security Center](https://azure.microsoft.com/services/security-center/) és [Operations Management Suite biztonsági és megfelelőségi](https://azure.microsoft.com/services/security-center/). Az operációs rendszer konfigurációs túlmutató mindkét lehetőség. Ezenkívül tartalmaznak az alkalmazás mögötti infrastruktúra, például a hálózati konfigurációs és virtuális készülék használja a konfiguráció ellenőrzése.

## <a name="manage-operating-systems"></a>Operációs rendszerek kezelése

IaaS-telepítés, továbbra is való telepítésért felelős a felügyeleti rendszerek központi telepítése, csakúgy, mint bármely más kiszolgálóra vagy munkaállomásra a környezetben. Javítás, vezethet, engedélyek és a rendszer karbantartási kapcsolódó minden más tevékenység még a felelős. A helyszíni erőforrások szorosan integrált rendszerekhez előfordulhat, hogy használni kívánt azonos eszközök és, hogy a helyszíni használja többek között a víruskereső, kártevőirtó, javítását és biztonsági mentési eljárások.

### <a name="harden-systems"></a>Rendszerek megerősítése
Azure IaaS összes virtuális gépnek kell megerősítve, így csak a telepített alkalmazások által igényelt Szolgáltatásvégpontok szolgáltatnak. A Windows virtuális gépek, kövesse a Microsoft által közzétett alapkonfigurációkat, a [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) megoldás.

Security Compliance Manager ingyenes. Használhatja a gyors konfigurálásához és a asztalok, a hagyományos adatközpontok és a privát és nyilvános felhő kezelése csoportházirend és a System Center Configuration Manager használatával.

Security Compliance Manager házirendek kész a központi telepítése és ellenőrzi a Szükségeskonfiguráció-kezelési konfigurációs csomagok biztosít. Ezek alaptervek alapuló [Microsoft biztonsági útmutatói](https://technet.microsoft.com/library/cc184906.aspx) javaslatok és az iparág ajánlott eljárások. Ezek segítségével kezelheti a konfigurációs eltéréseket, cím megfelelőségi követelményeket, és a biztonsági kockázatok csökkentése.

Security Compliance Manager segítségével importálja a jelenlegi konfigurációt a számítógépek két különböző módszer használatával. Először az Active Directory-alapú csoportházirendek importálhatja. Második, "arany főkiszolgáló" konfigurációs importálhatja a referencia-számítógép a [LocalGPO eszköz](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) biztonsági mentése a helyi csoportházirend. Importálhatja a helyi csoportházirend Security Compliance Manager.

Hasonlítsa össze az ágazatban kialakult bevált gyakorlaton szabványok, testre is szabhatja őket, és hozzon létre új házirendek és a szükséges konfiguráció kezelésének konfigurációs csomagokat. Az összes támogatott operációs rendszerhez, beleértve a Windows 10 évforduló Update és Windows Server 2016 alaptervek közzé lettek.


### <a name="install-and-manage-antimalware"></a>Telepítheti és kezelheti a kártevőirtó

Éles környezettől külön-külön szolgáltatott környezetben a kártevőirtó bővítmény segítségével védheti a virtuális gépeket, és a felhőalapú szolgáltatások. Az integrálható [az Azure Security Center](../security-center/security-center-intro.md).


[A Microsoft Antimalware](azure-security-antimalware.md) magában foglalja a szolgáltatások, mint a valós idejű védelem, ütemezett vizsgálatát, kártevő szoftverek eltávolítása, aláírás frissítések, motor frissítéseit, reporting, kizárási eseménygyűjtés, mintákat és [PowerShell-támogatás](https://msdn.microsoft.com/library/dn771715.aspx).

![Az Azure kártevőirtó](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Telepítse a legújabb biztonsági frissítéseket
Az első Azure ügyfelek helyezhetik át munkaterhelések labs és kívülre irányuló rendszerek. Ha az Azure által üzemeltetett virtuális gépek üzemeltetéséhez alkalmazásokhoz vagy szolgáltatásokhoz, elérhető-e az internethez, éberen kapcsolatos javítását. Javítás az operációs rendszer felett. Harmadik féltől származó alkalmazások a nem javított biztonsági rések is elkerülhető, ha rendelkezésre áll a javítások jó problémákhoz vezethet.

### <a name="deploy-and-test-a-backup-solution"></a>Telepítse és tesztelje a biztonsági mentési megoldás

Csakúgy, mint a biztonsági frissítések biztonsági kezelendő a megszokott módon, hogy kezeli-e bármilyen más műveletet. Ez igaz a rendszerek, amelyek a termelési környezetben kiterjesztése a felhőbe. Teszt és fejlesztési rendszerek, amelyek hasonlóak a felhasználók milyen rendelkezik hozzászoktak számára, azok a helyszíni környezetben élményt alapján visszaállítási képességeket biztosítanak biztonsági mentési stratégia kell követnie.

Termelési számítási feladatokhoz telepít át az Azure kell integrálható a meglévő biztonsági mentési megoldás, ha lehetséges. Másik lehetőségként használhatja [Azure biztonsági mentési](../backup/backup-azure-arm-vms.md) segítségével a biztonsági mentés követelményeinek.


## <a name="monitor"></a>Figyelés

[A Security Center](../security-center/security-center-intro.md) biztosít a potenciális biztonsági hiányosságok azonosítása az Azure-erőforrások biztonsági állapotát folyamatos értékelését. A javaslatok listája végigvezeti Önt a szükséges szabályozási folyamatok konfigurálásának folyamatán.

Példák erre vonatkozóan:

- Azonosítsa és eltávolítani a kártevő szoftvereket kártevőirtók kiépítése.
- Hálózati biztonsági csoportok és a virtuális gépek forgalmának ellenőrzésére vonatkozó szabályok konfigurálása.
- A támadások elleni védelemre, hogy a célkiszolgáló webalkalmazási tűzfalak kiépítése a webalkalmazások.
- Hiányzó rendszerfrissítések telepítése.
- Operációs rendszer azon konfigurációit, amelyek nem felelnek meg a javasolt alapkonfigurációkkal címzést.

A következő kép bemutatja, hogy engedélyezheti a Security Center a beállítások egy része.

![Az Azure Security Center házirendek](./media/azure-security-iaas/security-center-policies.png)

[Az Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra. Az Operations Management Suite a felhő alapú szolgáltatásként valósul meg, mert gyors és az infrastruktúrához kapcsolódó erőforrások használatára irányuló befektetéséből minimális telepíthető.

Új szolgáltatások automatikusan érkeznek, a folyamatos karbantartási így meg lehet spórolni, és frissítse a költségeket. Az Operations Management Suite is integrálható a System Center Operations Manager. Jobb kezelése érdekében a Azure munkaterhelésekhez, többek között különböző összetevői rendelkezik egy [biztonsági és megfelelőségi](../operations-management-suite/oms-security-getting-started.md) modul.

Az Operations Management Suite a biztonsági és megfelelőségi szolgáltatások segítségével a erőforrásokra vonatkozó információk megtekintése. Az információk négy fő kategóriába sorolhatók:

- **Biztonsági tartományok**: további felfedezés biztonsági rekordok időbeli. Frissítse az értékelés, a hálózati biztonsági adatai, a identitások és hozzáférések információkat és a számítógépek biztonsági események, férhet hozzá a kártevő szoftverek assessment. Gyorselérési kihasználja az Azure Security Center irányítópultot.
- **Jelentős problémák**: gyorsan azonosíthatja az aktív problémák számát és ezek a problémák súlyosságát.
- **Észlelések (előzetes verzió)**: azonosítására a támadási mintákat által a biztonsági riasztások megjelenítése, akkor fordulhat elő, az erőforrásokon.
- **A fenyegetés eszközintelligencia**: azonosítására a támadási mintákat által megjelenítése kimenő kártékony IP-forgalom, ha ezeket az IP-címekről érkező megjelenítő és a kártevő-fenyegetés típusú kiszolgálók száma.
- **Általános biztonsági lekérdezések**: a leggyakrabban használt biztonsági lekérdezések, amelyek segítségével figyelheti a környezet listájának megtekintéséhez. Egy lekérdezések, a **keresési** panel nyílik meg, és az, hogy a lekérdezés eredményeit jeleníti meg.

Az alábbi képernyőfelvételen az adatokat az Operations Management Suite megjelenítő példáját mutatja be.

![Az Operations Management Suite biztonsági alapterveket](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Az Azure biztonsági ajánlott eljárásairól és mintáiról](security-best-practices-and-patterns.md)
