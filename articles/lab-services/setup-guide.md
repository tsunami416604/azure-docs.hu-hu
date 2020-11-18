---
title: Gyorsított tesztkörnyezet beállítási útmutatója Azure Lab Services
description: Ez az útmutató segít a labor-készítőknek az iskolán belüli használatra szolgáló labor-fiók gyors beállításában.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8ef168aefb69df32f57b623bb488adbb97cbd411
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659675"
---
# <a name="lab-setup-guide"></a>Labor telepítési útmutatója

A laborok tanulók számára történő közzétételének folyamata akár több órát is igénybe vehet.  Az idő mennyisége a laborban létrehozandó virtuális gépek számától (VM) függ. Engedélyezzen legalább egy napot a labor beállításához, hogy megfelelően működjön, és hogy elegendő idő legyen a tanulói virtuális gépek közzétételére.

## <a name="understand-the-lab-requirements-of-your-class"></a>Az osztály laboratóriumi követelményeinek megismerése

Új Labor létrehozása előtt vegye figyelembe az alábbi kérdéseket.

### <a name="what-software-requirements-does-the-class-have"></a>Milyen szoftverek szükségesek az osztályban?

Az osztály tanulási céljai alapján döntse el, hogy mely operációs rendszereket, alkalmazásokat és eszközöket kell telepíteni a labor virtuális gépekre. A labor virtuális gépek beállításához három lehetőség közül választhat:

- **Azure Marketplace-rendszerkép használata**: az Azure Marketplace több száz képet biztosít, amelyeket a labor létrehozásakor használhat. Egyes osztályok esetében előfordulhat, hogy az egyik rendszerkép már tartalmaz mindent, amire szüksége van az osztályhoz.

- **Új egyéni rendszerkép létrehozása**: létrehozhat egy saját egyéni rendszerképet az Azure Marketplace-rendszerkép kiindulási pontként való használatával, és testre szabhatja azt a további szoftverek telepítésével és a konfiguráció módosításával.

- **Meglévő egyéni rendszerkép használata**: felhasználhatja a korábban létrehozott meglévő egyéni lemezképeket, illetve azokat, amelyeket más rendszergazdák vagy oktatók hoztak létre az iskolában. Egyéni lemezképek használatához a rendszergazdáknak létre kell hozniuk egy megosztott képtárat.  A megosztott képgyűjtemény az Egyéni rendszerképek mentéséhez használt tárház.

> [!NOTE]
> A rendszergazdák felelősek az Azure Marketplace-lemezképek és az Egyéni rendszerképek engedélyezéséhez, hogy azok használhatók legyenek. Az informatikai részleg koordinálásával gondoskodhat arról, hogy a szükséges lemezképek engedélyezve legyenek. A létrehozott egyéni rendszerképek automatikusan engedélyezve lesznek a saját laboratóriumokban való használatra.

### <a name="what-hardware-requirements-does-the-class-have"></a>Milyen hardverkövetelmények vonatkoznak az osztályra?

Különböző számítási méretek közül választhat:

- Beágyazott virtualizációs méretek, így hozzáférést biztosíthat a tanulók számára egy olyan virtuális géphez, amely több, beágyazott virtuális gépet is üzemeltet. Például használhatja ezt a számítási méretet a hálózatkezeléshez vagy az etikai hackelési osztályokhoz.

- GPU-méretek, így a tanulók használhatnak számítógép-igényes típusú alkalmazásokat. Ezt a lehetőséget például gyakran használják mesterséges intelligenciával és gépi tanulással.

