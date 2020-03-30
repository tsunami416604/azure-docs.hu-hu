---
title: Alkalmazások áttelepítése és integrációja az Azure DevTest Labs ben
description: Ez a cikk útmutatást nyújt az Azure DevTest Labs-infrastruktúra szabályozásához az alkalmazások áttelepítése és integrációja összefüggésében.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644886"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Az Azure DevTest Labs infrastruktúrájának irányítása – Alkalmazások áttelepítése és integrációja
Miután létrejött a fejlesztői/tesztlaborkörnyezet, a következő kérdéseket kell meggondolnia:

- Hogyan hasznosítja a környezetet a projektcsapaton belül?
- Hogyan biztosíthatja, hogy kövesse a szükséges szervezeti szabályzatokat, és fenntartsa az agilitást az alkalmazás értékének növeléséhez?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Piactér-képek és egyéni lemezképek

### <a name="question"></a>Kérdés
Mikor érdemes azure piactéri lemezképet használni a saját egyéni szervezeti lemezképemmel szemben?

### <a name="answer"></a>Válasz
Az Azure Marketplace-t kell használni alapértelmezés szerint, kivéve, ha konkrét aggályok vagy szervezeti követelmények. Néhány gyakori példa: néhány gyakori példa;

- Összetett szoftverbeállítás, amely megköveteli, hogy egy alkalmazás az alapkép része legyen.
- Egy alkalmazás telepítése és beállítása több órát is igénybe vehet, ami nem hatékony számítási idő hozzáadása az Azure Marketplace-lemezképhez.
- A fejlesztőknek és a tesztelőknek gyorsan hozzá kell férniük egy virtuális géphez, és szeretnék minimalizálni az új virtuális gépek beállítási idejét.
- Megfelelőségi vagy szabályozási feltételek (például biztonsági házirendek), amelyeknek minden géphez érvényben kell lenniük.

Az egyéni képek használata nem tekinthető félvállról. Ők be extra összetettsége, ahogy most kell kezelni VHD fájlokat az alapul szolgáló alap képek. Azt is meg kell, hogy rendszeresen patch ezeket az alapképeket szoftverfrissítésekkel. Ezek a frissítések új operációsrendszer-frissítéseket tartalmaznak, valamint a szoftvercsomaghoz szükséges frissítéseket vagy konfigurációs módosításokat.

## <a name="formula-vs-custom-image"></a>Képlet és egyéni kép

### <a name="question"></a>Kérdés
Mikor érdemes képletet és egyéni képet használni?

### <a name="answer"></a>Válasz
Ebben a forgatókönyvben a döntő tényező általában a költség és az újrafelhasználás.

Ha van egy forgatókönyv, ahol sok felhasználó/labs igényel egy lemezképet, amely sok szoftver tetején az alap lemezkép, majd csökkentheti a költségeket egy egyéni lemezkép létrehozásával. Ez azt jelenti, hogy a rendszerkép egyszer jön létre. Csökkenti a virtuális gép beállítási idejét és a telepítés során futó virtuális gép miatt felmerülő költségeket.

A szoftvercsomag módosításainak gyakorisága azonban további tényező. Ha naponta futtatja a buildeket, és megköveteli, hogy a szoftver a felhasználók virtuális gépein legyen, fontolja meg egy képlet használatát egyéni lemezkép helyett.

## <a name="use-custom-organizational-images"></a>Egyéni szervezeti képek használata

### <a name="question"></a>Kérdés
Hogyan állíthatok be egy könnyen megismételhető folyamatot, hogy az egyéni szervezeti lemezképeimet devtest labs környezetbe hozzam?

