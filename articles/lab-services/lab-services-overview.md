---
title: Az Azure Lab Services bemutatása | Microsoft Docs
description: Megismerheti, hogyan könnyítheti meg a Lab Services a tesztkörnyezetek létrehozását, felügyeletét és védelmét a fejlesztők, tesztelők, oktatók, diákok és mások által használható virtuális gépekkel.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: cb957346cb0e003e9aff19026ed81495abf432c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049377"
---
# <a name="an-introduction-to-azure-lab-services"></a>Az Azure Lab Services bemutatása
Az Azure Lab Services lehetővé teszi egy környezet (például: fejlesztési környezet, tesztelési környezet, osztályterem-tesztkörnyezet) beállítását a csapat számára a felhőben. A tesztkörnyezet tulajdonosa létrehoz egy tesztkörnyezetet, Windows vagy Linux rendszerű virtuális gépeket épít ki, telepíti a szükséges szoftvereket és eszközöket, és elérhetővé teszi azokat a tesztkörnyezet felhasználói számára. A tesztkörnyezet felhasználói a tesztkörnyezetben található virtuális gépekhez csatlakoznak, és a mindennapos munkájukhoz, rövidtávú projektekhez vagy osztálytermi gyakorlatokhoz használják azokat. Miután a felhasználók megkezdik az erőforrások használatát a tesztkörnyezetben, a tesztkörnyezet egy rendszergazdája több tesztkörnyezetre kiterjedően elemezheti a költségeket és a használatot, és átfogó szabályzatokat állíthat be a vállalat vagy csapat költségeinek optimalizálásához.

> [!IMPORTANT]
> Az **Azure DevTest Labs** új tesztkörnyezettípusokkal bővült (Azure Lab Services). 
> 
> Az Azure Lab Services lehetővé teszi felügyelt tesztkörnyezetek, például osztályterem-tesztkörnyezetek létrehozását. A szolgáltatás maga gondoskodik a felügyelt tesztkörnyezet infrastruktúrájának átfogó kezeléséről, a virtuális gépek indításától a hibák kezelésén keresztül az infrastruktúra méretezéséig. A felügyelt tesztkörnyezetek jelenleg előzetes verzióban érhetők el. Az előzetes verziót követően az új tesztkörnyezettípusok és a meglévő DevTest Labs-környezetek az Azure Lab Services gyűjtőnéven ismert új közös szolgáltatásban egyesülnek, amelyben tovább folyik a tesztkörnyezettípusok fejlesztése. 

## <a name="key-capabilities"></a>Főbb képességek
Az Azure Lab Services az alábbi fő képességeket/funkciókat támogatja: 

- **A tesztkörnyezetek gyors és rugalmas beállítása**. A tesztkörnyezetek tulajdonosai az Azure Lab Services használatával gyorsan állíthatnak be az igényeinek megfelelő tesztkörnyezetet. A szolgáltatás gondoskodik a felügyelt tesztkörnyezetek Azure-infrastruktúrájához kapcsolódó feladatok mindegyikének elvégzéséről, vagy pedig lehetővé teszi, hogy a tesztkörnyezet-tulajdonosok saját maguk kezeljék és szabják testre az infrastruktúrát a tesztkörnyezet-tulajdonos előfizetésében. A szolgáltatás biztosítja az infrastruktúra beépített méretezését és rugalmasságát azon tesztkörnyezetek esetén, amelyeket a tulajdonos helyett kezel. 
- **Egyszerű használat a tesztkörnyezetek felhasználói számára**. A felügyelt tesztkörnyezetekben, például az osztályterem-tesztkörnyezetekben a felhasználók regisztrációs kóddal regisztrálhatnak a tesztkörnyezetekre, és bármikor elérhetik a tesztkörnyezetet a tesztkörnyezet erőforrásainak használatához. A DevTest Labs szolgáltatásban létrehozott tesztkörnyezetekben a tesztkörnyezet tulajdonosa engedélyt adhat a tesztkörnyezet felhasználóinak a virtuális gépek létrehozásához és eléréséhez, az adatlemezek felügyeletéhez és újbóli használatához, valamint többször használható titkos kulcsok beállításához.  
- **Költségoptimalizálás és elemzés**. A tesztkörnyezet tulajdonosa ütemezéseket állíthat be a tesztkörnyezetben a virtuális gépek automatikus leállításához és elindításához. A tesztkörnyezet tulajdonosa beállíthat egy ütemezést azon időtartamok meghatározásához, amikor a tesztkörnyezet virtuális gépei elérhetők a felhasználók számára, és felhasználóalapú vagy tesztkörnyezet-alapú használati szabályzatokat állíthat be a költségek optimalizálása, továbbá a tesztkörnyezetben jelentkező használati és tevékenységi trendek elemzése érdekében. Felügyelt tesztkörnyezetek, például osztályterem-tesztkörnyezetek esetén a költségoptimalizálási és elemzési lehetőségeknek jelenleg csak egy kisebb része érhető el. 
- **Beágyazott biztonság**. A tesztkörnyezet tulajdonosa beállíthat egy virtuális magánhálózatot és alhálózatot a tesztkörnyezet számára, és engedélyezheti a megosztott nyilvános IP-címeket. A tesztkörnyezetek felhasználói biztonságosan érhetik el az erőforrásokat az ExpressRoute vagy helyek közötti VPN használatával konfigurált virtuális hálózattal. (Jelenleg csak a DevTest Labs szolgáltatásban érhető el.)
- **Integrálás a munkafolyamatokba és az eszközökbe**. Az Azure Lab Services lehetővé teszi a tesztkörnyezetek integrálását a vállalat webhelyébe és felügyeleti rendszereibe. Automatikusan építhet ki környezeteket a folyamatos integrációs/folyamatos üzembehelyezési (CI/CD) eszközökből. (Jelenleg csak a DevTest Labs szolgáltatásban érhető el.)

