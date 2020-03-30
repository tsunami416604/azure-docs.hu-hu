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
ms.openlocfilehash: d58e11551c2857c269e8985e81f84138f6d389ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370813"
---
# <a name="classroom-lab-setup-guide"></a>Tantermi laborbeállítási útmutató

A labor okainak a diákok számára történő közzétételének folyamata akár több órát is igénybe vehet a laborban létrehozandó virtuális gépek (VM-ek) számától függően.  Legalább egy napot kell adnia magának egy labor beállításához, hogy megbizonyosodjon arról, hogy megfelelően működik, és elegendő időt kell biztosítania a diákok virtuális gépének közzétételére.

## <a name="understand-your-classs-lab-requirements"></a>Ismerje meg az osztály laborkövetelményeit

Új labor beállítása előtt vegye figyelembe a következő kérdéseket:

**Milyen szoftverkövetelményekkel rendelkezik az osztály?**

Az osztály tanulási céljai alapján el kell döntenie, hogy mely operációs rendszert, alkalmazásokat, eszközöket stb.   A tesztkörnyezet virtuális gépeinek beállításához három lehetőség közül választhat:

- **Azure Piactér-lemezkép használata** – A Piactér több száz lemezképet biztosít, amelyek labor létrehozásakor használható.  Egyes osztályok, a piactér imázsa már tartalmazhat mindent, amire szüksége van az osztály.

- **Új egyéni lemezkép létrehozása** – Létrehozhatja saját egyéni lemezképét egy piactéri lemezkép használatával kiindulási pontként, és testre szabhatja további szoftverek telepítésével, konfigurációs módosítások at stb.

- **Meglévő egyéni lemezkép használata** – Újra felhasználhatja a korábban létrehozott vagy az iskolában más rendszergazdák vagy oktatók által létrehozott meglévő egyéni lemezképeket; ehhez a rendszergazdáknak konfigurálniuk kell egy megosztott képgalériát, amely az egyéni képek mentésére szolgáló tárház.

> [!NOTE]
> A rendszergazdák felelősek a Piactér és az egyéni lemezképek engedélyezéséért, hogy használhassa őket; egyeztetnie kell az informatikai részleggel annak érdekében, hogy a szükséges képek engedélyezve legyenek.  Egyéni létrehozott képek automatikusan engedélyezve vannak a saját laborokban való használatra.

**Milyen hardverkövetelményekkel rendelkezik az osztály?**

Számos számítási méret közül választhat, amelyek a következőket tartalmazzák:

- Egymásba ágyazott virtualizációs méretek, így hozzáférést biztosíthat a diákoknak egy olyan virtuális géphez, amely több beágyazott virtuális gépet képes üzemeltetni; ezt a számítási méretet például gyakran használják hálózati tanfolyamokhoz.

- A GPU-méretek, hogy a diákok számítógép-igényes alkalmazásokat használhassanak, például a mesterséges intelligenciához és a gépi tanuláshoz.

Tekintse meg a [virtuális gép méretezési](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) útmutatóját az elérhető számítási méretek teljes listájának megtekintéséhez.

> [!NOTE]
> Attól függően, hogy a labor ban a labor jön létre, előfordulhat, hogy kevesebb számítási méret áll rendelkezésre, mivel ez régiónként változik.  Általános javaslatunk, hogy válassza ki a legkisebb számítási méretet, amely a legközelebb áll az Ön igényeinek.  Az Azure Lab Services segítségével szükség esetén szükség esetén szükség esetén egy másik számítási kapacitással rendelkező új labort hozhat létre.

**Milyen függőségekkel rendelkezik az osztály külső Azure- vagy hálózati erőforrásoktól?**

Ha a tesztkörnyezet virtuális gépei külső erőforrásokat, például adatbázist, fájlmegosztást, licenckiszolgálót stb.

A virtuális hálózat által *nem* védett Azure-erőforrásokhoz való hozzáféréshez ezeket az erőforrásokat a nyilvános interneten keresztül érheti el a rendszergazdák további konfigurációja nélkül.

> [!NOTE]
> Fontolja meg, hogy csökkentheti-e a tesztkörnyezet függőségeit a külső erőforrásoktól azáltal, hogy az erőforrást közvetlenül a virtuális gépre biztosítja.  Például, hogy ne kelljen adatokat olvasni egy külső adatbázisból, telepítheti az adatbázist közvetlenül a virtuális gépre.  

**Hogyan ellenőrzik a költségeket?**

A Lab Services használatalapú díjszabási modellt használ, ami azt jelenti, hogy csak a tesztkörnyezet virtuális gépének futtatásáért kell fizetnie.  A költségek szabályozásához három lehetőség áll rendelkezésre, amelyeket általában egymással együtt használnak:

- **Ütemezés** – Az ütemezés lehetővé teszi, hogy automatikusan szabályozhatja, hogy a laborok virtuális gépei mikor indulnak el és állnak le.
- **Kvóta** – A kvóta azt szabályozza, hogy a diákok hány órával férhetnek hozzá a virtuális géphez az ütemezett órákon kívül.  Ha a kvóta elérése közben egy diák használja, a virtuális gép automatikusan leáll, és a diák nem tudja újraindítani a virtuális gép, kivéve, ha a kvóta megnő.
- **Automatikus leállítás** – Ha engedélyezve van, az automatikus leállítási beállítás hatására a Windows virtuális gépek egy bizonyos idő elteltével automatikusan leállnak, miután egy diák leválasztotta az RDP-munkamenetet.  Alapértelmezés szerint ez a beállítás le van tiltva.  

    > [!NOTE]
    > Ez a beállítás jelenleg csak a Windows rendszerben létezik.

