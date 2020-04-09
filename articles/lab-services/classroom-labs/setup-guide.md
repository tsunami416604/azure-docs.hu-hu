---
title: Gyorsított tantermi laborbeállítási útmutató az Azure Lab Services-hez
description: Ez az útmutató segít a laborkészítőknek egy tesztkörnyezet-fiók gyors beállításában az iskolájukon belül való használatra.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878102"
---
# <a name="classroom-lab-setup-guide"></a>Tantermi laborbeállítási útmutató

A labor okainak a diákok számára történő közzétételének folyamata akár több órát is igénybe vehet, attól függően, hogy hány virtuális gép (VM) jön létre a laborban. Legalább egy nap, hogy hozzanak létre egy labor, annak érdekében, hogy megfelelően működik, és hogy elegendő idő, hogy tegye közzé a diákok virtuális gépek.

## <a name="understand-the-lab-requirements-of-your-class"></a>Ismerje meg az osztály laborkövetelményeit

Mielőtt új labort állít an- t, vegye figyelembe a következő kérdéseket.

### <a name="what-software-requirements-does-the-class-have"></a>Milyen szoftverkövetelményekkel rendelkezik az osztály?

Az osztály tanulási céljai alapján döntse el, hogy melyik operációs rendszert, alkalmazásokat és eszközöket kell telepíteni a labor virtuális gépein. A tesztkörnyezet virtuális gépeinek beállításához három lehetőség közül választhat:

- **Azure Piactér-lemezkép használata:** Az Azure Marketplace több száz lemezképet biztosít, amelyeket labor létrehozásakor használhat. Egyes osztályok, az egyik ilyen képek már tartalmazmindent, amire szüksége van az osztályban.

- **Hozzon létre egy új egyéni lemezképet:** Létrehozhatja saját egyéni lemezképét egy Azure Marketplace-lemezkép kiindulási pontként való használatával, és további szoftverek telepítésével és konfigurációs módosításokkal testre szabhatja.

- **Meglévő egyéni lemezkép használata:** Újra felhasználhatja a korábban létrehozott, illetve az iskola más rendszergazdái vagy oktatói által létrehozott egyéni lemezképeket. Ehhez a rendszergazdáknak konfigurálniuk kell egy megosztott képgalériát, amely az egyéni lemezképek mentéséhez szükséges tárház.

> [!NOTE]
> A rendszergazdák felelősek az Azure Marketplace-lemezképek és egyéni lemezképek engedélyezéséért, hogy használhassa őket. Egyeztesse az informatikai részleggel annak érdekében, hogy a szükséges képek engedélyezve legyenek. Egyéni létrehozott képek automatikusan engedélyezve vannak a saját laborokban való használatra.

### <a name="what-hardware-requirements-does-the-class-have"></a>Milyen hardverkövetelményekkel rendelkezik az osztály?

A számítási méretek széles skálája közül választhat:

- Egymásba ágyazott virtualizációs méretek, így hozzáférést biztosíthat a diákok egy virtuális gép, amely képes több beágyazott virtuális gépek üzemeltetésére. Ezt a számítási méretet például hálózati tanfolyamokhoz használhatja.

- GPU-méretek, hogy a diákok számítógép-igényes típusú alkalmazásokat használhassanak. Ez a választás például megfelelő lehet a mesterséges intelligencia és a gépi tanulás számára.

Tekintse meg a [virtuális gép méretezési](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) útmutatóját az elérhető számítási méretek teljes listájának megtekintéséhez.

> [!NOTE]
> A labor régiójától függően előfordulhat, hogy kevesebb számítási méret érhető el, mivel ez régiónként változik. Általában válassza ki a legkisebb számítási méretet, amely a legközelebb áll az igényeinek. Az Azure Lab Services segítségével szükség esetén szükség esetén szükség esetén egy másik számítási kapacitással rendelkező új labort is beállíthat.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Milyen függőségekkel rendelkezik az osztály külső Azure- vagy hálózati erőforrásoktól?

