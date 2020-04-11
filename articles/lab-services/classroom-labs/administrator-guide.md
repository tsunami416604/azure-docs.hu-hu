---
title: Azure Lab Services – Rendszergazdai útmutató | Microsoft dokumentumok
description: Ez az útmutató az Azure Lab Services használatával laborfiókokat létrehozó és kezelő rendszergazdákat segíti.
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
ms.date: 12/19/2019
ms.author: spelluru
ms.openlocfilehash: 7ce7ef15f0bf13182e4799fb640e83136d0d4695
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115026"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – Rendszergazdai útmutató
Az egyetem felhőalapú erőforrásait kezelő informatikai rendszergazdák általában felelősek az iskola laborfiókjának beállításáért. A tesztkörnyezet-fiók beállítása után a rendszergazdák vagy az oktatók a tesztkörnyezet-fiókban található tantermi laborokat hoznak létre. Ez a cikk magas szintű áttekintést nyújt az azure-erőforrásokról és a létrehozásukhoz.

![Az Azure-erőforrások magas szintű nézete laborfiókban](../media/administrator-guide/high-level-view.png)

- Az osztálytermi laborok az Azure Lab Services tulajdonában lévő Azure-előfizetésen belül találhatók.
- A laborfiókok, a megosztott képgaléria és a képverziók az előfizetésen belül találhatók.
- A tesztkörnyezet-fiók és a megosztott képtár ugyanabban az erőforráscsoportban rendelkezhet. Ebben az ábrán különböző erőforráscsoportokban vannak. 

## <a name="subscription"></a>Előfizetés
Az egyetem egy vagy több Azure-előfizetéssel rendelkezik. Az előfizetés a számlázás és a biztonság kezelésére szolgál az összes Azure-erőforrások\szolgáltatások, amelyek a benne használt, beleértve a labor fiókok.

A tesztkörnyezet-fiók és az előfizetés közötti kapcsolat azért fontos, mert:

- A számlázás a tesztkörnyezet-fiókot tartalmazó előfizetésen keresztül történik.
- Az előfizetés Azure Active Directory (AD) bérlői hozzáférését biztosíthatja a felhasználóknak az Azure Lab Services szolgáltatáshoz. Hozzáadhat egy felhasználót tesztkörnyezet-fiók tulajdonosaként\közreműködő, tantermi laborlétrehozó vagy tantermi labor tulajdonos.

Az osztálytermi laborok és virtuális gépeik (VM-ek) az Azure Lab Services tulajdonában lévő előfizetésen belül kerülnek az Ön számára.

## <a name="resource-group"></a>Erőforráscsoport
Az előfizetés egy vagy több erőforráscsoportot tartalmaz. Az erőforráscsoportok az Azure-erőforrások logikai csoportjainak létrehozásához használatosak, amelyek ugyanazon a megoldáson belül használatosak.  

Laborfiók létrehozásakor konfigurálnia kell a laborfiókot tartalmazó erőforráscsoportot. 

