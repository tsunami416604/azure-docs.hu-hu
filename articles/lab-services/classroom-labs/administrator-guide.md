---
title: Azure Lab Services – rendszergazdai útmutató | Microsoft Docs
description: Ez az útmutató segít a rendszergazdáknak, hogy Azure Lab Services használatával hozzanak létre és kezeljenek labor-fiókokat.
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
ms.openlocfilehash: 638a90615d248b3c2829770432dd6a08eb4bb2fb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771734"
---
# <a name="azure-lab-services---administrator-guide"></a>Azure Lab Services – rendszergazdai útmutató
A szervezet felhőalapú erőforrásait kezelő informatikai rendszergazdák is jellemzően felelősek a szervezethez tartozó labor-fiók beállításában. A rendszergazdák vagy oktatók a Lab-fiókban hozhatnak létre osztályterem Labs-t. Ez a cikk átfogó áttekintést nyújt az érintett Azure-erőforrásokról és a létrehozásához szükséges útmutatóról.

![Azure-erőforrások magas szintű megtekintése egy labor-fiókban](../media/administrator-guide/high-level-view.png)

- A tanterem Labs egy Azure Lab Services által birtokolt Azure-előfizetésen belül üzemel
- A labor-fiókok, a megosztott képkatalógus és a képverziók az előfizetésen belül vannak tárolva
- Elvégezheti a labor-fiók és a szegmens képtárat ugyanabban az erőforráscsoporthoz. Ebben a diagramban különböző erőforráscsoportok találhatók. 

## <a name="subscription"></a>Előfizetés
A szervezet egy vagy több Azure-előfizetéssel rendelkezik. Az előfizetés segítségével kezelhető a számlázás és a biztonság az összes olyan Azure-resources\services esetében, amelyet a rendszer használ, beleértve a laboratóriumi fiókokat is.

A labor-fiók és az előfizetése közötti kapcsolat azért fontos, mert:

- A számlázás a labor-fiókot tartalmazó előfizetésen keresztül történik.
- Az előfizetéshez társított Azure Active Directory-(AD-) bérlőben a felhasználók Azure Lab Serviceshoz való hozzáférést biztosíthatnak. Felveheti a felhasználót labor-fiók owner\contributor vagy tantermi labor létrehozóként.

A tantermi laborokat és a virtuális gépeket (VM) teljes mértékben kezelik. Konkrétan a Azure Lab Services által birtokolt dedikált előfizetésen belül futnak.

## <a name="resource-group"></a>Erőforráscsoport
Egy előfizetés egy vagy több erőforráscsoportot tartalmaz. Az erőforráscsoportok használatával létrehozhatók az Azure-erőforrások logikai csoportjai, amelyek ugyanabban a megoldásban együtt használatosak.  

Labor-fiók létrehozásakor be kell állítania azt az erőforráscsoportot, amely a labor-fiókot tartalmazza. 

