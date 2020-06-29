---
title: Azure Lab Services – rendszergazdai útmutató | Microsoft Docs
description: Ez az útmutató segít a rendszergazdáknak, hogy Azure Lab Services használatával hozzanak létre és kezeljenek labor-fiókokat.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ad3bc110d93efb5b735f77fb8a0b2af9e4f9a7cd
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85444148"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – rendszergazdai útmutató
Az Egyetem Felhőbeli erőforrásait kezelő informatikai rendszergazdák általában felelősek az iskolájuk laboratóriumi fiókjának beállításához. A labor-fiók beállítása után a rendszergazdák vagy a pedagógusok létrehozzák a labor-fiókban található tantermi laborokat. Ez a cikk átfogó áttekintést nyújt az érintett Azure-erőforrásokról és a létrehozásához szükséges útmutatóról.

![Azure-erőforrások magas szintű megtekintése egy labor-fiókban](./media/administrator-guide/high-level-view.png)

- A tanterem Labs egy Azure Lab Services által birtokolt Azure-előfizetésen belül üzemel.
- A labor fiókok, a megosztott képkatalógus és a képverziók az előfizetésen belül találhatók.
- A labor-fiókját és a megosztott képtárat is megtekintheti ugyanabban az erőforráscsoporthoz. Ebben a diagramban különböző erőforráscsoportok találhatók. 

## <a name="subscription"></a>Előfizetés
Az Egyetem egy vagy több Azure-előfizetéssel rendelkezik. Az előfizetés segítségével kezelhető a számlázás és a biztonság az összes olyan Azure-resources\services esetében, amelyet a rendszer használ, beleértve a laboratóriumi fiókokat is.

A labor-fiók és az előfizetése közötti kapcsolat azért fontos, mert:

- A számlázás a labor-fiókot tartalmazó előfizetésen keresztül történik.
- Az előfizetés Azure Active Directory (AD) bérlője számára elérhetővé teheti a felhasználókat Azure Lab Serviceshoz. Hozzáadhat egy felhasználót labor-fiók owner\contributor, osztálytermi labor creatorként vagy tantermi labor tulajdonosként.

A tantermi laborokat és a virtuális gépeket (VM) felügyeli és üzemelteti a Azure Lab Services által birtokolt előfizetésen belül.

## <a name="resource-group"></a>Erőforráscsoport
Egy előfizetés egy vagy több erőforráscsoportot tartalmaz. Az erőforráscsoportok használatával létrehozhatók az Azure-erőforrások logikai csoportjai, amelyek ugyanabban a megoldásban együtt használatosak.  

Labor-fiók létrehozásakor be kell állítania azt az erőforráscsoportot, amely a labor-fiókot tartalmazza. 

