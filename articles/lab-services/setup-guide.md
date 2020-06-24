---
title: Gyorsított tantermi tesztkörnyezet telepítési útmutatója Azure Lab Services
description: Ez az útmutató segít a labor-készítőknek az iskolán belüli használatra szolgáló labor-fiók gyors beállításában.
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
ms.date: 05/19/2020
ms.author: spelluru
ms.openlocfilehash: 751d55493b1bdb06dfe61d9f951846e13e1e5d72
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897459"
---
# <a name="classroom-lab-setup-guide"></a>A tanterem Lab telepítési útmutatója

A laborok tanulók számára történő közzétételének folyamata akár több órát is igénybe vehet, attól függően, hogy hány virtuális gépet szeretne létrehozni a laborban. Engedélyezzen legalább egy napot a tesztkörnyezet beállításához, hogy megfelelően működjön, és hogy elegendő idő legyen a tanulók virtuális gépei közzétételére.

## <a name="understand-the-lab-requirements-of-your-class"></a>Az osztály laboratóriumi követelményeinek megismerése

Új Labor létrehozása előtt vegye figyelembe az alábbi kérdéseket.

### <a name="what-software-requirements-does-the-class-have"></a>Milyen szoftverek szükségesek az osztályban?

Az osztály tanulási céljai alapján döntse el, hogy mely operációs rendszereket, alkalmazásokat és eszközöket kell telepíteni a labor virtuális gépekre. A labor virtuális gépek beállításához három lehetőség közül választhat:

- **Azure Marketplace-rendszerkép használata**: az Azure Marketplace több száz képet biztosít, amelyeket a labor létrehozásakor használhat. Egyes osztályok esetében előfordulhat, hogy az egyik rendszerkép már tartalmaz mindent, amire szüksége van az osztályhoz.

- **Új egyéni rendszerkép létrehozása**: létrehozhat egy saját egyéni rendszerképet az Azure Marketplace-rendszerkép kiindulási pontként való használatával, és testre szabhatja azt a további szoftverek telepítésével és a konfiguráció módosításával.

- **Meglévő egyéni rendszerkép használata**: felhasználhatja a korábban létrehozott meglévő egyéni lemezképeket, illetve azokat, amelyeket más rendszergazdák vagy oktatók hoztak létre az iskolában. Ehhez a rendszergazdának megosztott képtárat kell konfigurálnia, amely az egyéni lemezképek mentésének tárháza.

> [!NOTE]
> A rendszergazdák felelősek az Azure Marketplace-lemezképek és az Egyéni rendszerképek engedélyezéséhez, hogy azok használhatók legyenek. Az informatikai részleg koordinálásával gondoskodhat arról, hogy a szükséges lemezképek engedélyezve legyenek. A létrehozott egyéni rendszerképek automatikusan engedélyezve lesznek a saját laboratóriumokban való használatra.

### <a name="what-hardware-requirements-does-the-class-have"></a>Milyen hardverkövetelmények vonatkoznak az osztályra?

Számos különböző számítási méret közül választhat:

- Beágyazott virtualizációs méretek, így hozzáférést biztosíthat a tanulók számára egy olyan virtuális géphez, amely több, beágyazott virtuális gép üzemeltetésére képes. Használhatja például ezt a számítási méretet a hálózatkezelési tanfolyamok esetében.

- GPU-méretek, így a tanulók használhatnak számítógép-igényes típusú alkalmazásokat. Ilyen lehet például a mesterséges intelligencia és a gépi tanulás.

A rendelkezésre álló számítási méretek teljes listájának megtekintéséhez tekintse meg a [virtuális gép méretezési](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) útmutatóját.

> [!NOTE]
> A labor régiójától függően kevesebb számítási méret érhető el, mivel ez régiónként eltérő lehet. Általában az igényeihez legközelebb eső legkisebb számítási méretet kell kiválasztania. A Azure Lab Services segítségével később is beállíthat egy új labort, ha szükséges, egy másik számítási kapacitással.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Milyen függőségek vannak az osztályban a külső Azure-vagy hálózati erőforrásokon?