[Megosztott képtár](#shared-image-gallery)létrehozásakor erőforráscsoport is szükséges. Dönthet úgy, hogy a tesztkörnyezet-fiókot és a megosztott képgalériát két külön erőforráscsoportba helyezi, ami jellemző, ha a képgalériát különböző megoldások között kívánja megosztani. Vagy dönthet úgy is, hogy ugyanabba az erőforráscsoportba helyezi őket.

Laborfiók létrehozásakor automatikusan létrehozhat és csatolhat egy megosztott képgalériát egyszerre.  Ez a beállítás azt eredményezi, hogy a tesztkörnyezet-fiók és a megosztott képtár jön létre külön erőforráscsoportokban. Ez a viselkedés az oktatóanyagban leírt lépések használatakor jelenik meg: [Megosztott képgaléria konfigurálása a tesztkörnyezet-fiók létrehozásakor](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A cikk tetején található kép is ezt a konfigurációt használja. 

Azt javasoljuk, hogy az erőforráscsoportok szerkezetének megtervezéséhez előre fordíts időt, mivel létrehozása után *nem* lehet módosítani egy tesztkörnyezet-fiók vagy megosztott képgaléria erőforráscsoportját. Ha módosítania kell ezeknek az erőforrásoknak az erőforráscsoportját, törölnie kell, majd újra létre kell hoznia a tesztkörnyezet-fiókot és\vagy a megosztott képgalériát.

## <a name="lab-account"></a>Labor-fiók
A laborfiók egy vagy több tantermi labor tárolójaként szolgál. Az Azure Lab Services első lépésekén gyakori, hogy csak egyetlen laborfiókkal rendelkezik. A labor használat méretezése, később dönthet úgy, hogy hozzon létre további tesztkörnyezet-fiókok.

Az alábbi lista azokat a forgatókönyveket emeli ki, amelyekben egynél több tesztkörnyezet-fiók hasznos lehet:

- **Különböző házirend-követelmények kezelése az osztálytermi laborokban** 
    
    Laborfiók beállításakor olyan szabályzatokat állít be, amelyek a laborfiók alatt az *összes* tantermi laborra vonatkoznak, például:
    - Az Azure virtuális hálózat megosztott erőforrásokkal, amelyek az osztályteremben labor ban férhet hozzá. Például előfordulhat, hogy egy sor tantermi laborok, amelyek hozzáférést igényelnek egy megosztott adatkészlet egy virtuális hálózaton belül.
    - A virtuális gép (VM) lemezképek, amelyek az osztályteremben laborok virtuális gépek létrehozásához használható. Például előfordulhat, hogy egy tantermi laborok, amelyek hozzáférést igényelnek a [Data Science VM linuxos](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) piactér-rendszerképhez. 
    
    Ha olyan tantermi laborokkal rendelkezik, amelyek egymástól egyedi házirend-követelményeket támasztanak, hasznos lehet külön laborfiókokat létrehozni ezeknek az osztálytermi laboroknak a külön-külön történő kezeléséhez.

- **Külön költségvetés laborfiók szerint**
  
    Ahelyett, hogy az összes tantermi labor költséget egyetlen laborfiókon keresztül jelenti, szüksége lehet egy világosabban elkülönített költségvetésre. Létrehozhat például laborfiókokat az egyetem matematikai osztályához, számítógép-tudományi osztályához stb., hogy elkülönítse a költségvetést a részlegek között.  Ezután megtekintheti az egyes tesztkörnyezet-fiókok költségét az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)használatával.
    
- **Kísérleti laborok elkülönítése az aktív\termelési laboroktól**
  
    Előfordulhat, hogy olyan esetekben, ahol szeretné teszten házirend-módosítások at egy tesztkörnyezet-fiók potenciálisan hatással aktív\termelési laborok. Az ilyen típusú forgatókönyv, egy külön laborfiók létrehozása próbacélokra lehetővé teszi a módosítások elkülönítését. 

## <a name="classroom-lab"></a>Tantermi labor
Az osztálytermi labor virtuális gépeket (VM-eket) tartalmaz, amelyek mindegyike egyetlen tanulóhoz van rendelve. Általánosságban elmondható, hogy számíthat a következőkre:

- Minden osztályhoz egy tantermi labor tartozik.
- Hozzon létre egy új tantermi laborok minden félévben (vagy minden egyes időkeretben az osztály kínálnak). Általában az osztályok, amelyek az azonos képigényeket, használjon [megosztott képgalériát](#shared-image-gallery) a képek újra felhasználása laborok és szemeszter között.

Vegye figyelembe a következő pontokat, amikor meghatározza, hogyan strukturálja az osztálytermi laborjait:

- **Az osztályterem-laborban lévő összes virtuális gép ugyanazzal a lemezképpel van telepítve, mint a közzétett**

    Ennek eredményeképpen, ha egy osztály, amely előírja, hogy a különböző laborképek et közzé kell tenni egy időben, külön tantermi laborok kell létrehozni mindegyikhez.
  
- **A használati kvóta a labor szintjén van beállítva, és a tesztkörnyezet összes felhasználójára vonatkozik**
    
    Különböző kvóták beállítása a felhasználók számára, létre kell hoznia külön tantermi labs. A kvóta beállítása után azonban több órát is hozzáadhat egy adott felhasználóhoz.
  
- **Az indítási vagy leállítási ütemezés a labor szintjén van beállítva, és a laboron belüli összes virtuális gépre vonatkozik**

    Az előző ponthoz hasonlóan, ha különböző ütemezéseket kell beállítania a felhasználók számára, külön tantermi laborokat kell létrehoznia. 

## <a name="shared-image-gallery"></a>Megosztott képgaléria
A megosztott képgaléria egy tesztkörnyezet-fiókhoz van csatolva, és központi tárházként szolgál a képek tárolásához. A rendszer egy képet a katalógusba ment, amikor egy oktató úgy dönt, hogy egy tantermi labor sablon virtuális gépéről (VM) exportál. Minden alkalommal, amikor egy oktató módosítja a sablon virtuális gép és az export, a rendszerkép új verziói mentésre, miközben a korábbi verziók.

Az oktatók közzétehetik a képverziót a megosztott képgalériából, amikor új tantermi labort hoznak létre. Bár a katalógus egy kép több verzióját is tárolhatja, az oktatók csak a legújabb verziót választhatják ki a tesztkörnyezet létrehozása során.

Megosztott képgaléria egy opcionális erőforrás, amely nem feltétlenül kell azonnal, ha csak néhány tantermi laborok kezdve. A megosztott képgaléria használata azonban számos előnnyel jár, amelyek hasznosak, mivel több tantermi labort szeretne:

- **Lehetővé teszi a sablon virtuálisgép-lemezképverzióinak mentését és kezelését**

    Hasznos, ha egyéni lemezképet hoz létre, vagy módosítja (szoftver, konfiguráció és így tovább) egy képet a nyilvános piactér-galériából.  Például gyakori, hogy az oktatók különböző szoftverek\eszközök telepítését követelik meg. Ahelyett, hogy a diákoknak manuálisan kellene telepíteniük ezeket az előfeltételeket, a sablon virtuálisgép-lemezkép különböző verziói exportálhatók egy megosztott képtárba. Ezek a lemezképek ezután új tantermi laborok létrehozásakor használhatók.
- **Lehetővé teszi a sablon virtuálisgép-lemezképeinek megosztását\újrafelhasználását az osztálytermi laborokban**

    Mentheti és újra felhasználhatja a képet, így nem kell minden új tantermi labor létrehozásakor teljesen konfigurálnia a képet. Ha például több osztályt kínálnak, amelyekugyanazt a lemezképet igényelik, ezt a képet csak egyszer kell létrehozni, és exportálni kell a megosztott képgalériába, hogy meg osztható legyen az osztálytermi laborok között.
- **Biztosítja a kép replikációs rendelkezésre állását**

    Amikor egy tantermi laborból menti a megosztott képgalériába, a rendszer automatikusan replikálja a képet az azonos földrajzi területen belüli más [területekre.](https://azure.microsoft.com/global-infrastructure/regions/) Abban az esetben, ha egy régióban van egy kimaradás, a rendszerkép közzététele az osztályteremben laborban nem érinti, mivel egy más régióból származó lemezkép replikája használható.  Virtuális gépek közzététele több replikák is segíthet a teljesítmény.

A megosztott képek logikus csoportosításához több lehetőség közül választhat:

- Hozzon létre több megosztott képgalériát. Minden tesztkörnyezet-fiók csak egy megosztott képtárhoz tud csatlakozni, ezért ez a beállítás több tesztkörnyezet-fiókot is létre kell hoznia.
- Vagy használhat egyetlen megosztott képgalériát, amelyet több tesztkörnyezet-fiók oszt meg. Ebben az esetben minden egyes tesztkörnyezet-fiók engedélyezheti, hogy csak azokat a képeket, amelyek a benne található tantermi laborok.

## <a name="naming"></a>Elnevezés
Az Azure Lab Services első lépéseisorán azt javasoljuk, hogy hozzon létre elnevezési konvenciókat az erőforráscsoportok, a laborfiókok, az osztálytermi laborok és a megosztott képgaléria számára. Bár a létrehozandó elnevezési konvenciók egyediek lesznek a szervezet igényeinek megfelelően, az alábbi táblázat általános irányelveket vázol fel.

| Erőforrás típusa | Szerepkör | Javasolt minta | Példák |
| ------------- | ---- | ----------------- | -------- | 
| Erőforráscsoport | Egy vagy több tesztkörnyezet-fiókot és egy vagy több megosztott képgalériát tartalmaz | \<szervezet rövid\>-\<\>név környezet -rg<ul><li>**A szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>**A környezet** azonosítja az erőforrás környezetét, például a Kísérleti vagy az Éles</li><li>**Rg** az erőforrástípust jelenti: erőforráscsoport.</li></ul> | contosouniversitylabs-rg<br/>contosouniversitylabs-pilot-rg<br/>contosouniversitylabs-prod-rg |
| Labor-fiók | Egy vagy több labort tartalmaz | \<szervezet rövid\>-\<\>név környezet -la<ul><li>**A szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>**A környezet** azonosítja az erőforrás környezetét, például a Kísérleti vagy az Éles</li><li>**La** jelentése az erőforrás típusa: labor számla.</li></ul> | contosouniversitylabs-la<br/>mathdeptlabs-la<br/>sciencedeptlabs-pilot-la<br/>sciencedeptlabs-prod-la |
| Tantermi labor | Egy vagy több virtuális gépet tartalmaz |\<osztálynév\>-\<időkeretoktatói\>-\<azonosítója\><ul><li>**Az osztály neve** azonosítja a labor által támogatott osztály nevét.</li><li>**Az időkeret** azonosítja azt az időkeretet, amelyben az osztály fel van ajánlva.</li>**Az oktatási azonosító** azonosítja a labort birtokló oktatót.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Megosztott képgaléria | Egy vagy több virtuálisgép-lemezkép-verziót tartalmaz | \<szervezet rövid\>név galéria | contosouniversitylabsgaléria |

Az azure-erőforrások elnevezésével kapcsolatos további információkért tekintse [meg az Azure-erőforrások elnevezési konvencióit.](/azure/architecture/best-practices/naming-conventions)

## <a name="regionslocations"></a>Régiók\helyek

Az Azure Lab Services erőforrásainak beállításakor meg kell adnia az erőforrást üzemeltető adatközpont egy régióját (vagy helyét). Az alábbiakban további részleteket arégió hatása az egyes erőforrások at egy labor beállítása.

### <a name="resource-group"></a>Erőforráscsoport

A régió határozza meg azt az adatközpontot, ahol az erőforráscsoport adatai tárolódnak. Az erőforráscsoportban található Azure-erőforrások a szülőtől eltérő régiókban lehetnek.

### <a name="lab-account"></a>Labor-fiók

A tesztkörnyezet-fiók helye azt a régiót jelzi, amelyben ez az erőforrás létezik.  

### <a name="classroom-lab"></a>Tantermi labor
    
Az osztálytermi labor helye a következő tényezőktől függ:

  - **A laborfiók egy virtuális hálózatra (VNet) koncettedik**
  
    A tesztkörnyezet-fiók egy virtuális hálózat, ha ugyanabban a régióban vannak [társviszonyban.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network)  Ha egy tesztkörnyezet-fiók társviszonyban van egy virtuális hálózattal, tantermi laborok automatikusan létre jönnek ugyanabban a régióban, mint a laborfiók és a virtuális hálózat.

    > [!NOTE]
    > Ha egy tesztkörnyezet-fiók társviszonyban van egy virtuális hálózattal, a labor létrehozója számára a **labor helyének kiválasztására** beállítás le van tiltva. Erről a beállításról további információ található a cikkben: [A labor készítője kiválaszthatja a labor helyét.](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)
    
  - **Nincs virtuális hálózat társviszonyban, ***és*** a labor alkotók nem szabad kiválasztani a labor helyét**
  
    Ha **nincs** virtuális hálózat társviszonyban a *laborfiók* és a [tesztkörnyezet alkotói **nem** szabad kiválasztani a labor helyét,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)tantermi laborok automatikusan létre jön nek egy olyan régióban, amely rendelkezik a virtuális gép kapacitása.  Pontosabban az Azure Lab Services olyan régiókban keresi a rendelkezésre [állást, amelyek a laborfiókkal azonos földrajzi helyen találhatók.](https://azure.microsoft.com/global-infrastructure/regions)

  - **Nincs virtuális hálózat társviszonyban, ***és*** a labor alkotók számára megengedett, hogy válassza ki a labor helyét**
       
    Ha **nincs** virtuális hálózat társviszony-létesített és [a tesztkörnyezet alkotói számára lehetővé teszik, hogy válassza ki a labor helyét,](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)a helyek, amelyeket a tesztkörnyezet létrehozója a rendelkezésre álló kapacitás alapján.

> [!NOTE]
> Annak érdekében, hogy egy régió ban elegendő virtuális gép kapacitása legyen, fontos, hogy először kérje a kapacitást a laborfiókon keresztül vagy a labor létrehozásakor.

Általános szabály, hogy egy erőforrás régióját a felhasználókhoz legközelebb eső régióra állítsa be. Az tantermi laborok esetében ez azt jelenti, hogy létre kell hoznunk a diákokhoz legközelebb eső tantermi labort. Az online tanfolyamok, ahol a diákok találhatók a világ minden tájáról, meg kell használni a legjobb ítéletet, hogy hozzon létre egy osztályteremben labor, amely központi elhelyezkedésű. Vagy ossza fel az osztályt több tantermi laborra a diákok régiója alapján.

### <a name="shared-image-gallery"></a>Megosztott képgaléria

A régió azt a forrásrégiót jelzi, ahol az első képverzió tárolódik, mielőtt automatikusan replikálni a célterületekre.

## <a name="vm-sizing"></a>Virtuális gép méretezése
Amikor a rendszergazdák vagy a laborkészítők létrehoznak egy tantermi labort, a következő virtuálisgép-méretek közül választhatnak az osztályuk igényei alapján. Ne feledje, hogy az elérhető számítási méretek attól függ, hogy a régió, ahol a tesztkörnyezet fiók található:

| Méret | Jellemzők | Adatsorozat | Javasolt használat |
| ---- | ----- | ------ | ------------- |
| Kicsi| <ul><li>2 mag</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a legalkalmasabb a parancssori, webböngésző, alacsony forgalmú webszerverek, kis és közepes adatbázisok megnyitásához. |
| Közepes | <ul><li>4 mag</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a legalkalmasabb relációs adatbázisokhoz, memórián belüli gyorsítótárazáshoz és elemzésekhez. |
| Közepes (beágyazott virtualizáció) | <ul><li>4 mag</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Ez a méret a legalkalmasabb relációs adatbázisokhoz, memórián belüli gyorsítótárazáshoz és elemzésekhez.  Ez a méret támogatja a beágyazott virtualizációt is. |
| Nagy | <ul><li>8 mag</li><li>32 GB RAM</li></ul>  | [Standard_DC8_v2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a legalkalmasabb olyan alkalmazásokhoz, amelyeknek gyorsabb processzorokra, jobb helyi lemezteljesítményre, nagy adatbázisokra, nagy memória-gyorsítótárakra van szükségük.  Ez a méret támogatja a beágyazott virtualizációt is. |
| Kis GPU (képi megjelenítés) | <ul><li>6 mag</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ez a méret a legalkalmasabb távoli vizualizációra, streamelésre, játékra, kódolásra olyan keretrendszerek használatával, mint az OpenGL és a DirectX. |
| Kis GPU (számítás) | <ul><li>6 mag</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ez a méret a legalkalmasabb számítógép-igényes alkalmazásokhoz, mint a mesterséges intelligencia és a deep learning. |
| Közepes GPU (képi megjelenítés) | <ul><li>12 mag</li><li>112 GB RAM</li></ul>  | [Standard_NC12](https://docs.microsoft.com/azure/virtual-machines/nc-series) | Ez a méret a legalkalmasabb távoli vizualizációra, streamelésre, játékra, kódolásra olyan keretrendszerek használatával, mint az OpenGL és a DirectX. |

## <a name="manage-identity"></a>Identitás kezelése
Az [Azure szerepköralapú hozzáférés-vezérlésével](https://docs.microsoft.com/azure/role-based-access-control/overview)a következő szerepkörök rendelhetők hozzá a laborfiókokhoz és az osztálytermi laborokhoz való hozzáféréshez:

- **Tesztkörnyezetfiók tulajdonosa**

    A tesztkörnyezet-fiókot létrehozó rendszergazda automatikusan hozzáadódik a tesztkörnyezet-fiók **tulajdonosi** szerepköréhez.  A **Tulajdonos** szerepkörhöz rendelt rendszergazda a következőket teheti:
     - Módosítsa a tesztkörnyezet-fiók beállításait.
     - Más rendszergazdáknak tulajdonosként vagy közreműködőként hozzáférést biztosíthat a tesztkörnyezet-fiókhoz. 
     - Hozzáférést biztosítaz oktatóknak az oktatói laborokhoz alkotókként, tulajdonosokként vagy közreműködőkként.
     - A laborfiókban található összes tantermi labor létrehozása és kezelése.

- **Laborfiók közreműködője**

    A **közreműködői** szerepkörhöz rendelt rendszergazda:
    - Módosítsa a tesztkörnyezet-fiók beállításait.
    - A laborfiókon belüli összes tantermi labor létrehozása és kezelése.
    
    Azonban *nem* adhatnak hozzáférést más felhasználóknak sem a laborfiókokhoz, sem az osztálytermi laborokhoz.

- **Tantermi labor készítője**

    Tantermi laborok létrehozásához egy laborfiókon belül, az oktatónak a **Lab Creator** szerepkör tagjának kell lennie.  Amikor egy oktató létrehoz egy tantermi labort, a program automatikusan hozzáadja őket a labor tulajdonosaként.  Tekintse meg az oktatóanyagot arról, hogyan [adhat hozzá felhasználót a **Lab Creator** szerepkörhöz.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role) 

- **Tantermi labor tulajdonosa\közreműködő**
  
    Az oktatók megtekinthetik és módosíthatják az osztálytermi labor beállításait, ha egy labor **tulajdonosának** vagy **közreműködői** szerepkörének tagjai; a laborfiók **Olvasó** szerepkörének is tagja kell lenniük.

    A **tesztkörnyezet tulajdonosa** és **közreműködői** szerepkörei közötti egyik legfontosabb különbség az, hogy egy közreműködő *nem* adhat hozzáférést más felhasználóknak a labor kezeléséhez – csak a tulajdonosok adhatnak hozzáférést más felhasználóknak a tesztkörnyezet kezeléséhez.
    
    Emellett egy oktató *nem hozhat* létre új tantermi teszteredményeket, kivéve, ha ők is a Lab **Creator** szerepkör tagjai.

- **Megosztott képgaléria**
    
    Amikor megosztott képgalériát csatol egy tesztkörnyezet-fiókhoz, a tesztkörnyezet-fiók tulajdonosai\közreműködők és a tesztkörnyezet alkotói\tulajdonosok\közreműködők automatikusan hozzáférést kapnak a katalógusban lévő képek megtekintéséhez és mentéséhez. 

Íme néhány tipp a szerepkörök hozzárendeléséhez:
   - Általában csak a rendszergazdák lehetnek tagjai a tesztkörnyezet-fiók **tulajdonosi** vagy **közreműködői** szerepkörök; lehet, hogy egynél több tulajdonos\közreműködő is rendelkezik.

   - Annak érdekében, hogy egy oktató képes legyen új tantermi laborokat létrehozni és kezelni az általuk létrehozott laborokat; csak hozzáférést kell hozzárendelnie a **Lab Creator** szerepkörhöz.
   
   - Annak érdekében, hogy egy oktató képes legyen bizonyos tantermi laborok kezelésére, de *nem* új laborok létrehozására; hozzáférést kell hozzárendelnie a **tulajdonosi** vagy **közreműködői** szerepkörhöz az általuk kezelt tantermi laborok mindegyikéhez.  Előfordulhat például, hogy egy professzornak és egy tanársegédnek is engedélyezni szeretné, hogy egy tantermi labor társtulajdonában legyen.  Tekintse meg az útmutatót arról, hogyan [adhat hozzá egy felhasználót tulajdonosként egy tantermi laborhoz.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner)

## <a name="pricing"></a>Díjszabás

### <a name="azure-lab-services"></a>Azure Lab Services
Az Azure Lab Services díjszabását a következő cikk ismerteti: [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

Ha a lemezképek tárolására és kezelésére kívánja használni, figyelembe kell vennie a megosztott képgaléria díjszabását is. 

### <a name="shared-image-gallery"></a>Megosztott képgaléria
Megosztott képgaléria létrehozása és csatolása a tesztkörnyezet-fiókhoz ingyenes. A költségek nem merülnek fel, amíg nem menti a képverziót a katalógusba. Általában a megosztott képgaléria használatának díjszabása meglehetősen elhanyagolható, de fontos megérteni, hogyan számítják ki, mivel nem szerepel az Azure Lab Services díjszabásában.  

#### <a name="storage-charges"></a>Tárolási díjak
A lemezképek verzióinak tárolásához a megosztott képgaléria szabványos HDD-vel kezelt lemezeket használ. A használt HDD-kezelt lemez mérete a tárolt lemezkép méretétől függ. Az árképzés díjszabását a következő cikkben [tekintheti](https://azure.microsoft.com/pricing/details/managed-disks/)meg.


#### <a name="replication-and-network-egress-charges"></a>Replikációs és hálózati kimenő díjak
Amikor egy tantermi labor sablonvirtuális gépével (VM) menti a lemezképet, az Azure Lab Services először egy forrásrégióban tárolja, majd automatikusan replikálja a forráslemezkép verzióját egy vagy több célrégióba. Fontos megjegyezni, hogy az Azure Lab Services automatikusan replikálja a forráslemez verzióját az összes [célrégióban a földrajzi,](https://azure.microsoft.com/global-infrastructure/regions/) ahol az osztályteremben labor található. Ha például az osztálytermi labor az Egyesült Államok földrajzi területén található, a rendszer az Egyesült Államokban meglévő nyolc régió mindegyikére replikálja a lemezkép-verziót.

A hálózati kimenő forgalom díja akkor jelentkezik, amikor egy lemezkép-verzió replikálása a forrásrégióból további célterületekre történik. A felszámított összeg a lemezkép-verzió méretétől függ, amikor a rendszerkép adatai először a forrásrégióból kerülnek átvitelre.  Az árképzésrészleteiről a következő cikkben olvashat: [Sávszélesség-díjszabásrészletei](https://azure.microsoft.com/pricing/details/bandwidth/).

[Oktatási megoldások](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) az ügyfelek lehet eltekinteni a kifizető kimenő díjakat. További információért forduljon a fiókkezelőjéhez.  További információt a csatolt dokumentum **GYIK** részében talál, különösen a "Milyen adatátviteli programok léteznek a tudományos ügyfelek számára, és hogyan minősíthetem?".

#### <a name="pricing-example"></a>Példa árképzésre
A fent leírt díjszabás újrabedugni, nézzünk meg egy példát a sablon virtuálisgép-kép mentése a megosztott képgalériába. Tegyük fel a következő eseteket:

- Egyetlen egyéni virtuálisgép-lemezképet kap.
- A kép két verzióját menti.
- A laborja az USA-ban van, ahol összesen nyolc régió van.
- Minden lemezkép verzió32 GB méretű; ennek eredményeként, a HDD-kezelt lemez ára 1,54 $ havonta.

A teljes költséget a következőként becsülik meg:

Képek száma × verziók száma × replikák száma × felügyelt lemez ára

Ebben a példában a költség:

1 egyéni kép (32 GB) x 2 verzió x 8 amerikai régió x $1.54 = $24.64 havonta

#### <a name="cost-management"></a>Cost Management
Fontos, hogy a tesztkörnyezet-fiók rendszergazdája a költségek kezeléséhez rendszeresen a szükségtelen lemezkép-verziók a katalógusból. 

A költségek csökkentése érdekében ne törölje az adott régiókra irányuló replikációt (ez a lehetőség a megosztott képgalériában található). A replikációs módosítások káros hatással lehetnek az Azure Lab Service azon képességére, hogy virtuális gépeket tegyen közzé a megosztott képgalériában mentett lemezképekről.

## <a name="next-steps"></a>További lépések
Tekintse meg az oktatóanyagban a laborfiók és a labor létrehozásához adott lépésenkénti utasításokat: [Útmutató beállítása](tutorial-setup-lab-account.md)