> [!NOTE]
> Az Azure Lab Services jelenleg csak az Azure Marketplace rendszerképeiből létrehozott virtuális gépeket támogatja. Ha egyéni rendszerképeket szeretne használni vagy egyéb PaaS-erőforrásokat szeretne létrehozni tesztkörnyezetben, használja a DevTest Labst. További információkért lásd az [egyéni rendszerkép a DevTest Labsban történő létrehozását](devtest-lab-create-custom-image-from-vm-using-portal.md) és [a tesztkörnyezetek Resource Manager-sablonok használatával történő létrehozását](devtest-lab-create-environment-from-arm.md) ismertető cikket. 

## <a name="scenarios"></a>Forgatókönyvek
Néhány, az Azure Lab Services által támogatott forgatókönyv: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Méretezhető számítógépes tesztkörnyezet beállítása a felhőben az osztályterem számára  

- Felügyelt osztályterem-tesztkörnyezet létrehozása. Mindössze közölnie kell a szolgáltatással, hogy pontosan mire van szüksége, a szolgáltatás pedig létrehozza és felügyeli a tesztkörnyezet infrastruktúráját, így az osztály tanítására összpontosíthat, és nem kell a tesztkörnyezet technikai részleteivel foglalkoznia. 
- A pontosan a tanórához szükséges dolgokkal konfigurált virtuális gépekből álló tesztkörnyezet biztosítása a diákok számára. Korlátozott mennyiségű időt biztosíthat minden diák számára a virtuális gépek használatához az osztálytermi munka elvégzéséhez.  
- Az iskola fizikai számítógéptermének áthelyezése a felhőbe. A virtuális gépek számának automatikus méretezése a tesztkörnyezetben beállított maximális használatnak és költséghatárnak megfelelően. 
- A tesztkörnyezet törlése egyetlen kattintással, miután végzett. 

### <a name="use-devtest-labs-for-development-environments"></a>A DevTest Labs használata fejlesztési környezetekhez 
Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a fejlesztői gépeket a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja: 

- Lehetővé teszi a fejlesztők számára a fejlesztési gépek igény szerinti kiépítését.
- Windows- és Linux-környezetek kiépítése újrahasznosítható sablonokkal és összetevőkkel.
- A fejlesztők szükség esetén könnyedén testreszabhatják a fejlesztési gépeket.
- A költségek szabályozása érdekében a rendszergazdák gondoskodhatnak arról, hogy a fejlesztők ne kapjanak több virtuális gépet, mint amennyire a fejlesztéshez szükségük van, illetve hogy a virtuális gépek leálljanak, amikor nincsenek használatban. 

További információ: [A DevTest Labs használata fejlesztéshez](devtest-lab-developer-lab.md). 

### <a name="use-devtest-labs-for-test-environments"></a>A DevTest Labs használata tesztkörnyezetekhez
Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a gépeket a tesztelők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A tesztelők gyorsan tesztelhetik az alkalmazás legújabb verzióját Windows- és Linux-környezetek újrahasznosítható sablonokkal és összetevőkkel történő kiépítésével.
- A tesztelők vertikálisan felskálázhatják a terhelésteszteket több tesztügynök kiépítésével.
- A költségek szabályozása érdekében a rendszergazdák gondoskodhatnak arról, hogy a tesztelők ne kapjanak több virtuális gépet, mint amennyire a teszteléshez szükségük van, illetve hogy a virtuális gépek leálljanak, amikor nincsenek használatban.

További információ: [A DevTest Labs használata teszteléshez](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Felhasználói profilok
Ez a cikk az Azure Lab Services különböző felhasználói profiljait ismerteti. 

### <a name="lab-account-owner"></a>Tesztkörnyezetfiók tulajdonosa
Általában a vállalat felhőerőforrásainak informatikai rendszergazdája, aki az Azure-előfizetés tulajdonosa, és a tesztkörnyezetfiók tulajdonosaként eljárva az alábbi feladatokat hajthatja végre:   

- Tesztkörnyezetfiókot állít be a vállalat számára.
- Felügyeli és konfigurálja a szabályzatokat minden tesztkörnyezetre vonatkozóan.
- Engedélyeket ad a vállalatban dolgozó személyeknek tesztkörnyezet létrehozására a tesztkörnyezetfiókban.

### <a name="lab-creator"></a>Tesztkörnyezet létrehozója 
Általában olyan felhasználók, mint a fejlesztési vezető/igazgató, oktató, Hackathon-verseny szervezője vagy online oktató hoznak létre tesztkörnyezeteket egy tesztkörnyezetfiókban. A tesztkörnyezet létrehozója az alábbi feladatokat hajthatja végre: 

- Tesztkörnyezetet hoz létre.
- Virtuális gépeket hoz létre a tesztkörnyezetben. 
- Telepíti a megfelelő szoftvereket a virtuális gépeken.
- Meghatározza, hogy ki férhet hozzá a tesztkörnyezethez.
- A tesztkörnyezetre mutató hivatkozást biztosít a tesztkörnyezet felhasználói számára.

### <a name="lab-user"></a>Tesztkörnyezet felhasználója
A tesztkörnyezet felhasználója az alábbi feladatokat hajthatja végre:

- Regisztrál a tesztkörnyezetben a tesztkörnyezet létrehozójától kapott regisztrációs hivatkozással. 
- Csatlakozik a tesztkörnyezetben lévő virtuális géphez, és azt fejlesztéshez, teszteléshez vagy osztálytermi munkához használja. 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](classroom-labs/tutorial-setup-classroom-lab.md)
- [Tesztkörnyezet beállítása](tutorial-create-custom-lab.md)