Ha a tesztkörnyezet külső erőforrásokat (például adatbázist, fájlmegosztást vagy licencelési kiszolgálót) kell használnia, egyeztessen a rendszergazdával, és győződjön meg arról, hogy a labor hozzáfér ezekhez az erőforrásokhoz.

A virtuális hálózat által *nem* védett Azure-erőforrásokhoz való hozzáféréshez a rendszergazdának nem kell további konfigurációkat keresnie. Ezeket az erőforrásokat a nyilvános interneten keresztül érheti el.

> [!NOTE]
> Érdemes megfontolni, hogy az erőforrást közvetlenül a virtuális gépen biztosíthatja-e, ha csökkenti a labor függőségeit a külső erőforrásokra. Ha például el szeretné távolítani az adatok külső adatbázisból való beolvasásának szükségességét, az adatbázist közvetlenül a virtuális gépre is telepítheti.  

### <a name="how-will-costs-be-controlled"></a>Hogyan történik a költségek szabályozása?

A labor Services az utólagos elszámolású díjszabási modellt használja, ami azt jelenti, hogy csak akkor kell fizetnie, amikor egy Tesztkörnyezet virtuális gépe fut. A költségek szabályozása érdekében három lehetőség közül választhat, amelyek általában egymással együtt használatosak:

- **Schedule**: az ütemterv lehetővé teszi, hogy automatikusan vezérelje a laborok virtuális gépei indításának és leállításának idejét.
- **Kvóta**: a kvóta azt határozza meg, hogy a tanulók hány óráig férhetnek hozzá egy virtuális géphez az ütemezett órákon kívül. Ha a kvóta eléri a tanulók általi használatot, a rendszer automatikusan leállítja a virtuális gépet. A tanuló nem tudja újraindítani a virtuális gépet, ha a kvóta megnövekszik.
- **Automatikus leállítás**: Ha engedélyezve van, az automatikus leállítási beállítás azt eredményezi, hogy a Windows rendszerű virtuális gépek bizonyos időtartam után automatikusan leállnak, miután egy tanuló leválasztott egy RDP protokoll (RDP) munkamenetből. Alapértelmezés szerint ez a beállítás le van tiltva.  

    > [!NOTE]
    > Ez a beállítás jelenleg csak a Windows esetében létezik.

### <a name="how-will-students-save-their-work"></a>Hogyan fogja megmenteni a tanulókat a munkájukat?

A tanulók mindegyike saját virtuális gépet rendelt hozzájuk, amelyet a labor élettartama során rendeltek hozzájuk. Választhatnak:

- Mentse közvetlenül a virtuális gépre.
- Mentse külső helyre, például OneDrive vagy GitHubra.

A OneDrive automatikusan konfigurálható a tanulók számára a laboratóriumi virtuális gépeken.

> [!NOTE]
> Annak biztosítása érdekében, hogy a tanulók továbbra is hozzáférjenek a mentett munkához a laboron kívül, és az osztály befejezése után azt javasoljuk, hogy a tanulók a munkájukat egy külső adattárba mentse.

### <a name="how-will-students-connect-to-their-vm"></a>Hogyan csatlakozhatnak a tanulók a virtuális géphez?

A Windows rendszerű virtuális gépekhez való RDP esetén javasoljuk, hogy a tanulók a [Microsoft távoli asztal ügyfelet](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)használják. Távoli asztal-ügyfél támogatja a Mac, a Chromebook és a Windows rendszert.

Linux rendszerű virtuális gépek esetén a tanulók SSH-vagy RDP-t is használhatnak. Ahhoz, hogy a tanulók RDP-kapcsolaton keresztül csatlakozhassanak, telepítenie és konfigurálnia kell a szükséges RDP-és GUI-csomagokat.

## <a name="set-up-your-lab"></a>Tesztkörnyezet beállítása