[Megosztott képgyűjtemény](#shared-image-gallery)létrehozásakor is szükség van egy erőforráscsoporthoz. Dönthet úgy, hogy két különálló erőforráscsoporthoz helyezi el a labor-fiókját és a közös rendszerkép-katalógust, amely akkor jellemző, ha a képtárat különböző megoldásokban szeretné megosztani. Vagy dönthet úgy is, hogy ugyanabba az erőforráscsoporthoz helyezi őket.

Amikor létrehoz egy Lab-fiókot, és egy időben automatikusan létrehoz és csatol egy megosztott képtárat, a rendszer alapértelmezés szerint külön erőforráscsoporthoz hozza létre és csatlakoztatja a közös rendszerkép-katalógust. Ezt a viselkedést a jelen oktatóanyagban ismertetett lépések használatakor fogja látni: a [megosztott rendszerkép-gyűjtemény konfigurálása a tesztkörnyezet létrehozásakor](how-to-attach-detach-shared-image-gallery.md#configure-at-the-time-of-lab-account-creation). A cikk tetején található rendszerkép ezt a konfigurációt is használja. 

Javasoljuk, hogy az erőforrások felépítésének megtervezéséhez tervezze meg az időkorlátot, mivel a létrehozása után nem lehet módosítani a labor-fiók vagy a megosztott képgyűjtemény erőforráscsoportot. Ha módosítania kell az erőforrás-csoportot ezen erőforrások esetében, törölnie kell, majd újra létre kell hoznia a tesztkörnyezet and\or megosztott képtárat.

## <a name="lab-account"></a>Labor-fiók
A labor-fiókok egy vagy több osztályterem Labs számára tárolóként szolgálnak. A Azure Lab Services első lépéseiben gyakran csak egyetlen labor-fiókkal rendelkezhet. A labor használatának skálázása után később további labor-fiókokat is létrehozhat.

Az alábbi lista olyan forgatókönyveket mutat be, amelyekben több labor-fiók is hasznos lehet:

- **Különböző házirend-követelmények kezelése a tanterem Labs-ben** 

    Labor-fiók beállításakor olyan házirendeket állít be, amelyek az Lab-fiókban az összes osztályterem Labs-re érvényesek, például:
    - Az Azure-beli virtuális hálózat olyan megosztott erőforrásokkal rendelkezik, amelyekhez a tanterem Lab hozzáférhet. Előfordulhat például, hogy olyan osztályterem Labs-készlettel rendelkezik, amelynek hozzáférésre van szüksége egy megosztott adatkészlethez egy virtuális hálózaton belül.
    - Azok a virtuálisgép-lemezképek, amelyeket a tanterem Labs használhat a virtuális gépek létrehozásához. Előfordulhat például, hogy olyan osztályterem Labs-készlettel rendelkezik, amelynek hozzáférésre van szüksége a [Data Science VM for Linux](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.linux-data-science-vm-ubuntu) Marketplace-rendszerképhez.  

    Ha olyan tantermi laborokkal rendelkezik, amelyek egymástól egyedi házirend-követelményekkel rendelkeznek, érdemes lehet külön labor-fiókokat létrehozni a tantermi laborok külön történő kezeléséhez.
- **A labor-létrehozók hozzáférésének korlátozása adott tantermi laborokhoz**  

    Ha a felhasználó labor-létrehozóként van hozzáadva, akkor a rendszer hozzáférést kap az összes tantermi laborhoz a labor-fiókon belül, beleértve a más labor-készítők által létrehozott laborokat is. Ha korlátozni szeretné a tesztkörnyezet-készítőket adott Labs kezelésére, létrehozhat külön labor-fiókokat a hozzáférésük hatókörének korlátozásához. Létrehozhat például egy külön Lab-fiókot az egyetemen található minden részleghez. Például: egy labor-fiók a tudományos részleghez, egy másik pedig a matematikai részleghez, és így tovább.   
- **Elkülönített költségvetés labor-fiókkal**

    Ahelyett, hogy az összes osztályterem Lab-költségét egyetlen Lab-fiókra vonatkozóan kellene jelentenie, lehet, hogy világosabban elkülönített költségvetést kell használnia. A fenti felsorolásban szereplő példának megfelelően létrehozhat egy Lab-fiókot az egyes egyetemi részlegekhez, hogy ennek megfelelően elkülönítse a költségvetést. A Azure Cost Management használatával megtekintheti az egyes labor-fiókok költségeit.
- **A kísérleti Labs elkülönítése az Active Labs-ből**

    Előfordulhat, hogy olyan esetekre van szükség, amikor a szabályzatot egy labor-fiókba kívánja módosítani anélkül, hogy ez hatással lenne az aktív laborokra. Ilyen esetben a tesztelési célokra külön labor-fiók létrehozása lehetővé teszi a módosítások elkülönítését. 

## <a name="classroom-lab"></a>Tantermi labor
Az osztályterem Lab egy vagy több virtuális gépet tartalmaz, amelyek mindegyike egy adott tanulóhoz van rendelve. Általánosságban elmondható, hogy az alábbiakra számíthat:

- Minden osztályhoz egy osztályterem labor tartozik.
- Hozzon létre egy új osztályterem Labs-készletet minden félévben (vagy az osztály minden egyes időkerete esetén). Az azonos Rendszerképbeli igényekkel rendelkező osztályok esetében általában [megosztott](#shared-image-gallery) képkatalógust használ a képek megosztásához a laborokban és a félévekben.

A következő szempontokat kell figyelembe vennie az osztályterem Labs szerkezetének meghatározásakor:

- **A tantermi laborban lévő összes virtuális gép a közzétett lemezképpel azonos képpel van telepítve**. Ennek eredményeképpen, ha olyan osztállyal rendelkezik, amelyhez különböző tesztkörnyezet-lemezképek is közzé lesznek téve, külön tantermi laborokat kell létrehozni mindegyikhez.
- A **használati kvóta a labor szintjén van beállítva, és a laborban lévő összes felhasználóra érvényes**. Előfordulhat például, hogy olyan pedagógusokkal rendelkezik, amelyeknek hozzáférésre van szüksége egy osztály virtuális gépei számára az oktatóanyag előkészítéséhez, de az oktatóknak csak 10 órás kvótára van szükségük, míg az osztályban regisztrált tanulók 40 órás kvótát igényelnek. Ha különböző kvótákat szeretne beállítani a felhasználók számára, külön tantermi laborokat kell létrehoznia. A kvóta beállítása után azonban lehetőség van több óra hozzáadására egy adott felhasználó számára.
- **Az indítási vagy leállítási ütemterv a tesztkörnyezet szintjén van beállítva, és a laboron belüli összes virtuális gépre vonatkozik**. Az előző ponthoz hasonlóan, ha különböző ütemterveket kell beállítania a felhasználóknak, külön tantermi laborokat kell létrehoznia. 

## <a name="shared-image-gallery"></a>Közös Képtár
A megosztott képkatalógus egy labor-fiókhoz van csatolva, és központi tárházként szolgál a lemezképek tárolásához. A rendszer egy képet ment a katalógusba, ha egy oktató úgy dönt, hogy a tanterem-tesztkörnyezet sablonjának virtuális gépe (VM) alapján menti a mentést. Minden alkalommal, amikor egy oktató módosítja a sablon virtuális gépet és menti a fájlokat, a rendszer menti a rendszerkép új verzióit a korábbi verziók fenntartása mellett.

Az oktatók közzétehetnek egy rendszerkép-verziót a megosztott rendszerkép-katalógusban, amikor új osztálytermi labort hoznak létre. Bár a katalógus egy rendszerkép több verzióját is képes tárolni, a pedagógusok csak a legújabb verziót tudják kiválasztani a tesztkörnyezet létrehozásakor.

A megosztott képkatalógus egy opcionális erőforrás, amelyet nem kell azonnal megadnia, ha csak néhány osztálytermi labort használ. A megosztott képkatalógus használata azonban számos előnnyel jár, amelyek hasznosak a több osztályterem Labs-vel való méretezéshez:

- **Lehetővé teszi egy sablonbeli virtuálisgép-rendszerkép verzióinak mentését és kezelését**.

    Ez akkor hasznos, ha egyéni rendszerképet hoz létre, vagy módosításokat (szoftvereket, konfigurációt stb.) végez a nyilvános Piactéri katalógusban.  Például gyakori az oktatók számára, hogy különböző software\tooling kelljen telepíteni. Ahelyett, hogy a tanulóknak manuálisan kellene telepíteniük ezeket az előfeltételeket, a sablon virtuálisgép-rendszerképének különböző verziói menthetők egy megosztott képtárba. Ezek a rendszerkép-verziók később új osztályterem Labs létrehozásakor is használhatók.
- **Lehetővé teszi a sablonok virtuálisgép-rendszerképeinek sharing\reuse a tanterem Labs**-ben.

    Ez megakadályozza, hogy egy új osztályterem-labor létrehozásakor minden alkalommal be lehessen állítani egy rendszerképet. Ha például több olyan osztályt is kínálunk, amelyek ugyanazt a rendszerképet igénylik, akkor ezt a rendszerképet csak egyszer kell létrehozni és menteni a megosztott rendszerkép-katalógusban, hogy az a tanterem Labs-ban is megosztható legyen.
- **Biztosítja a lemezkép rendelkezésre állását a replikáción keresztül**.

    Ha a megosztott képtárat egy osztályterem laborból menti, a rendszer automatikusan replikálja a rendszerképet az azonos földrajzi régión belüli más régiókba. Abban az esetben, ha egy régió esetében leállás történik, a sablon virtuális gépe az osztályterem laborban való közzététele nem érinti a más régiókban található képreplika használatával. Emellett segít a több virtuális gépre kiterjedő közzétételi forgatókönyvek teljesítményében azáltal, hogy a különböző replikák használatát terjeszti ki.

A megosztott rendszerképek logikai csoportosításához több lehetőség közül választhat:

- Hozzon létre több megosztott rendszerkép-gyűjteményt. Az egyes tesztkörnyezet-fiókok csak egy megosztott képtárat tudnak csatlakozni, így a beállításhoz több tesztkörnyezet is létre kell hoznia.
- Vagy használhat egyetlen megosztott képtárat is, amelyet több Lab fiók is megoszt. Ebben az esetben az egyes tesztkörnyezet-fiókok csak azokat a lemezképeket engedélyezhetik, amelyek alkalmazhatók a benne található tantermi laborokra.

## <a name="naming"></a>Elnevezés
A Azure Lab Services első lépéseinél javasoljuk, hogy hozzon létre elnevezési konvenciókat az erőforráscsoportok, a labor-fiókok, a tanterem Labs és a megosztott képgyűjtemény számára. Míg a létrehozott elnevezési konvenciók egyediek lesznek a szervezet igényeinek megfelelően, az alábbi táblázat az általános irányelveket ismerteti.

| Erőforrás típusa | Szerepkör | Javasolt minta | Példák |
| ------------- | ---- | ----------------- | -------- | 
| Erőforráscsoport | Egy vagy több Lab-fiókot, valamint egy vagy több megosztott képgyűjteményt tartalmaz | \<szervezet rövid neve\>-\<Environment\>-RG<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>A **környezet** azonosítja az erőforrás környezetét, például a tesztet vagy a gyártást</li><li>A **RG** a következő erőforrástípus: erőforráscsoport.</li></ul> | contosouniversitylabs – RG<br/>contosouniversitylabs – teszt – RG<br/>contosouniversitylabs – Prod-RG |
| Labor-fiók | Egy vagy több labort tartalmaz | \<szervezet rövid neve\>-\<Environment\>-La<ul><li>A **szervezet rövid neve** azonosítja annak a szervezetnek a nevét, amelyet az erőforráscsoport támogat</li><li>A **környezet** azonosítja az erőforrás környezetét, például a tesztet vagy a gyártást</li><li>A **La** az erőforrástípus: Lab-fiók.</li></ul> | contosouniversitylabs-La<br/>mathdeptlabs-La<br/>sciencedeptlabs-test-La<br/>sciencedeptlabs-Prod-La |
| Tantermi labor | Egy vagy több virtuális gépet tartalmaz |\<osztály neve\>-\<időkerete\>-\<oktató azonosítója\><ul><li>Az **osztály neve** azonosítja a labor által támogatott osztály nevét.</li><li>Az **időkeret** azonosítja azt az időkeretet, amelyben az osztály elérhető.</li>Az **oktatási azonosító** azonosítja azt a pedagógust, amely a labor tulajdonosa.</li></ul> | CS1234-fall2019-johndoe<br/>CS1234-spring2019-johndoe | 
| Közös Képtár | Egy vagy több virtuálisgép-rendszerkép-verziót tartalmaz | \<szervezet rövid neve\>gyűjtemény | contosouniversitylabsgallery |

További információ az Azure-erőforrások elnevezéséről: az [Azure-erőforrások elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions).

## <a name="regions-or-locations"></a>Régiók vagy helyszínek
A Azure Lab Services erőforrásainak beállításakor meg kell adnia az erőforrást tároló adatközpont régióját vagy helyét. Az alábbiakban részletesen tájékozódhat arról, hogy a region\location hogyan befolyásolja a labor Services üzembe helyezéséhez használt alábbi erőforrásokat:

- **Erőforráscsoport**

    A régió határozza meg azt az adatközpontot, ahol a rendszer az erőforráscsoport adatait tárolja. Az erőforráscsoporthoz tartozó Azure-erőforrások a szülőtől különböző régiókban is lehetnek.
- **Lab-fiók vagy osztálytermi labor**

    A labor fiókjának helye az erőforrás régióját jelöli. A labor-fiókban létrehozott tantermi laborok bármelyik régióban üzembe helyezhetők ugyanazon a földrajzon belül. Az a régió, amelyre a labor virtuális gépei telepítve vannak, automatikusan ki lesz választva a régióban rendelkezésre álló kapacitás alapján.  
    Ha egy rendszergazda lehetővé teszi, hogy a labor-készítők kiválasszák a tantermi tesztkörnyezet helyét, a kiválasztáshoz elérhető helyek a labor létrehozásakor rendelkezésre álló regionális kapacitáson alapulnak.

    A tanterem laborjának helye határozza meg azt is, hogy mely virtuálisgép-számítási méretek érhetők el a kiválasztáshoz. Bizonyos számítási méretek csak bizonyos helyszíneken érhetők el.
- **Közös Képtár**

    A régió jelzi azt a forrás-régiót, ahol az első rendszerkép-verzió tárolódik, mielőtt a rendszer automatikusan replikálja a célhelyekre.
    
Általános szabály egy erőforrás region\location beállítása, amely legközelebb van a felhasználóhoz. A tanterem Labs esetében ez azt jelenti, hogy a tanulók számára legközelebb eső tantermi labort hozza létre. Az online kurzusok, ahol a tanulók szerte a világon találhatók, a legjobb ítélet alapján kell létrehoznia a központilag elhelyezett tantermi labort. Vagy feloszthat egy osztályt több osztályterem Labs-ra a tanulók régiója alapján.

## <a name="vm-sizing"></a>Virtuális gép méretezése
Amikor a rendszergazdák vagy a labor készítői létrehoznak egy tantermi labort, a következő virtuálisgép-méretek közül választhatnak a tanterem igényei alapján. Ne feledje, hogy az elérhető számítási méretek attól a régiótól függenek, amelyben a labor-fiókja található:

| Méret | Adatok | Javasolt használat |
| ---- | ----- | ------------- |
| Kicsi| <ul><li>2 mag</li><li>3,5 GB RAM</li></ul> | Ez a méret a legmegfelelőbb a parancssorhoz, a webböngésző megnyitásához, az alacsony forgalmú webkiszolgálók, a kis és közepes adatbázisok eléréséhez. |
| Közepes | <ul><li>4 mag</li><li>7 GB RAM</li></ul> | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez. |
| Közepes (beágyazott virtualizálás) | <ul><li>4 mag</li><li>16 GB RAM</li></ul> | Ez a méret a legmegfelelőbb a kapcsolatok adatbázisaihoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.  Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. |
| Nagy | <ul><li>8 mag</li><li>32 GB RAM</li></ul> | Ez a méret a gyorsabb processzorokat, nagyobb teljesítményű helyi lemezeket, nagyméretű adatbázisokat és nagyméretű memória-gyorsítótárat igénylő alkalmazások számára ajánlott.  Ez a méret támogatja a beágyazott virtualizálás szolgáltatást is. |
| Kis GPU (vizualizáció) | <ul><li>6 mag</li><li>56 GB RAM</li> | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |
| Kis GPU (számítás) | <ul><li>6 mag</li><li>56 GB RAM</li></ul> |Ez a méret a nagy számítógépes igényű alkalmazások, például a mesterséges intelligencia és a Deep learning esetében ajánlott. |
| Közepes GPU (vizualizáció) | <ul><li>12 mag</li><li>112 GB RAM</li></ul> | Ez a méret a távoli vizualizációhoz, a folyamatos átvitelhez, a játékokhoz, a kódoláshoz, például az OpenGL és a DirectX keretrendszerekhez használható. |

## <a name="manage-identity"></a>Identitás kezelése
A labor-fiók rendszergazdája két típusú szerepkörrel rendelkezhet:

- **Tulajdonos**

    A **tulajdonosi** szerepkörhöz rendelt rendszergazdának teljes hozzáférése van a labor-fiókhoz, beleértve a jogot, hogy más felhasználóknak hozzáférést biztosítson a labor-fiókhoz, és hozzáadja a labor-készítőket. A Lab-fiókot alapértelmezés szerint létrehozó rendszergazda adja hozzá tulajdonosként.
- **Közreműködő**

    A közreműködő szerepkörhöz rendelt rendszergazda módosíthatja a labor-fiókok beállításait, de nem adhat hozzáférést más felhasználóknak. és nem is hozzáadhatnak Lab-készítőket.

Ha megosztott képtárat csatlakoztat egy Lab-fiókhoz, a rendszer automatikusan megkapja a hozzáférést a rendszergazda és a labor készítők számára, így a képeket megtekinthetik és menthetik a gyűjteményben. 

## <a name="pricing"></a>Díjszabás

### <a name="azure-lab-services"></a>Azure Lab Services
A Azure Lab Services díjszabását a következő cikk ismerteti: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).

A megosztott képkatalógus díjszabását is figyelembe kell vennie, ha azt tervezi, hogy a rendszerképek tárolásához és kezeléséhez használja azt. 

### <a name="shared-image-gallery"></a>Közös Képtár
A megosztott képgyűjtemény létrehozása és a labor-fiókhoz való csatolása ingyenes. A költségek nem merülnek fel, amíg nem ment egy rendszerkép-verziót a gyűjteménybe. A megosztott képkatalógus használatának díjszabása általában meglehetősen elhanyagolható, de fontos tisztában lennie azzal, hogyan számítja ki a számítást, mivel az nem szerepel a Azure Lab Services díjszabásában.  

### <a name="storage-charges"></a>Tárolási díjak
A képverziók tárolásához a megosztott képgyűjtemény szabványos HDD által felügyelt lemezeket használ. A használt HDD által felügyelt lemez mérete az éppen tárolt lemezkép-verzió méretétől függ. A díjszabást a következő cikkben tekintheti meg: [felügyelt lemezek díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).


### <a name="replication-and-network-egress-charges"></a>Replikációs és hálózati kimenő forgalom díjai
Amikor egy tantermi labor sablonjának virtuális gépe (VM) használatával menti a rendszerkép verzióját, először a labor Services tárolja azt egy forrásoldali régióban, majd automatikusan replikálja a forrás rendszerkép verzióját egy vagy több célhelyre. Fontos megjegyezni, hogy Azure Lab Services automatikusan replikálja a forrás-rendszerkép verzióját az összes olyan célcsoportba, ahol az osztályterem-tesztkörnyezet található. Ha például az osztályterem Lab az Egyesült államokbeli térinformatikai szolgáltatásban van, a rendszer az Egyesült Államokban meglévő nyolc régióba replikálja a rendszerkép verzióját.

A kimenő hálózati forgalom akkor fordul elő, ha egy rendszerkép verziója replikálódik a forrás régiójából a további célpontokra. A kiszámított összeg a rendszerkép verziójának méretétől függ, amikor a rendszerkép adatait először a forrás régiójából továbbítja a rendszer.  A díjszabással kapcsolatos részletekért tekintse meg a következő cikket: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/).

Az [oktatási megoldások](https://www.microsoft.com/licensing/licensing-programs/licensing-for-industries?rtc=1&activetab=licensing-for-industries-pivot:primaryr3) ügyfelei lemondanak a kimenő forgalomért fizetendő díjakról. További információért forduljon a fiók kezelőjéhez.  További információkért lásd: a csatolt dokumentum **Gyakori kérdések** című szakasza, konkrétan a "milyen adatátviteli programok léteznek az akadémiai ügyfelek számára, és Hogyan szerezhetem be a jogosultságot?" című szakaszt.

### <a name="pricing-example"></a>Példa az árképzésre
A fent ismertetett díjszabás beolvasása érdekében tekintse meg a sablon virtuálisgép-rendszerképének mentését bemutató példát a megosztott képkatalógusba. A következő helyzetekben feltételezzük:

- Egy egyéni virtuálisgép-lemezképpel rendelkezik.
- A rendszerkép két verzióját menti.
- A labor az Egyesült Államokban található, amely összesen nyolc régióval rendelkezik.
- Minden rendszerkép-verzió 32 GB méretű; Ennek eredményeképpen a HDD által felügyelt lemez ára $1,54 havonta.

A teljes díj a következőképpen becsülhető meg:

Lemezképek száma × a verziók száma × a replikák száma × felügyelt lemez ára

Ebben a példában a Cost a következő:

1 egyéni rendszerkép (32 GB) x 2 verzió x 8 amerikai régió x $1,54 = $24,64/hó

### <a name="cost-management"></a>Költségkezelés
Fontos, hogy a labor-fiók rendszergazdája a katalógusból rendszeresen törölje a szükségtelen rendszerkép-verziókat. 

A költségek csökkentése érdekében ne törölje a replikálást meghatározott régiókba (ez a lehetőség a megosztott képtárban található). Előfordulhat, hogy a replikációs módosítások kedvezőtlen hatással lehetnek az Azure Lab szolgáltatásban a megosztott képtárban mentett rendszerképekből származó virtuális gépek közzétételének képességére.

## <a name="next-steps"></a>Következő lépések
A labor-fiókok és a laborok létrehozásával kapcsolatos részletes útmutatásért lásd: [oktatóanyag: tesztkörnyezet beállítása](tutorial-setup-lab-account.md)
