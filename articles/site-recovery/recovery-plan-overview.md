---
title: Helyreállítási tervek használata a vész-helyreállításban Azure Site Recovery
description: Ismerje meg, hogyan használhatja a helyreállítási terveket a vész-helyreállításhoz a Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 8502e08db48700aefe51a6e4f0e79d1b08f6ca79
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814437"
---
# <a name="about-recovery-plans"></a>A helyreállítási tervek ismertetése

Ez a cikk a [Azure site Recovery](site-recovery-overview.md)helyreállítási terveit ismerteti.

A helyreállítási terv helyreállítási csoportokba gyűjti a gépeket. A tervet testreszabhatja rendelés, utasítások és feladatok hozzáadásával. A terv meghatározása után futtathatja a feladatátvételt.  A gépek több helyreállítási tervben is szerepelhetnek, amelyekben a következő tervek kihagyják a gép telepítését/indítását, ha korábban egy másik helyreállítási terv használatával telepítették.


## <a name="why-use-a-recovery-plan"></a>Miért érdemes helyreállítási tervet használni?

A helyreállítási terv segítséget nyújt egy rendszeres helyreállítási folyamat definiálásához, ha olyan kis független egységeket hoz létre, amelyek feladatátvételt hajtanak végre. Egy egység általában egy alkalmazást jelöl a környezetben. A helyreállítási terv meghatározza a gépek feladatátvételét, valamint azt a sorozatot, amelyben a feladatátvétel után indulnak. Helyreállítási tervek használata a következőhöz:

* Alkalmazás modellezése a függőségei köré.
* Automatizálja a helyreállítási feladatokat a RTO csökkentése érdekében.
* Győződjön meg arról, hogy készen áll az áttelepítésre vagy a vész-helyreállításra, mivel az alkalmazások egy helyreállítási terv részét képezik.
* Futtasson feladatátvételi tesztet a helyreállítási terveken, hogy a vész-helyreállítás vagy az áttelepítés a várt módon működjön.


## <a name="model-apps"></a>Modell alkalmazások

Az alkalmazás-specifikus tulajdonságok rögzítéséhez létrehozhat egy helyreállítási csoportot. Tegyük fel például, hogy egy tipikus háromrészes alkalmazást alkalmazunk egy SQL Server-háttérrel, a köztes hálózattal és egy webes előtérben. A helyreállítási tervet általában úgy szabhatja testre, hogy az egyes rétegekbe tartozó gépek a feladatátvétel után megfelelő sorrendben induljon el.

- Az SQL-háttérnek először el kell indulnia, a middleware következő, végül pedig a webes felület.
- Ez az indítási sorrend biztosítja, hogy az alkalmazás az utolsó gép indításakor is működik.
- Ez a sorrend biztosítja, hogy amikor a middleware elindul, és megpróbál csatlakozni a SQL Server szintjéhez, a SQL Server-szintet már futtatja. 
- Ez a megrendelés azt is lehetővé teszi, hogy az előtér-kiszolgáló utoljára induljon el, így a végfelhasználók nem csatlakoznak az alkalmazás URL-címéhez az összes összetevő üzembe helyezése előtt, és az alkalmazás készen áll a kérelmek fogadására.

A rendelés létrehozásához vegyen fel csoportokat a helyreállítási csoportba, és vegyen fel gépeket a csoportokba.
- A sorrend megadásakor a rendszer a sorrendet használja. A műveletek a megfelelő módon futnak párhuzamosan az alkalmazás-helyreállítási RTO javítására.
- Az egyetlen csoportba tartozó gépek párhuzamosan hajtják végre a feladatátvételt.
- A különböző csoportokban lévő gépek a csoportosítási sorrend szerint működnek, így a 2. csoportba tartozó gépek csak azt követően kezdik el a feladatátvételt, miután az 1. csoportban lévő összes gép felvette és elindult.

    ![Példa helyreállítási tervre](./media/recovery-plan-overview/rp.png)

Ennek a testreszabásnak a helyén a következő történik, ha feladatátvételt futtat a helyreállítási tervben: 

