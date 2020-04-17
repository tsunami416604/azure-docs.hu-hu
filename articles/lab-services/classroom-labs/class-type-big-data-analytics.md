---
title: Labor beállítása big data-elemzésoktatására az Azure Lab Services használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan állíthat be egy labort a big data-elemzés tanítására a Hortonworks Data Platform (HDP) Docker-telepítésével.
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538777"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Labor beállítása big data-elemzéshez a HortonWorks Data Platform Docker-telepítésével

Ez a cikk bemutatja, hogyan állíthat be egy labort egy big data-elemzési osztály tanításához.  Az ilyen típusú osztály, a diákok megtanulják, hogyan kell kezelni a nagy mennyiségű adatot, és alkalmazza a gépi és statisztikai tanulási algoritmusok levezetni adatelemzések.  A diákok egyik fő célja, hogy megtanulják használni az adatelemzési eszközöket, például az [Apache Hadoop nyílt forráskódú szoftvercsomagját,](https://hadoop.apache.org/) amely eszközöket biztosít a big data tárolásához, kezeléséhez és feldolgozásához.

Ebben a laborban a diákok a [Cloudera](https://www.cloudera.com/)által biztosított Hadoop népszerű kereskedelmi verzióját fogják használni, amelyet [Hortonworks Data Platform (HDP)](https://www.cloudera.com/products/hdp.html)néven használnak.  Pontosabban, a diákok fogják használni [HDP Sandbox 3.0.1,](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) amely egy egyszerűsített, könnyen használható változata a platform, amely ingyenes, és célja a tanulás és a kísérletezés.  Bár ez az osztály használhatja a Windows vagy linuxos virtuális gépek (VM) HDP sandbox telepítve, ez a cikk bemutatja, hogyan kell használni a Windows.

A labor egy másik érdekes aspektusa, hogy a HDP-sandbox üzembe helyezése a laborvirtuális gépek [en Docker-tárolók](https://www.docker.com/) használatával.  Minden Docker-tároló saját elkülönített környezetet biztosít a szoftveralkalmazások belső futtatásához.  Fogalmilag a Docker-tárolók olyanok, mint a beágyazott virtuális gépek, és a [Docker Hubon](https://www.docker.com/products/docker-hub)biztosított tárolórendszerképek alapján számos szoftveralkalmazás egyszerű üzembe helyezésére és futtatására használhatók.  A Cloudera HDP sandbox-telepítési parancsfájlja automatikusan lekéri a [HDP Sandbox 3.0.1 Docker-rendszerképet](https://hub.docker.com/r/hortonworks/sandbox-hdp) a Docker Hubról, és két Docker-tárolót futtat:
  - homokozó-hdp
  - homokozó-proxy

## <a name="lab-configuration"></a>Labor konfigurációja

A tesztkörnyezet beállításához azure-előfizetésre és laborfiókra van szükség a kezdéshez. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené. Miután megkapja az Azure-előfizetést, létrehozhat egy új laborfiókot az Azure Lab Servicesben. Az új tesztkörnyezet-fiók létrehozásáról az [Oktatóanyag a Laborfiók beállítása](tutorial-setup-lab-account.md)című témakörben talál további információt.  Meglévő tesztkörnyezet-fiókot is használhat.

### <a name="lab-account-settings"></a>Laborfiók beállításai

Engedélyezze az alábbi táblázatban leírt beállításokat a tesztkörnyezet-fiókhoz. A marketplace-lemezképek engedélyezéséről a [Piactér-képek megadása a laborkészítők számára elérhető képtárban](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)című témakörben talál további információt.

| Laborfiók beállítása | Utasítások |
| ------------------- | ------------ |
|Piactér képe| Engedélyezze a Windows 10 Pro-lemezképet a laborfiókban való használatra.|

### <a name="lab-settings"></a>Labor beállításai

Az alábbi táblázatbeállításait használhatja az osztálytermi labor beállításakor.  Az osztálytermi labor létrehozásáról további információt az [osztálytermi laboroktatói bemutató beállítása című](tutorial-setup-classroom-lab.md)témakörben talál.

| Labor beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| Közepes (beágyazott virtualizáció). Ez a virtuális gép mérete a legalkalmasabb relációs adatbázisok, a memóriában belüli gyorsítótárazás és az elemzés.  Ez a méret támogatja a beágyazott virtualizációt is.|  
|Virtuális gép képe| Windows 10 Pro|

> [!NOTE] 
> Közepes (beágyazott virtualizáció) használatát kell használnunk, mivel a HDP sandbox Docker használatával történő üzembe helyezése a következőket igényli:
>   - Windows Hyper-V beágyazott virtualizációval
>   - Legalább 10 GB RAM

## <a name="template-machine-configuration"></a>Sablongép konfigurációja

A sablongép beállításához a következőket fogjuk tenni:
- A Docker telepítése
- HDP-sandbox telepítése
- A Docker-tárolók automatikus indítása a PowerShell és a Windows feladatütemező használatával

### <a name="install-docker"></a>A Docker telepítése

Ebben a szakaszban a lépések a [Cloudera Docker-tárolókkal való üzembe helyezésre vonatkozó utasításain](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)alapulnak. 

A Docker-tárolók használatához először telepítenie kell a Docker Desktopot a sablon virtuális gépén:

1. Kövesse a [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)telepítéséhez az [Előfeltételek szakaszlépéseit.](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 

    > [!IMPORTANT] 
    > Győződjön meg arról, hogy a **Windows-tárolók használata linuxos tárolók helyett** beállítás nincs bejelölve.

1. Győződjön meg arról, hogy a **Windows-tárolók és a Hyper-V szolgáltatások** be vannak kapcsolva.
   ![Windows-szolgáltatások be- és kikapcsolása](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Kövesse a Docker memóriakonfigurációjának konfigurálásához a [Windows memóriája](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) szakaszlépéseit.

    > [!WARNING]
    > Ha véletlenül ellenőrzi a **Windows-tárolók használata linuxos tárolók helyett** beállítást a Docker telepítésekor, akkor nem fogja látni a memória konfigurációs beállításait.  A probléma megoldásához váltson linuxos tárolókra [a Windows rendszertálcán a Docker ikonra kattintva](https://docs.docker.com/docker-for-windows/#docker-settings-dialog); amikor megnyílik a Docker Desktop menü, válassza **a Váltás Linux-tárolókra**lehetőséget.
 
### <a name="deploy-hdp-sandbox"></a>HDP-sandbox telepítése

Ebben a szakaszban a HDP sandboxot telepíti, majd a HDP sandboxot is elérheti a böngésző használatával.

1. Győződjön meg arról, hogy telepítette a [Git Bash-t](https://gitforwindows.org/) az útmutató [Előfeltételek szakaszában,](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) mivel ez a következő lépések végrehajtásához ajánlott.

1. A [Cloudera Üzembe helyezési és telepítési útmutatójának használatával](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)hajtsa végre a következő szakaszokban ismertetett lépéseket:
   
   -    HDP-sandbox telepítése
   -    HDP-sandbox ellenőrzése

    > [!WARNING] 
    > Amikor letölti a LEGÚJABB .zip fájlt a HDP-hez, győződjön meg arról, hogy a .zip fájlt *nem* menti szóközt tartalmazó könyvtárelérési úton.

    > [!NOTE] 
    > Ha a telepítés során kivételt kap, amely szerint a **Drive nincs megosztva,** meg kell osztania a C meghajtót a Docker-rel, hogy a HDP Linux-tárolói hozzáférhessenek a helyi Windows-fájlokhoz.  A probléma megoldásához [kattintson a Docker ikonra a Windows tálcában](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) a Docker Desktop menü megnyitásához és a **Beállítások**parancs kiválasztásához.  Amikor megnyílik **a Docker Beállítások** párbeszédpanelje, válassza **az Erőforrások > fájlmegosztás lehetőséget,** és ellenőrizze a **C** meghajtót.  Ezután megismételheti a HDP-sandbox telepítéséhez szükséges lépéseket.

1. Miután a Docker-tárolók HDP sandbox üzembe helyezése és futtatása, elérheti a környezetet a böngésző elindításával, és kövesse Cloudera utasításait a [Sandbox Welcome Page](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) megnyitásához és a HDP-irányítópult elindításához.

    > [!NOTE] 
    > Ezek az utasítások feltételezik, hogy először leképezte a helyi IP-címét a sandbox környezetben a sandbox-hdp.hortonworks.com a sablon virtuális gép gazdafájlban.  Ha **nem** teszi meg ezt a leképezést, a [http://localhost:8080](http://localhost:8080)sandbox üdvözlőlapát a .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>A Docker-tárolók automatikus indítása diákok bejelentkezésekor

A diákok számára könnyen használható élmény biztosítása érdekében olyan PowerShell-parancsfájlt használunk, amely automatikusan:
  - Elindítja a HDP sandbox Docker-tárolók, amikor egy diák elindul, és csatlakozik a labor virtuális gép.
  - Elindítja a böngészőt, és a sandbox üdvözlőoldalára navigál.
A Windows Feladatütemező segítségével is automatikusan futtatjuk ezt a parancsfájlt, amikor egy diák bejelentkezik a virtuális gépükbe.
Ennek beállításához kövesse az alábbi lépéseket: [Big Data Analytics-parancsfájlok](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Költségbecslés

Ha meg szeretné becsülni a labor költségét, a következő példát használhatja.

Egy 25 fős osztály esetében, akik 20 óra ütemezett óra időt és 10 óra kvótát kapnak a házi feladatra vagy a feladatokra, a labor ára a következő lenne:
  - 25 diák * (20 + 10) óra * 55 Labor egység * 0,01 USD óránként = 412,50 USD

További részletek a díjszabásról az [Azure Lab Services díjszabása.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>Összegzés

Ez a cikk végigjárta a szükséges lépéseket egy labor létrehozásához egy big data-elemzési osztály, amely a Hortonworks Data Platform üzembe helyezett Docker használatával.  Az osztálytípus beállítása hasonló adatelemzési osztályokhoz használható.  Ez a beállítás más típusú osztályokra is alkalmazható, amelyek a Docker-t használják a központi telepítéshez.

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)