Ha a tesztkörnyezet virtuális gépei külső erőforrásokat, például adatbázist, fájlmegosztást vagy licencelési kiszolgálót kell használniuk, egyeztessenek a rendszergazdákkal, hogy a tesztkörnyezet hozzáférhessen ezekhez az erőforrásokhoz.

A virtuális hálózat által *nem* védett Azure-erőforrásokhoz való hozzáféréshez nem kell további konfigurációt keresnie a rendszergazdáknak. Ezeket az erőforrásokat a nyilvános interneten keresztül érheti el.

> [!NOTE]
> Fontolja meg, hogy csökkentheti-e a tesztkörnyezet függőségeit a külső erőforrásoktól azáltal, hogy az erőforrást közvetlenül a virtuális gépre biztosítja. Például, hogy ne kelljen adatokat olvasni egy külső adatbázisból, telepítheti az adatbázist közvetlenül a virtuális gépre.  

### <a name="how-will-costs-be-controlled"></a>Hogyan ellenőrzik a költségeket?

A Lab Services használatalapú díjszabási modellt használ, ami azt jelenti, hogy csak a tesztkörnyezet virtuális gépének futtatásáért kell fizetnie. A költségek szabályozásához három lehetőség áll rendelkezésre, amelyeket általában egymással együtt használnak:

- **Ütemezés:** Az ütemezés lehetővé teszi, hogy automatikusan szabályozhatja, hogy a laborok virtuális gépei mikor indulnak el és állnak le.
- **Kvóta:** A kvóta azt szabályozza, hogy a diákok hány órát férhetnek hozzá a virtuális géphez az ütemezett órákon kívül. Ha a kvóta elérése közben egy diák használja, a virtuális gép automatikusan leáll. A tanuló csak akkor tudja újraindítani a virtuális gép újraindítását, ha a kvóta megnő.
- **Automatikus leállítás**: Ha engedélyezve van, az automatikus leállítás idoben a Windows virtuális gépek automatikusan leállnak egy bizonyos idő elteltével, miután egy diák leválasztotta a Távoli asztali protokoll (RDP) munkamenetét. Alapértelmezés szerint ez a beállítás le van tiltva.  

    > [!NOTE]
    > Ez a beállítás jelenleg csak a Windows rendszerben létezik.

### <a name="how-will-students-save-their-work"></a>Hogyan fogják a tanulók megmenteni a munkájukat?

A diákok mindegyike saját virtuális gép, amely hozzá van rendelve a labor élettartama. Választhatnak, hogy:

- Mentse közvetlenül a virtuális gépre.
- Mentés külső helyre, például a OneDrive-ra vagy a GitHubra.

Lehetőség van a OneDrive automatikus konfigurálására a diákok számára a laborban lévő virtuális gépeken.

> [!NOTE]
> Annak érdekében, hogy a tanulók továbbra is hozzáférhessenek a mentett munkájukhoz a laboron kívül, és miután az óra véget ért, azt javasoljuk, hogy a tanulók mentsék a munkájukat egy külső tárházba.

### <a name="how-will-students-connect-to-their-vm"></a>Hogyan fognak a diákok csatlakozni a virtuális gépükhöz?

RdP–Windows virtuális gépek esetén azt javasoljuk, hogy a diákok a [Microsoft Remote Desktop ügyfelet](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)használják. A Távoli asztali ügyfél támogatja a Mac, a Chromebookok és a Windows rendszert.

Linuxos virtuális gépek esetén a diákok ssh-t vagy RDP-t használhatnak. Ahhoz, hogy a diákok rdp használatával csatlakozzanak, telepítenie és konfigurálnia kell a szükséges RDP- és grafikus felhasználói felület-csomagokat.

## <a name="set-up-your-lab"></a>Tesztkörnyezet beállítása