1. A leállítási lépés megkísérli kikapcsolni a helyszíni gépeket. A kivétel a feladatátvételi teszt futtatása, amely esetben az elsődleges hely továbbra is fut. 
2. A Leállítás elindítja a helyreállítási tervben szereplő összes gép párhuzamos feladatátvételét.
3. A feladatátvétel replikált adattal készíti elő a virtuális gépek lemezeit.
4. Az indítási csoportok sorrendben futnak, és elindítják a gépeket az egyes csoportokban. Először az 1. csoport fut, majd a 2. csoport, végül pedig a 3. csoport. Ha egy csoportban több gép is van, akkor az összes gép párhuzamosan indul el.


## <a name="automate-tasks"></a>Feladatok automatizálása

A nagyméretű alkalmazások helyreállítása összetett feladat lehet. A manuális lépések végrehajtásával a folyamat hibát észlelt, és előfordulhat, hogy a feladatátvételt futtató személy nem veszi figyelembe az alkalmazás összes bonyolult feltételét. A helyreállítási terv segítségével megadhatja a sorrendet, és automatizálhatja az egyes lépésekhez szükséges műveleteket, Azure Automation runbookok használatával végezheti el a feladatátvételt az Azure-ba vagy a parancsfájlokba. Olyan feladatokhoz, amelyek nem lehetnek automatizáltak, a manuális műveletekre vonatkozó szüneteltetéseket is beillesztheti helyreállítási tervbe. Több különböző típusú feladat is konfigurálható:

* **Feladatok az Azure virtuális gépen feladatátvétel után**: Ha az Azure-ba végez feladatátvételt, általában műveleteket kell végrehajtania, hogy a feladatátvételt követően csatlakozni lehessen a virtuális géphez. Példa: 
    * Hozzon létre egy nyilvános IP-címet az Azure-beli virtuális gépen.
    * Rendeljen egy hálózati biztonsági csoportot az Azure-beli virtuális gép hálózati adapteréhez.
    * Terheléselosztó hozzáadása egy rendelkezésre állási készlethez.
* A **virtuális gépen belüli feladatok feladatátvétel után**: Ezek a feladatok általában a gépen futó alkalmazást konfigurálja újra, így az új környezetben továbbra is megfelelően működik. Példa:
    * Módosítsa az adatbázis-kapcsolatok karakterláncát a gépen belül.
    * Módosítsa a webkiszolgáló konfigurációját vagy szabályait.


## <a name="test-failover"></a>Feladatátvételi teszt

Helyreállítási terv használatával feladatátvételi tesztet indíthat. Használja az alábbi ajánlott eljárásokat:

- Teljes feladatátvétel futtatása előtt mindig végezzen feladatátvételi tesztet egy alkalmazáson. A feladatátvételi teszt segítségével ellenőrizheti, hogy az alkalmazás elérhető-e a helyreállítási helyen.
- Ha úgy találja, hogy kihagyott valamit, aktiválja a tisztítást, majd futtassa újra a feladatátvételi tesztet. 
- Futtasson többszöri feladatátvételi tesztet, amíg nem biztos benne, hogy az alkalmazás zökkenőmentesen működik.
- Mivel az egyes alkalmazások egyediek, létre kell hoznia minden egyes alkalmazáshoz testre szabott helyreállítási terveket, és mindegyiknél futtasson feladatátvételi tesztet.
- Az alkalmazások és a függőségeik gyakran változnak. A helyreállítási tervek naprakészen tartása érdekében futtasson egy feladatátvételi tesztet minden egyes alkalmazáshoz negyedévente.

    ![Képernyőkép egy példa tesztelési helyreállítási tervről Site Recovery](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Videó megtekintése

Tekintse meg a kétrétegű WordPress-alkalmazásra vonatkozó on-Click feladatátvételt bemutató gyors példát tartalmazó videót.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>További lépések

- [Hozzon létre](site-recovery-create-recovery-plans.md) egy helyreállítási tervet.
- További információ a [feladatátvételek futtatásáról](site-recovery-failover.md).  