A virtuális gép megfelelő méretének kiválasztásával kapcsolatos útmutatásért olvassa el a következő cikkeket:
- [Virtuális gép méretezése](./administrator-guide.md#vm-sizing)
- [Áthelyezés fizikai laborból a Azure Lab Servicesba](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

> [!NOTE]
> A labor régiójától függően kevesebb számítási méret érhető el, mivel ez régiónként eltérő lehet. Általában az igényeihez legközelebb eső legkisebb számítási méretet kell kiválasztania. A Azure Lab Services segítségével később is beállíthat egy új labort, ha szükséges, egy másik számítási kapacitással.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Milyen függőségek vannak az osztályban a külső Azure-vagy hálózati erőforrásokon?
Előfordulhat, hogy a laboratóriumi virtuális gépeknek hozzá kell férniük a külső erőforrásokhoz, például az adatbázishoz, a fájlmegosztáshoz vagy a licencelési kiszolgálóhoz való hozzáféréshez.  Ha engedélyezni szeretné, hogy a labor virtuális gépei külső erőforrásokat használjanak, egyeztessen a rendszergazdával.

> [!NOTE]
> Érdemes megfontolni, hogy az erőforrást közvetlenül a virtuális gépen biztosíthatja-e, ha csökkenti a labor függőségeit a külső erőforrásokra. Ha például el szeretné távolítani az adatok külső adatbázisból való beolvasásának szükségességét, az adatbázist közvetlenül a virtuális gépre is telepítheti.  

### <a name="how-will-costs-be-controlled"></a>Hogyan történik a költségek szabályozása?
A labor Services az utólagos elszámolású díjszabási modellt használja, ami azt jelenti, hogy csak akkor kell fizetnie, amikor egy Tesztkörnyezet virtuális gépe fut. A költségek szabályozása érdekében három lehetőség közül választhat, amelyek jellemzően együtt használatosak:

- **Schedule**: az ütemterv lehetővé teszi, hogy automatikusan vezérelje a laborok virtuális gépei indításának és leállításának idejét.
- **Kvóta**: a kvóta azt határozza meg, hogy a tanulók hány óráig férhetnek hozzá egy virtuális géphez az ütemezett órákon kívül.  Ha egy tanuló a virtuális gépet használja, és eléri a kvótát, a virtuális gép automatikusan leáll.  A tanuló nem tudja újraindítani a virtuális gépet, ha a kvóta megnövekszik.
- Automatikus **Leállítás**: Ha engedélyezve van, az automatikus leállítás beállítás hatására a Windows rendszerű virtuális gépek automatikusan leállnak a tanulók RDP protokoll (RDP) munkamenetből való leválasztását követően. Alapértelmezés szerint ez a beállítás le van tiltva.

További információért olvassa el a következő cikkeket:
- [A költségek megbecslése](./cost-management-guide.md#estimate-the-lab-costs)
- [Költségek kezelése](./cost-management-guide.md#manage-costs)

### <a name="how-will-students-save-their-work"></a>Hogyan fogja megmenteni a tanulókat a munkájukat?
A tanulók mindegyike saját virtuális gépet rendelt hozzájuk, amelyet a labor élettartama során rendeltek hozzájuk. Választhatnak:

- Mentse közvetlenül a virtuális gépre.
- Mentse külső helyre, például OneDrive vagy GitHubra.

A OneDrive automatikusan konfigurálható a tanulók számára a laboratóriumi virtuális gépeken.

> [!NOTE]
> Annak biztosítása érdekében, hogy a tanulók továbbra is hozzáférjenek a mentett munkához a laboron kívül, és az osztály befejezése után azt javasoljuk, hogy a tanulók a munkájukat egy külső adattárba mentse.

### <a name="how-will-students-connect-to-their-vm"></a>Hogyan csatlakozhatnak a tanulók a virtuális géphez?
A Windows rendszerű virtuális gépekhez való RDP esetén javasoljuk, hogy a tanulók a [Microsoft távoli asztal ügyfelet](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)használják. Távoli asztal-ügyfél támogatja a Mac, a Chromebook és a Windows rendszert.

Linux rendszerű virtuális gépek esetén a tanulók SSH-vagy RDP-t is használhatnak. Ahhoz, hogy a tanulók RDP-kapcsolaton keresztül csatlakozhassanak, telepítenie és konfigurálnia kell a szükséges RDP-és GUI-csomagokat.

### <a name="will-students-also-be-using-microsoft-teams"></a>A tanulók a Microsoft csapatait is használják?
A Azure Lab Services együttműködik a Microsoft Teams szolgáltatással, így a tanárok a csapaton belül hozhatnak létre és kezelhetik a laborokat.  Hasonlóképpen a tanulók a csapatokon belül is hozzáférhetnek a laborhoz.

További információkat a következő cikkekben talál:
- [Azure Lab Services a Microsoft Teams szolgáltatáson belül](./lab-services-within-teams-overview.md)

## <a name="set-up-your-lab"></a>Tesztkörnyezet beállítása

Miután megértette az osztály laborjának követelményeit, készen áll a beállításra. Az ebben a szakaszban található hivatkozásokat követve megtudhatja, hogyan állíthatja be a labort.  Figyelje meg, hogy a különböző lépéseket a rendszer attól függően hajtja végre, hogy a csapatokon belül használja a laborokat.

1. **Hozzon létre egy labort.** Tekintse meg a labor létrehozásával kapcsolatos oktatóanyagokat:
    - [Hozzon létre egy tantermi labort](./tutorial-setup-classroom-lab.md#create-a-classroom-lab) az utasításokhoz.
    - [Labor létrehozása a Teamsből](./how-to-get-started-create-lab-within-teams.md)

    > [!NOTE]
    > Ha az osztályban beágyazott virtualizálás szükséges, tekintse meg a [beágyazott virtualizálás engedélyezése](./how-to-enable-nested-virtualization-template-vm.md)című témakör lépéseit.

1. **Képek testreszabása és laboratóriumi virtuális gépek közzététele.** Kapcsolódjon egy olyan speciális virtuális géphez, amelyet a sablon virtuális gépe nevez. Tekintse meg a következő útmutatók lépéseit:
    - [Sablonos virtuális gép létrehozása és kezelése](./tutorial-setup-classroom-lab.md#publish-the-template-vm)
    - [Megosztott rendszerkép-katalógus használata](./how-to-use-shared-image-gallery.md)

    > [!NOTE]
    > Ha Windows rendszert használ, a [Windows-sablon virtuális gép előkészítésének](./how-to-prepare-windows-template.md)utasításait is látnia kell. Ezek az utasítások a tanulók számára a OneDrive és az Office használatának beállításához szükséges lépéseket tartalmazzák.

1. **VIRTUÁLIS gépek készletének és kapacitásának kezelése.** A virtuálisgép-kapacitást egyszerűen, az osztálya igény szerint méretezheti. Ne feledje, hogy a virtuális gép kapacitása több óráig is eltarthat, mivel az új virtuális gépek beállítása folyamatban van. Tekintse meg a következő cikk lépéseit:
    - [Virtuálisgép-készlet beállítása és kezelése](./how-to-set-virtual-machine-passwords.md)
    - [Virtuálisgép-készlet kezelése a labor Servicesben a csapatoktól](./how-to-manage-vm-pool-within-teams.md)

1. **Labor-felhasználók hozzáadása és kezelése.** Ha felhasználókat szeretne felvenni a laborba, tekintse át az alábbi oktatóanyagok lépéseit:
   - [Felhasználók hozzáadása a laborhoz](./tutorial-setup-classroom-lab.md#add-users-to-the-lab)
   - [Meghívók küldése a felhasználóknak](./tutorial-setup-classroom-lab.md#send-invitation-emails-to-users)
   - [A labor Services felhasználói listájainak kezelése a csapatokból](./how-to-manage-user-lists-within-teams.md)

    További információ a tanulók által használható fiókok típusairól: Student- [fiókok](./how-to-configure-student-usage.md#student-accounts).
  
1. **A Cost Controls beállítása.** A labor költségeinek szabályozásához állítsa be az ütemterveket, a kvótákat és az automatikus leállítást. Lásd az alábbi oktatóanyagokat:

   - [Ütemterv beállítása](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)

        > [!NOTE]
        > A telepített operációs rendszer típusától függően előfordulhat, hogy a virtuális gép elindításához több percet is igénybe vehet. Annak biztosítása érdekében, hogy a labor virtuális gépek az ütemezett órákon belül használatra készek legyenek, javasoljuk, hogy 30 perccel előre indítsa el a virtuális gépeket.

   - [Kvóták beállítása a felhasználók](./how-to-configure-student-usage.md#set-quotas-for-users) számára, és [további kvóta beállítása egy adott felhasználó számára](./how-to-configure-student-usage.md#set-additional-quotas-for-specific-users)
  
   - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](./how-to-enable-shutdown-disconnect.md)

        > [!NOTE]
        > Az ütemtervek és a kvóták nem érvényesek a sablon virtuális gépre, de az automatikus leállítási beállítások érvényesek. 
        > 
        > Labor létrehozásakor a rendszer létrehozza a sablon virtuális gépet, de az nem indult el. Elindíthatja, csatlakozhat hozzá, és telepítheti az előfeltételként szükséges szoftvereket a laborhoz, majd közzéteheti azt. Amikor közzéteszi a sablon virtuális gépet, az automatikusan leáll, ha még nem tette meg. 
        > 
        > A sablon virtuális gépei a futtatáskor **költségekkel** járnak, ezért győződjön meg arról, hogy a sablon virtuális gépe leáll, ha nincs szükség a futtatására.

    - [Labor Services-ütemtervek létrehozása és kezelése a csapatokon belül](./how-to-create-schedules-within-teams.md) 

1. **Használja az irányítópultot.** Útmutatásért lásd: [a tesztkörnyezet irányítópultjának használata](./use-dashboard.md).

    > [!NOTE]
    > Az irányítópulton látható becsült költségek azt a maximális díjat jelentik, amelyet a tanulók a laborban használhatnak. Például nem számítunk fel díjat a tanulók által fel *nem* használt időtartamokra. A becsült költségek *nem* tükrözik a virtuális gép, a megosztott képkatalógus vagy a tesztkörnyezet létrehozója által elindítható használati díjakat.

## <a name="next-steps"></a>Következő lépések

- [Osztályterem-tesztkörnyezet használatának követése](tutorial-track-usage.md)
  
- [Osztályterem-tesztkörnyezet elérése](tutorial-connect-virtual-machine-classroom-lab.md)