Miután megértette az osztály laborjának követelményeit, készen áll a beállításra. A labor beállításához kövesse az ebben a szakaszban található hivatkozásokat.

1. **Hozzon létre egy labort.** Az utasításokat az [osztályterem-labor létrehozásáról szóló oktatói oktatóanyagban](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) találja.

    > [!NOTE]
    > Ha az osztály egymásba ágyazott virtualizációt igényel, olvassa el a [beágyazott virtualizálás engedélyezésének lépéseit.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Testre szabhatja a lemezképeket, és közzéteheti a tesztkörnyezetben futó virtuális gépeket.** Csatlakozzon egy speciális virtuális gép, a sablon virtuális gép nevű. Lásd az alábbi útmutatók lépéseit:
    - [Sablon létrehozása és kezelése virtuális gép](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Megosztott rendszerkép-katalógus használata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Ha Windows rendszert használ, a [Windows sablon virtuális gépének előkészítésére](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)vonatkozó utasításokat is látnia kell. Ezek az utasítások a OneDrive és az Office beállításának lépéseit tartalmazzák a diákok számára.

1. **Virtuálisgép-készlet és kapacitás kezelése.** Könnyedén skálázhatja fel- vagy le a virtuális gép kapacitását, az osztály által szükséges. Ne feledje, hogy a virtuális gép kapacitásának növelése több órát is igénybe vehet, mert ez magában foglalja az új virtuális gépek beállítását. Tekintse meg a [virtuálisgép-készlet beállításának és kezelésének lépéseit.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)

1. **Laborfelhasználók hozzáadása és kezelése.** Ha felhasználókat szeretne hozzáadni a tesztkörnyezethez, olvassa el az alábbi oktatóanyagok lépéseit:
   - [Felhasználók hozzáadása a tesztkörnyezethez](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Meghívók küldése a felhasználóknak](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    A diákok által használható fiókok típusáról a [Diákfiókok című témakörben](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)talál további információt.
  
1. **Költségvezérlők beállítása.** A tesztkörnyezet költségeinek szabályozásához állítsa be az ütemezéseket, a kvótákat és az automatikus leállítást. Lásd az alábbi oktatóanyagokat:

   - [Ütemezés beállítása](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A telepített operációs rendszer típusától függően a virtuális gép indítása több percet is igénybe vehet. Annak érdekében, hogy egy tesztkörnyezet virtuális gép készen áll az ütemezett órákban, azt javasoljuk, hogy indítsa el a virtuális gépek 30 perccel előre.

   - [Kvóták beállítása a felhasználók számára,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) és [további kvóta beállítása egy adott felhasználóhoz](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Ütemezések, kvóták és automatikus leállítás nem vonatkozik a sablon virtuális gép. Ennek eredményeképpen győződjön meg arról, hogy állítsa le a sablon virtuális gép, ha nem használja. Ellenkező esetben továbbra is költségek merülnek fel. Is, alapértelmezés szerint, amikor létrehoz egy tesztkörnyezet, a sablon virtuális gép automatikusan elindul. Győződjön meg arról, hogy azonnal befejezte a tesztkörnyezet beállítását, és állítsa le a sablon virtuális gép leállítását.

1. **Használja az irányítópultot.** További információt [a labor irányítópultjának használata című témakörben talál.](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard)

    > [!NOTE]
    > Az irányítópulton látható becsült költség a tesztkörnyezet tanulói számára várható maximális költség. Például a diákok *nem* számítanak fel díjat a fel nem használt kvótaórákért. A becsült költségek *nem* tükrözik a sablon virtuális gép vagy a megosztott képtár használatáért felhozott díjakat.

## <a name="next-steps"></a>További lépések

- [Osztályterem-tesztkörnyezet használatának követése](tutorial-track-usage.md)
  
- [Osztályterem-tesztkörnyezet elérése](tutorial-connect-virtual-machine-classroom-lab.md)