**Hogyan fogják a tanulók megmenteni a munkájukat?**

A diákok mindegyike saját virtuális gép, amely hozzá van rendelve a labor élettartama.  Választhatnak, hogy:

- Mentse közvetlenül a virtuális gépre.
- Mentés külső tárházba, például OneDrive-ra, GitHubra stb.

A OneDrive használatához beállíthatja ezt automatikusan a laborgépeiken lévő diákok számára.  Erről további információkat alább talál.

> [!NOTE]
> Annak érdekében, hogy a diákok továbbra is hozzáférhessenek a mentett munkájukhoz a laboron kívül, amely az osztály befejezése után is benne van, javasoljuk, hogy a tanulók mentsék a munkájukat egy külső tárházba.

**Hogyan fognak a diákok csatlakozni a virtuális gépükhöz?**

RdP és Windows virtuális gépek esetén azt javasoljuk a diákoknak, hogy microsoft [remote desktop klienst használjanak.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)  A Távoli asztali ügyfél támogatja a Mac, a Chromebookok és a Windows rendszert.

Linuxos virtuális gépek esetén a diákok ssh-t vagy RDP-t használhatnak.   Az RDP használatával történő csatlakozáshoz ön a felelős a szükséges RDP- és grafikus felhasználói felület-csomagok telepítéséért és konfigurálásáért.  További részletek az alábbiakban.

## <a name="set-up-your-lab"></a>Tesztkörnyezet beállítása

Amint megértette az osztály laborjának követelményeit, készen áll a beállítására.  A labor beállításához kövesse az ebben a szakaszban található hivatkozásokat.

1. **Labor létrehozása**

   Az utasításokat az [osztályterem-labor létrehozásáról szóló oktatói oktatóanyagban](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) találja.

    > [!NOTE]
    > Ha az osztály beágyazott virtualizációt igényel, olvassa el az útmutató útmutató lépéseit, amelyek a [beágyazott virtualizáció engedélyezését mutatják.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm)

1. **Lemezképek testreszabása és tesztkörnyezetben futó virtuális gépek közzététele**

    A lemezképek testreszabásához és a virtuális gépek közzétételéhez a laborban, csatlakozhat egy speciális virtuális gép, a sablon virtuális gép; lásd a következő útmutatókat:
    - [Sablon létrehozása és kezelése virtuális gép](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Megosztott képtár használata](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Ha Windows rendszert használ, olvassa el a Windows sablon [virtuális gépének előkészítésére](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template)vonatkozó útmutató utasításait is.  Ezek az utasítások a OneDrive és az Office beállításának lépéseit tartalmazzák a diákok számára.

1. **Virtuálisgép-készlet és kapacitás kezelése**

   Könnyedén skálázhatja fel- vagy le a virtuális gép kapacitását, ha az osztály a szükséges.  Ne feledje, hogy a virtuális gép kapacitásának növelése több órát is igénybe vehet, mivel ez magában foglalja az új virtuális gépek beállítását.  Tekintse meg az útmutató útmutatóban a [virtuális gépkészlet beállításához és kezeléséhez](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords)szükséges lépéseket.

1. **Tesztkörnyezeti felhasználók létrehozása és felügyelete**

   Ha felhasználókat szeretne hozzáadni a tesztkörnyezethez, olvassa el az alábbi oktatóanyagok lépéseit:
   - [Felhasználók hozzáadása a tesztkörnyezethez](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Meghívók küldése a felhasználóknak](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    A diákok által használható fiókok típusaira vonatkozó információt az alábbi cikkben talál:
    - [Diákfiókok](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts)
  
1. **Költségvezérlők beállítása**

    A tesztkörnyezet költségeinek szabályozásához állítsa be az ütemezéseket, a kvótákat és az automatikus leállítást; lásd az alábbi útmutatók utasításait:

   - [Ütemezés beállítása](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > A telepített operációs rendszer típusától függően a virtuális gép indítása több percet is igénybe vehet.  Annak érdekében, hogy egy tesztkörnyezet virtuális gép készen áll az ütemezett órákban, azt javasoljuk, hogy indítsa el a virtuális gépek 30 perccel előre annak érdekében, hogy a virtuális gépek futnak, és készen áll a használatra.

   - [Kvóták beállítása a felhasználók számára,](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) és [további kvóta beállítása egy adott felhasználóhoz](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quota-for-a-specific-user)
  
   - [Automatikus leállítás engedélyezése a kapcsolat bontásakor](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Ütemezések, kvóták és automatikus leállítás *nem* vonatkoznak a sablon virtuális gép.  Ennek eredményeképpen biztosítania kell, hogy állítsa le a sablon virtuális gép, ha nem használja, különben továbbra is felmerülő költségeket.  Emellett alapértelmezés szerint egy tesztkörnyezet létrehozásakor a sablon virtuális gép automatikusan elindul, ezért győződjön meg arról, hogy azonnal befejezi a tesztkörnyezet beállítását, és leállítja a sablon virtuális gép leállítását.

1. **Irányítópult használata**

    Tekintse meg az útmutató útmutató [a labor irányítópultjának használatával](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard) kapcsolatos utasításokat.

    > [!NOTE]
    > Az irányítópulton látható becsült költségek a tesztkörnyezet tanulói által várt maximális költség.  Például a diákok *nem* számítanak fel díjat a fel nem használt kvótaórákért.  A becsült költségek *nem* tükrözik a sablon virtuális gép vagy a megosztott képtár használatáért felszámított díjakat.

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

- [Osztályterem-tesztkörnyezet használatának követése](tutorial-track-usage.md)
  
- [Osztályterem-tesztkörnyezet elérése](tutorial-connect-virtual-machine-classroom-lab.md)