[Megosztott képgyűjtemény](#shared-image-gallery)létrehozásakor is szükség van egy erőforráscsoporthoz. Dönthet úgy, hogy két különálló erőforráscsoporthoz helyezi el a labor-fiókját és a közös rendszerkép-katalógust, amely akkor jellemző, ha a képtárat különböző megoldásokban szeretné megosztani. Vagy dönthet úgy is, hogy ugyanabba az erőforráscsoporthoz helyezi őket.

Amikor létrehoz egy tesztkörnyezet-fiókot, automatikusan létrehozhat és csatolhat megosztott képtárat.  Ez a beállítás azt eredményezi, hogy a labor fiók és a megosztott rendszerkép-katalógus külön erőforráscsoportok között jön létre. Ezt a viselkedést a jelen oktatóanyagban ismertetett lépések használatakor fogja látni: a [megosztott rendszerkép-gyűjtemény konfigurálása a tesztkörnyezet létrehozásakor](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A cikk tetején található rendszerkép ezt a konfigurációt is használja. 

Javasoljuk, hogy az erőforráscsoportok szerkezetének megtervezéséhez növelje meg az idő előtti időt, mivel a létrehozása után *nem* lehet módosítani a labor-fiók vagy a megosztott képgyűjtemény erőforráscsoportot. Ha módosítania kell az erőforrás-csoportot ezen erőforrások esetében, törölnie kell, majd újra létre kell hoznia a tesztkörnyezet and\or megosztott képtárat.

## <a name="lab-account"></a>Labor-fiók

A labor-fiókok egy vagy több osztályterem Labs számára tárolóként szolgálnak. A Azure Lab Services első lépéseiben gyakran csak egyetlen labor-fiókkal rendelkezhet. A labor használatának skálázása után később további labor-fiókokat is létrehozhat.

Az alábbi lista olyan forgatókönyveket mutat be, amelyekben több labor-fiók is hasznos lehet:

- **Különböző házirend-követelmények kezelése a tanterem Labs-ben**

    Labor-fiók beállításakor olyan házirendeket állít be, amelyek az Lab-fiókban az *összes* osztályterem Labs-re érvényesek, például:
    - Az Azure-beli virtuális hálózat olyan megosztott erőforrásokkal rendelkezik, amelyekhez a tanterem Lab hozzáférhet. Előfordulhat például, hogy olyan osztályterem Labs-készlettel rendelkezik, amelynek hozzáférésre van szüksége egy megosztott adatkészlethez egy virtuális hálózaton belül.
    - Azok a virtuálisgép-lemezképek, amelyeket a tanterem Labs használhat a virtuális gépek létrehozásához. Előfordulhat például, hogy olyan osztályterem Labs-készlettel rendelkezik, amelynek hozzáférésre van szüksége a [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) Marketplace-rendszerképhez.

    Ha olyan tantermi laborokkal rendelkezik, amelyek egymástól egyedi házirend-követelményekkel rendelkeznek, érdemes lehet külön labor-fiókokat létrehozni a tantermi laborok külön történő kezeléséhez.

- **Elkülönített költségvetés labor-fiókkal**
  
    Ahelyett, hogy az összes osztályterem Lab-költséget egyetlen labor-fiókon keresztül kellene jelentenie, egy világosabban elkülönített költségvetést kell használnia. Például létrehozhat labor-fiókokat az egyetemi matematikai részleg, a számítástechnikai részleg és így tovább, hogy elkülönítse a költségvetést a részlegek között.  Ezután megtekintheti az egyes labor-fiókok költségeit [Azure Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)használatával.

- **Kísérleti laborok elkülönítése a active\production Labs-ből**
  
    Előfordulhat, hogy olyan esetekre van szükség, amikor egy labor-fiókra vonatkozó házirend-módosításokat kíván használni anélkül, hogy ez hatással lenne a active\production Labs-ra. Ilyen esetben a tesztelési célokra külön labor-fiók létrehozása lehetővé teszi a módosítások elkülönítését. 

## <a name="classroom-lab"></a>Tantermi labor

Az osztályterem Lab olyan virtuális gépeket tartalmaz, amelyek mindegyike egyetlen tanulóhoz van rendelve.  Általánosságban elmondható, hogy az alábbiakra számíthat:

- Minden osztályhoz egy osztályterem labor tartozik.
- Hozzon létre egy új osztályterem Labs-készletet minden félévben (vagy az osztály minden egyes időkerete esetén). Az azonos Rendszerképbeli igényekkel rendelkező osztályok esetében általában [megosztott](#shared-image-gallery) képkatalógust kell használni a rendszerképek és a félévek közötti újrafelhasználáshoz.

A következő szempontokat kell figyelembe vennie az osztályterem Labs szerkezetének meghatározásakor:

- **A tantermi laborban lévő összes virtuális gép a közzétettvel megegyező képpel van telepítve**

    Ennek eredményeképpen, ha olyan osztállyal rendelkezik, amelyhez különböző tesztkörnyezet-lemezképek is közzé lesznek téve, külön tantermi laborokat kell létrehozni mindegyikhez.
  
- **A használati kvóta a labor szintjén van beállítva, és a laborban lévő összes felhasználóra érvényes.**

    Ha különböző kvótákat szeretne beállítani a felhasználók számára, külön tantermi laborokat kell létrehoznia. A kvóta beállítása után azonban lehetőség van több óra hozzáadására egy adott felhasználó számára.
  
- **Az indítási vagy leállítási ütemterv a tesztkörnyezet szintjén van beállítva, és a laborban található összes virtuális gépre vonatkozik.**

    Az előző ponthoz hasonlóan, ha különböző ütemterveket kell beállítania a felhasználóknak, külön tantermi laborokat kell létrehoznia.

Alapértelmezés szerint minden osztályterem-tesztkörnyezet saját virtuális hálózattal fog rendelkezni.  Ha engedélyezve van a vnet-társítás, az egyes tantermi laborokhoz saját alhálózat tartozik a megadott virtuális hálózathoz.

## <a name="shared-image-gallery"></a>Közös Képtár

A megosztott képkatalógus egy labor-fiókhoz van csatolva, és központi tárházként szolgál a lemezképek tárolásához. A rendszer egy képet ment a katalógusba, ha egy oktató úgy dönt, hogy egy tantermi labor sablonjának virtuális gépről (VM) exportálja. Minden alkalommal, amikor egy oktató módosítja a sablon virtuális gépet és az exportot, a rendszer menti a rendszerkép új verzióit a korábbi verziók fenntartása mellett.

Az oktatók közzétehetnek egy rendszerkép-verziót a megosztott rendszerkép-katalógusban, amikor új osztálytermi labort hoznak létre. Bár a katalógus egy rendszerkép több verzióját tárolja, a pedagógusok csak a legújabb verziót tudják kiválasztani a tesztkörnyezet létrehozásakor.

A megosztott képkatalógus egy opcionális erőforrás, amelyet nem kell azonnal megadnia, ha csak néhány osztálytermi labort használ. A megosztott képkatalógus használata azonban számos előnnyel jár, amelyek hasznosak a több osztályterem Labs-vel való méretezéshez:

- **Lehetővé teszi a sablonos virtuálisgép-rendszerképek verzióinak mentését és kezelését**

    Érdemes egyéni rendszerképet létrehozni, vagy változtatásokat (szoftvereket, konfigurációt stb.) készíteni a nyilvános Piactéri galériából.  Például gyakori az oktatók számára, hogy különböző software\tooling kelljen telepíteni. Ahelyett, hogy a tanulók manuálisan telepítik ezeket az előfeltételeket a saját, a sablon virtuálisgép-rendszerképének különböző verzióit exportálhatja egy megosztott képgyűjteménybe. Ezek a rendszerkép-verziók később új osztályterem Labs létrehozásakor is használhatók.
- **Lehetővé teszi a sablonok virtuálisgép-rendszerképeinek sharing\reuse a tanterem Labs-ben**

    Mentheti és újra felhasználhatja a képeket, így nem kell minden alkalommal konfigurálnia a rendszerképet, amikor új osztálytermi labort hoz létre. Ha például több olyan osztályt is kínálunk, amelyek ugyanazt a rendszerképet igénylik, ezt a rendszerképet csak egyszer kell létrehoznia, és exportálni kell a megosztott képkatalógusba, hogy az a tanterem Labs-ban is megosztható legyen.
- **A képek rendelkezésre állásának biztosítása replikáció útján**

    Amikor egy osztálytermi laborból menti a megosztott képtárat, a rendszer automatikusan replikálja a rendszerképet [az azonos földrajzi régión belüli más régiókba](https://azure.microsoft.com/global-infrastructure/regions/). Abban az esetben, ha egy régió esetében leáll a rendszerkép közzététele, nem érinti a rendszerképet, mert egy másik régióból származó rendszerkép-replikát lehet használni.  A virtuális gépek több replikából való közzététele is segíthet a teljesítményben.

A megosztott rendszerképek logikai csoportosításához több lehetőség közül választhat:

- Hozzon létre több megosztott rendszerkép-gyűjteményt. Az egyes tesztkörnyezet-fiókok csak egy megosztott képtárat tudnak csatlakozni, így a beállításhoz több tesztkörnyezet is létre kell hoznia.
- Vagy használhat egyetlen megosztott képtárat is, amelyet több Lab fiók is megoszt. Ebben az esetben az egyes tesztkörnyezet-fiókok csak azokat a lemezképeket engedélyezhetik, amelyek alkalmazhatók a benne található tantermi laborokra.

## <a name="naming"></a>Elnevezés

A Azure Lab Services első lépéseinél javasoljuk, hogy hozzon létre elnevezési konvenciókat az erőforráscsoportok, a labor-fiókok, a tanterem Labs és a megosztott képgyűjtemény számára. Míg a létrehozott elnevezési konvenciók egyediek lesznek a szervezet igényeinek megfelelően, az alábbi táblázat az általános irányelveket ismerteti.

| Erőforrás típusa | Szerepkör | Javasolt minta | Példák |
| ------------- | ---- | ----------------- | -------- | 
| Erőforráscsoport | Egy vagy több Lab-fiókot, valamint egy vagy több megosztott képgyűjteményt tartalmaz | \<organization short name\>-\<environment\>– RG<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>A **környezet** azonosítja az erőforrás környezetét, például a próbaüzem vagy a gyártás</li><li>A **RG** a következő erőforrástípus: erőforráscsoport.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs-Pilot-RG<br/>contosouniversitylabs – Prod-RG |
| Labor-fiók | Egy vagy több labort tartalmaz | \<organization short name\>-\<environment\>– La<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>A **környezet** azonosítja az erőforrás környezetét, például a próbaüzem vagy a gyártás</li><li>A **La** az erőforrástípus: Lab-fiók.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs – pilóta – La<br/>sciencedeptlabs-Prod-La |
| Tantermi labor | Egy vagy több virtuális gépet tartalmaz |\<class name\>-\<timeframe\>-\<educator identifier\><ul><li>Az **osztály neve** azonosítja a labor által támogatott osztály nevét.</li><li>Az **időkeret** azonosítja azt az időkeretet, amelyben az osztály elérhető.</li>Az **oktatási azonosító** azonosítja azt a pedagógust, amely a labor tulajdonosa.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe |
| Közös Képtár | Egy vagy több virtuálisgép-rendszerkép-verziót tartalmaz | \<organization short name\>Galéria | contosouniversitylabsgallery |

További információ az Azure-erőforrások elnevezéséről: az [Azure-erőforrások elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions).

## <a name="regionslocations"></a>Regions\locations

A Azure Lab Services erőforrásainak beállításakor meg kell adnia az erőforrást tároló adatközpont régióját (vagy helyét). Itt talál további információt arról, hogy a régió hogyan befolyásolja a laborok beállításában érintett erőforrásokat.

### <a name="resource-group"></a>Erőforráscsoport

A régió határozza meg azt az adatközpontot, ahol a rendszer az erőforráscsoport adatait tárolja. Az erőforráscsoporthoz tartozó Azure-erőforrások a szülőtől különböző régiókban is lehetnek.

### <a name="lab-account"></a>Labor-fiók

A labor-fiókok helye azt a régiót jelzi, amelyben ez az erőforrás található.  

### <a name="classroom-lab"></a>Tantermi labor

A tanterem laborjának helye a következő tényezők alapján változhat:

  - **A labor-fiók egy virtuális hálózathoz (VNet) van összemutatva**
  
    Egy labor-fiók VNet is lehet [,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) ha ugyanabban a régióban vannak.  Ha egy Lab-fiók egy VNet van társítva, akkor a rendszer automatikusan létrehozza az osztályterem Labs-t ugyanabban a régióban, mint a labor-fiókot és a VNet.

    > [!NOTE]
    > Ha egy Lab-fiók egy VNet van társítva, akkor a labor- **létrehozó a labor helyének kiválasztására** vonatkozó beállítás le van tiltva. Erről a beállításról további információt a következő cikkben talál: a labor [Creator kiválasztása a tesztkörnyezet helyének kiválasztásához](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location).
    
  - **Nincs VNet, és a labor ***-*** készítők nem választhatják ki a labor helyét**
  
    Ha **nincs VNet** társítva a labor-fiókkal, *és* a [labor létrehozói **nem** jogosultak a labor helyének kiválasztására](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location), az osztályterem Labs automatikusan létrejön egy olyan régióban, ahol rendelkezésre áll a virtuális gép kapacitása.  Pontosabban Azure Lab Services keresi a rendelkezésre állást azokban a [régiókban, amelyek a labor-fiókkal megegyező földrajzi területen belül vannak](https://azure.microsoft.com/global-infrastructure/regions).

  - **Nincs VNet, ***és*** a labor-készítők számára engedélyezett a labor helyének kiválasztása**
       
    Ha **nincs VNet,** és a labor létrehozói jogosultak [a labor helyének](https://docs.microsoft.com/azure/lab-services/classroom-labs/allow-lab-creator-pick-lab-location)kiválasztására, akkor a labor Creator által kiválasztható helyek a rendelkezésre álló kapacitáson alapulnak.

> [!NOTE]
> Annak érdekében, hogy elegendő virtuálisgép-kapacitás legyen a régióhoz, fontos, hogy először a labor-fiókon vagy a labor létrehozásakor igényelje a kapacitást.

Általános szabály egy erőforrás régiójának beállítása a felhasználókhoz legközelebb eső értékre. A tanterem Labs esetében ez azt jelenti, hogy a tanulók számára legközelebb eső tantermi labort hozza létre. Az online kurzusok, ahol a tanulók szerte a világon találhatók, a legjobb ítélet alapján kell létrehoznia a központilag elhelyezett tantermi labort. Vagy feloszthat egy osztályt több osztályterem Labs-ra a tanulók régiója alapján.

### <a name="shared-image-gallery"></a>Közös Képtár

A régió jelzi azt a forrás-régiót, ahol az első rendszerkép-verzió tárolódik, mielőtt a rendszer automatikusan replikálja a célhelyekre.

## <a name="vm-sizing"></a>Virtuális gép méretezése

Amikor a rendszergazdák vagy a labor készítői létrehoznak egy tantermi labort, a következő virtuálisgép-méretek közül választhatnak a tanterem igényei alapján. Ne feledje, hogy az elérhető számítási méretek attól a régiótól függenek, amelyben a labor-fiókja található:

| Méret | Jellemzők | Adatsorozat | Javasolt használat |
| ---- | ----- | ------ | ------------- |
| Kicsi| <ul><li>2 mag</li><li>3,5 GB RAM</li> | [Standard_A2_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a legmegfelelőbb a parancssorhoz, a webböngésző megnyitásához, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok eléréséhez. |
| Közepes | <ul><li>4 mag</li><li>7 GB RAM</li> | [Standard_A4_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. |
| Közepes (beágyazott virtualizálás) | <ul><li>4 mag</li><li>16 GB RAM</li></ul> | [Standard_D4s_v3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#dsv3-series) | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.
| Nagy | <ul><li>8 mag</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott.  Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. |
| Nagyméretű (beágyazott virtualizálás) | <ul><li>8 mag</li><li>16 GB RAM</li></ul>  | [Standard_A8_v2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott. |
| Kis GPU (vizualizáció) | <ul><li>6 mag</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |
| Kis GPU (számítás) | <ul><li>6 mag</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Ez a méret a nagy számítógépes igényű alkalmazások, például a mesterséges intelligencia és a Deep learning esetében ajánlott. |
| Közepes GPU (vizualizáció) | <ul><li>12 mag</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |

## <a name="manage-identity"></a>Identitás kezelése

Az [Azure szerepköralapú hozzáférés-vezérlésének](https://docs.microsoft.com/azure/role-based-access-control/overview)használatával a következő szerepkörök rendelhetők hozzá a labor-fiókokhoz és a tanterem Labs-hoz:

- **Tesztkörnyezetfiók tulajdonosa**

    A labor-fiókot létrehozó rendszergazda automatikusan hozzáadja a tesztkörnyezet **tulajdonosi** szerepköréhez.  A **tulajdonosi** szerepkörhöz rendelt rendszergazda a következőket teheti:
     - Módosítsa a labor-fiók beállításait.
     - Adja meg a rendszergazdának a labor-fiók tulajdonosként vagy közreműködőként való elérését.
     - Oktatóknak, tulajdonosoknak vagy közreműködőknek hozzáférést biztosíthat a tantermi laborokhoz.
     - Az összes osztályterem Labs létrehozása és kezelése a labor-fiókon belül.

- **Labor-fiók közreműködői**

    A **közreműködői** szerepkörhöz rendelt rendszergazda a következőket teheti:
    - Módosítsa a labor-fiók beállításait.
    - Az összes osztályterem Labs létrehozása és kezelése a labor-fiókon belül.

    Azonban *nem* tudnak más felhasználóknak hozzáférést biztosítani a labor-fiókokhoz vagy a tantermi laborokhoz.

- **Tantermi tesztkörnyezet létrehozója**

    A tantermi laborok tesztkörnyezetben való létrehozásához a pedagógusnak a **labor létrehozói** szerepkör tagjának kell lennie.  Amikor egy oktató létrehoz egy osztályterem labort, a rendszer automatikusan hozzáadja őket a labor tulajdonosaként.  Tekintse meg az oktatóanyagot, amely bemutatja, hogyan [adhat hozzá felhasználót a **labor létrehozói** szerepkörhöz](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account#add-a-user-to-the-lab-creator-role). 

- **Tantermi labor owner\contributor**
  
    A pedagógusok megtekinthetik és módosíthatják a tantermi labor beállításait, ha a labor **tulajdonosi** vagy **közreműködői** szerepkörének tagjai. Emellett a labor-fiók **olvasó** szerepkörének is tagjai.

    A labor **tulajdonosi** és **közreműködői** szerepkörei közötti fő különbség az, hogy a közreműködők *nem* biztosíthatnak más felhasználóknak hozzáférést a csak labor tulajdonosai számára, hogy más felhasználóknak is hozzáférjenek a laborhoz.

    Emellett a pedagógus *nem tud* új osztályterem Labs-t létrehozni, kivéve, ha a **labor létrehozói** szerepkör tagja is.

- **Közös Képtár**

    Ha megosztott képtárat csatlakoztat egy Lab-fiókhoz, a Lab-fiók owners\contributors és a labor creators\owners\contributors automatikusan hozzáférést kapnak a képek megtekintéséhez és mentéséhez a gyűjteményben.

Az alábbi tippek segítséget nyújtanak a szerepkörök hozzárendeléséhez:
   - Általában csak a rendszergazdáknak kell tartozniuk a labor-fiók **tulajdonosi** vagy **közreműködői** szerepköreihez; több owner\contributor. is rendelkezhet
   - Ahhoz, hogy a pedagógus új tantermi laborokat hozzon létre, és felügyelje a létrehozott laborokat; csak hozzá kell rendelnie a hozzáférést a **labor létrehozói** szerepkörhöz.
   - Ahhoz, hogy a pedagógus képes legyen az adott tantermi laborok kezelésére, de *nem* lehet új laborokat létrehozni; a **tulajdonosi** vagy **közreműködői** szerepkörhöz hozzá kell rendelnie a hozzáférést a felügyelni kívánt tantermi laborokhoz.  Tegyük fel például, hogy egy professzort és egy tanítási asszisztenst is szeretne használni egy osztályterem laborhoz.  Tekintse meg az útmutatót, amely bemutatja, hogyan [adhat hozzá felhasználót tulajdonosként egy osztálytermi laborhoz](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-add-user-lab-owner).

## <a name="pricing"></a>Díjszabás

### <a name="azure-lab-services"></a>Azure Lab Services

A Azure Lab Services díjszabását a következő cikk ismerteti: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

A megosztott képkatalógus díjszabását is figyelembe kell vennie, ha azt tervezi, hogy a rendszerképek tárolásához és kezeléséhez használja azt. 

### <a name="shared-image-gallery"></a>Közös Képtár

A megosztott képgyűjtemény létrehozása és a labor-fiókhoz való csatolása ingyenes. A költségek nem merülnek fel, amíg nem ment egy rendszerkép-verziót a gyűjteménybe. A megosztott képkatalógus használatának díjszabása általában meglehetősen elhanyagolható, de fontos tisztában lennie azzal, hogyan számítja ki a számítást, mivel az nem szerepel a Azure Lab Services díjszabásában.  

#### <a name="storage-charges"></a>Tárolási díjak

A képverziók tárolásához a megosztott képgyűjtemény szabványos HDD által felügyelt lemezeket használ. A használt HDD által felügyelt lemez mérete az éppen tárolt lemezkép-verzió méretétől függ. A díjszabást a következő cikkben tekintheti meg: [felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

#### <a name="replication-and-network-egress-charges"></a>Replikációs és hálózati kimenő forgalom díjai

Ha a tanterem Lab sablonjának virtuális gépe (VM) használatával ment egy rendszerkép-verziót, Azure Lab Services először a forrás-régióban tárolja, majd automatikusan replikálja a forrás rendszerkép verzióját egy vagy több célpontra. Fontos megjegyezni, hogy Azure Lab Services automatikusan replikálja a forrás-rendszerkép verzióját a földrajzi hely összes olyan [régiójába](https://azure.microsoft.com/global-infrastructure/regions/) , ahol az osztályterem-tesztkörnyezet található. Ha például az osztályterem Lab az Egyesült államokbeli földrajzban van, a rendszer a rendszerkép verzióját az Egyesült Államokban létező nyolc régióba replikálja.

A kimenő hálózati forgalom akkor fordul elő, ha egy rendszerkép verziója replikálódik a forrás régiójából a további célpontokra. A kiszámított összeg a rendszerkép verziójának méretétől függ, amikor a rendszerkép adatait először a forrás régiójából továbbítja a rendszer.  A díjszabással kapcsolatos részletekért tekintse meg a következő cikket: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Az [oktatási megoldások](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) ügyfelei lemondanak a kimenő forgalomért fizetendő díjakról. További információért forduljon a fiók kezelőjéhez.  További információkért lásd: a csatolt dokumentum **Gyakori kérdések** című szakasza, konkrétan az a kérdés, hogy milyen adatátviteli programok léteznek az akadémiai ügyfelek számára, és Hogyan szerezhetem be a minősítést?.

#### <a name="pricing-example"></a>Példa a díjszabásra

A fent ismertetett díjszabás beolvasása érdekében tekintse meg a sablon virtuálisgép-rendszerképének mentését bemutató példát a megosztott képkatalógusba. A következő helyzetekben feltételezzük:

- Egy egyéni virtuálisgép-lemezképpel rendelkezik.
- A rendszerkép két verzióját menti.
- A labor az Egyesült Államokban található, amely összesen nyolc régióval rendelkezik.
- Minden rendszerkép-verzió 32 GB méretű; Ennek eredményeképpen a HDD által felügyelt lemez ára $1,54 havonta.

A teljes díj a következőképpen becsülhető meg:

Lemezképek száma × a verziók száma × a replikák száma × felügyelt lemez ára

Ebben a példában a Cost a következő:

1 egyéni rendszerkép (32 GB) x 2 verzió x 8 amerikai régió x $1,54 = $24,64/hó

#### <a name="cost-management"></a>Cost Management

Fontos, hogy a labor-fiók rendszergazdája a katalógusból rendszeresen törölje a szükségtelen rendszerkép-verziókat. 

A költségek csökkentése érdekében ne törölje a replikálást meghatározott régiókba (ez a lehetőség a megosztott képtárban található). Előfordulhat, hogy a replikációs módosítások kedvezőtlen hatással lehetnek az Azure Lab szolgáltatásban a megosztott képtárban mentett rendszerképekből származó virtuális gépek közzétételének képességére.

## <a name="next-steps"></a>További lépések

A labor-fiókok és laborok létrehozásával kapcsolatos részletes útmutatásért lásd: [útmutató beállítása](tutorial-setup-lab-account.md)