### <a name="answer"></a>Válasz
Lásd [ezt a videót a Kép Gyár minta](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Ebben a forgatókönyvben egy speciális forgatókönyv, és a megadott parancsfájlok csak minta parancsfájlok. Ha bármilyen módosításra van szükség, a környezetben használt parancsfájlokat kell kezelnie és karbantartania.

A DevTest Labs használata egyéni lemezkép-folyamat létrehozásához az Azure-folyamatokban:

- [Bevezetés: Percek alatt elkészíti a virtuális gépeket egy lemezkép-gyár beállításával az Azure DevTest Labsben](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Image Factory - 2. Az Azure-folyamatok és a gyári labor beállítása virtuális gépek létrehozásához](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Image Factory - 3. rész: Egyéni képek mentése és terjesztése több Labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Videó: Egyéni lemezképgyár az Azure DevTest Labs segítségével](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>A hálózati konfiguráció beállításának mintái

### <a name="question"></a>Kérdés
Hogyan biztosítható, hogy a fejlesztő és a tesztelési virtuális gépek nem tudják elérni a nyilvános internetet? Vannak-e ajánlott minták a hálózati konfiguráció beállításához?

### <a name="answer"></a>Válasz
Igen. Két szempontot kell figyelembe venni : bejövő és kimenő forgalmat.

**Bejövő forgalom** – Ha a virtuális gép nem rendelkezik nyilvános IP-címmel, akkor nem érhető el az interneten keresztül. Közös megközelítés annak biztosítása, hogy egy előfizetés-szintű szabályzat van beállítva, úgy, hogy egyetlen felhasználó sem képes létrehozni egy nyilvános IP-címet.

**Kimenő forgalom** – Ha meg szeretné akadályozni, hogy a virtuális gépek közvetlenül a nyilvános internetre menjenek, és a forgalmat vállalati tűzfalon keresztül kényszerítsék ki, akkor a helyszíni forgalmat expressz útvonalon vagy VPN-en keresztül irányíthatja, kényszerített útválasztás használatával.

> [!NOTE]
> Ha olyan proxykiszolgálóval rendelkezik, amely blokkolja a proxybeállítások nélküli forgalmat, ne felejtsen el kivételeket hozzáadni a tesztelem-tárfiókhoz.

A hálózati biztonsági csoportokat virtuális gépekhez vagy alhálózatokhoz is használhatja. Ez a lépés egy további védelmi réteget ad hozzá a forgalom engedélyezéséhez / blokkolásához.

## <a name="new-vs-existing-virtual-network"></a>Új és meglévő virtuális hálózat

### <a name="question"></a>Kérdés
Mikor hozzak létre új virtuális hálózatot a DevTest Labs környezethez, szemben egy meglévő virtuális hálózat használatával?

### <a name="answer"></a>Válasz
Ha a virtuális gépek nek kell a meglévő infrastruktúrával, majd érdemes egy meglévő virtuális hálózat a DevTest Labs környezetben. Emellett az ExpressRoute használata esetén érdemes lehet minimalizálni a virtuális hálózatok / alhálózatok mennyiségét, hogy ne tördelje szét az előfizetésekben való használatra kijelölt IP-címterületet. Érdemes megfontolni a virtuális hálózat társviszony-létesítési minta itt (Hub-Küllőmodell). Ez a megközelítés lehetővé teszi a virtuális hálózat/alhálózati kommunikációt egy adott régión belüli előfizetések között, bár a régiók közötti társviszony-létesítés az Azure-hálózategy közelgő funkció.

Ellenkező esetben minden DevTest Labs-környezet saját virtuális hálózattal rendelkezhet. Vegye figyelembe azonban, hogy előfizetésenként a virtuális hálózatok száma [korlátozott.](../azure-resource-manager/management/azure-subscription-service-limits.md) Az alapértelmezett összeg 50, bár ez a korlát 100-ra emelhető.

## <a name="shared-public-or-private-ip"></a>Megosztott, nyilvános vagy privát IP

### <a name="question"></a>Kérdés
Mikor érdemes megosztott IP-t és nyilvános IP-t használni a privát IP-címhez?

### <a name="answer"></a>Válasz
Ha helyek közötti VPN-t vagy Expressz-útvonalat használ, fontolja meg a privát IP-k használatát, hogy a gépek elérhetők legyenek a belső hálózaton keresztül, és ne legyenek elérhetők a nyilvános interneten keresztül.

> [!NOTE]
> A labortulajdonosok módosíthatják ezt az alhálózati szabályzatot annak érdekében, hogy véletlenül senki ne hozza létre a virtuális gépek nyilvános IP-címét. Az előfizetés tulajdonosának létre kell hoznia egy előfizetési szabályzatot, amely megakadályozza a nyilvános IP-k létrehozását.

Megosztott nyilvános IP-cím használata esetén a tesztkörnyezetben lévő virtuális gépek nyilvános IP-címet osztanak meg. Ez a megközelítés akkor lehet hasznos, ha el kell kerülnie a nyilvános IP-címek re vonatkozó korlátozások megszegését egy adott előfizetéshez.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>A virtuális gépek száma felhasználónként vagy laboronként i.

### <a name="question"></a>Kérdés
Van-e szabály aszerint, hogy hány virtuális gépet kell beállítani felhasználónként vagy laboronként?

### <a name="answer"></a>Válasz
A felhasználónkénti vagy laboronkénti virtuális gépek számának vizsgálatakor három fő probléma merül fel:

- A **teljes költség,** amelyet a csapat a laborban lévő erőforrásokra költhet. Sok gépet könnyű felpörgetni. A költségek szabályozása érdekében az egyik mechanizmus a virtuális gépek felhasználónkénti és/vagy laboronkénti számának korlátozása
- A tesztkörnyezetben lévő virtuális gépek teljes számát befolyásolja az [elérhető előfizetési szintű kvóták.](../azure-resource-manager/management/azure-subscription-service-limits.md) A felső korlátok egyike 800 erőforráscsoport előfizetésenként. DevTest Labs jelenleg létrehoz egy új erőforráscsoportot minden virtuális gép (kivéve, ha megosztott nyilvános IP-k vannak használva). Ha egy előfizetésben 10 labor oka van, a laborok elférnek körülbelül 79 virtuális gép minden egyes laborban (800 felső korlát – 10 erőforráscsoportok a 10 labs magukat) = 79 virtuális gépek laboronként.
- Ha a tesztkörnyezet expressz útvonalon keresztül csatlakozik a helyszíni helyekhez (például a virtuális hálózat/alhálózat **számára meghatározott IP-címterek állnak rendelkezésre.** Annak érdekében, hogy a laborban lévő virtuális gépek ne hozzanak létre (hiba: nem lehet leadni az IP-címet), a labortulajdonosok megadhatják a laboronkénti maximális virtuális gépeket a rendelkezésre álló IP-címterülethez igazítva.

## <a name="use-resource-manager-templates"></a>Resource Manager-sablonok használata

### <a name="question"></a>Kérdés
Hogyan használhatom az Erőforrás-kezelő sablonjait a DevTest Labs környezetben?

### <a name="answer"></a>Válasz
Az Erőforrás-kezelő-sablonokat egy DevTest Labs-környezetben telepíti a DevTest labs cikk [környezetek szolgáltatásában](devtest-lab-test-env.md) említett lépések használatával. Alapvetően ellenőrizze a Resource Manager-sablonokat egy Git-tárházba (azure-tárházba vagy GitHubba), és hozzáadhat egy [privát tárházat a sablonokhoz](devtest-lab-test-env.md) a laborba.

Ez a forgatókönyv nem lehet hasznos, ha fejlesztői gépek üzemeltetéséhez devtest labs használatával, de hasznos lehet, ha egy átmeneti környezet, amely az éles környezetben reprezentatív.

Azt is érdemes megjegyezni, hogy a virtuális gépek száma laboronként vagy felhasználónként i beállítás csak korlátozza a gépek száma natívan létrehozott a laborban is, és nem olyan környezetben (Resource Manager sablonok).

## <a name="next-steps"></a>További lépések
Lásd: [Környezetek használata a DevTest Labs ben.](devtest-lab-test-env.md)