Miután megértette az osztály laborjának követelményeit, készen áll a beállításra. Az ebben a szakaszban található hivatkozásokat követve megtudhatja, hogyan állíthatja be a labort.

1. **Hozzon létre egy labort.** További útmutatásért tekintse meg a [tantermi labor létrehozásával](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) kapcsolatos útmutatót.

    > [!NOTE]
    > Ha az osztályban beágyazott virtualizálás szükséges, tekintse meg a [beágyazott virtualizálás engedélyezése](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)című témakör lépéseit.

1. **Képek testreszabása és laboratóriumi virtuális gépek közzététele.** Kapcsolódjon egy olyan speciális virtuális géphez, amelyet a sablon virtuális gépe nevez. Tekintse meg a következő útmutatók lépéseit:
    - [Sablonos virtuális gép létrehozása és kezelése](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Megosztott rendszerkép-katalógus használata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Ha Windows rendszert használ, a [Windows-sablon virtuális gép előkészítésének](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)utasításait is látnia kell. Ezek az utasítások a tanulók számára a OneDrive és az Office használatának beállításához szükséges lépéseket tartalmazzák.

1. **VIRTUÁLIS gépek készletének és kapacitásának kezelése.** A virtuálisgép-kapacitást egyszerűen, az osztálya igény szerint méretezheti. Ne feledje, hogy a virtuális gép kapacitása több óráig is eltarthat, mivel ez új virtuális gépek beállítását foglalja magában. Tekintse meg a virtuálisgép- [Készlet beállítása és kezelése](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)című témakör lépéseit.

1. **Labor-felhasználók hozzáadása és kezelése.** Ha felhasználókat szeretne felvenni a laborba, tekintse át az alábbi oktatóanyagok lépéseit:
   - [Felhasználók hozzáadása a laborhoz](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Meghívók küldése a felhasználóknak](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    További információ a tanulók által használható fiókok típusairól: Student- [fiókok](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **A Cost Controls beállítása.** A labor költségeinek szabályozásához állítsa be az ütemterveket, a kvótákat és az automatikus leállítást. Lásd az alábbi oktatóanyagokat:

   - [Ütemterv beállítása](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A telepített operációs rendszer típusától függően előfordulhat, hogy a virtuális gép elindításához több percet is igénybe vehet. Annak biztosítása érdekében, hogy a labor virtuális gépek az ütemezett órákon belül használatra készek legyenek, javasoljuk, hogy 30 perccel előre indítsa el a virtuális gépeket.

   - [Kvóták beállítása a felhasználók](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) számára, és [további kvóta beállítása egy adott felhasználó számára](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Az ütemtervek és a kvóták nem érvényesek a sablon virtuális gépre, de az automatikus leállítási beállítások érvényesek. 
        > 
        > Labor létrehozásakor a rendszer létrehozza a sablon virtuális gépet, de az nem indult el. Elindíthatja, csatlakozhat hozzá, és telepítheti az előfeltételként szükséges szoftvereket a laborhoz, majd közzéteheti azt. Amikor közzéteszi a sablon virtuális gépet, az automatikusan leáll, ha még nem tette meg. 
        > 
        > A sablon virtuális gépei a futtatáskor **költségekkel** járnak, ezért győződjön meg arról, hogy a sablon virtuális gépe leáll, ha nincs szükség a futtatására. 


1. **Használja az irányítópultot.** Útmutatásért lásd: [a tesztkörnyezet irányítópultjának használata](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Az irányítópulton látható becsült költségek azt a maximális díjat jelentik, amelyet a tanulók a laborban használhatnak. Például nem számítunk fel díjat a tanulók által fel *nem* használt időtartamokra. A becsült költségek *nem* tükrözik a virtuális gép, a megosztott képkatalógus vagy a tesztkörnyezet létrehozója által elindítható használati díjakat.

## <a name="next-steps"></a>További lépések

- [Osztályterem-tesztkörnyezet használatának követése](tutorial-track-usage.md)
  
- [Osztályterem-tesztkörnyezet elérése](tutorial-connect-virtual-machine-classroom-lab.md)