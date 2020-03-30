---
title: Helyreállítási tervek az Azure Site Recovery szolgáltatásban
description: Ismerje meg a helyreállítási tervek et az Azure Site Recovery szolgáltatásban.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257770"
---
# <a name="about-recovery-plans"></a>A helyreállítási tervek ismertetése

Ez a cikk áttekintést nyújt az Azure Site Recovery helyreállítási [terveiről.](site-recovery-overview.md)

A helyreállítási terv feladatátvétel céljából helyreállítási csoportokba gyűjti a gépeket. A helyreállítási terv segít meghatározni a rendszeres helyreállítási folyamat, azáltal, hogy kis független egységek, amelyek feladatátvételt. Egy egység általában egy alkalmazást képvisel a környezetben.

- A helyreállítási terv határozza meg, hogyan gépek feladatátvétel, és a sorrend, amelyben elindulnak a feladatátvétel után.
- A helyreállítási tervek feladatátvételhez használatosak az Azure-ba, de nem használhatók az Azure-ból történő feladat-visszavételhez.
- Egy helyreállítási tervhez legfeljebb 100 védett példány adható hozzá.
- A tervet úgy szabhatja testre, hogy sorrendet, utasításokat és feladatokat ad hozzá.
- A terv definiálása után feladatátvételt futtathat rajta.
- A gépekre több helyreállítási tervben is hivatkozhat, amelyben a későbbi tervek kihagyják a gép üzembe helyezését/indítását, ha azt korábban egy másik helyreállítási terv vel telepítették.



### <a name="why-use-a-recovery-plan"></a>Miért érdemes helyreállítási tervet használni?

A helyreállítási tervek használatával:

* Modellezz egy alkalmazást a függőségei köré.
* A helyreállítási feladatok automatizálása a helyreállítási idő (RTO) csökkentése érdekében.
* Ellenőrizze, hogy felkészült-e az áttelepítésre vagy a vészhelyreállításra, és győződjön meg arról, hogy az alkalmazások egy helyreállítási terv részét képezik.
* Futtasson tesztfeladat-átvételeket a helyreállítási terveken, hogy biztosítsa a vészhelyreállítás vagy az áttelepítés várt működését.


## <a name="model-apps"></a>Modellalkalmazások 
Megtervezheti és létrehozhat egy helyreállítási csoportot az alkalmazásspecifikus tulajdonságok rögzítéséhez. Például vegyünk egy tipikus háromrétegű alkalmazás egy SQL-kiszolgáló háttér-, köztes és egy webes előtér. Általában testre szabhatja a helyreállítási tervet, hogy az egyes rétegekben lévő gépek a feladatátvétel után a megfelelő sorrendben indulhassanak.

- Az SQL háttérrendszer kell kezdeni az első, a middleware következő, és végül a webes előtér.
- Ez a kezdési sorrend biztosítja, hogy az alkalmazás az utolsó gép indításakor is működik.
- Ez a sorrend biztosítja, hogy amikor a köztes szoftver elindul, és megpróbál csatlakozni az SQL Server réteghez, az SQL Server szint már fut. 
- Ez a sorrend azt is biztosítja, hogy az előtér-kiszolgáló utolsó indításakor, hogy a végfelhasználók ne csatlakozzanak az alkalmazás URL-címét, mielőtt az összes összetevő fut, és az alkalmazás készen áll a kérelmek fogadására.

A rendelés létrehozásához csoportokat kell hozzáadnia a helyreállítási csoporthoz, és gépeket kell hozzáadnia a csoportokhoz.
- Ha a sorrend meg van adva, szekvenálást kell használni. A műveletek szükség szerint párhuzamosan futnak az alkalmazás-helyreállítási RTO javítása érdekében.
- Az egy csoportban lévő gépek párhuzamosan adják át a feladatátvételt.
- A különböző csoportokba delegált gépek csoportsorrendben, így a Group 2 gépek csak akkor indulnak el a feladatátvétel, ha az 1.

    ![Példa helyreállítási tervre](./media/recovery-plan-overview/rp.png)

Ezzel a testreszabással a következőképpen történik, ha feladatátvételt futtat a helyreállítási terven: 

1. A leállítási lépés megpróbálja kikapcsolni a helyszíni gépeket. A kivétel, ha fut egy teszt feladatátvétel, ebben az esetben az elsődleges hely továbbra is fut. 
2. A leállítás elindítja a helyreállítási tervben lévő összes gép párhuzamos feladatátvételét.
3. A feladatátvétel replikált adatok használatával készíti elő a virtuálisgép-lemezeket.
4. Az indítási csoportok sorrendben futnak, és minden csoportban elindítják a gépeket. Először is, az 1-es csoport fut, aztán a 2-es csoport, végül a 3-as csoport. Ha egynél több gép van egy csoportban, akkor az összes gép párhuzamosan indul.


## <a name="automate-tasks-in-recovery-plans"></a>Feladatok automatizálása helyreállítási tervekben

A nagy alkalmazások helyreállítása összetett feladat lehet. A manuális lépések miatt a folyamat hibát jelezhet, és előfordulhat, hogy a feladatátvételt futtató személy nem ismeri az összes alkalmazásbonyolult állapotot. A helyreállítási terv használatával rendelést szabhat ki, és automatizálhatja az egyes lépésekhez szükséges műveleteket az Azure Automation runbookok használatával az Azure-ba vagy parancsfájlok ba történő feladatátvételhez. A nem automatikusan automatizált feladatok esetében a helyreállítási tervekbe beillesztheti a manuális műveletek szüneteltetését. Többféle feladatot is konfigurálhat:

* **Feladatok az Azure virtuális gép feladatátvétel után: Ha feladatátvétel**után az Azure-ba, általában műveleteket kell végrehajtania, hogy a feladatátvétel után csatlakozhat a virtuális géphez. Példa: 
    * Hozzon létre egy nyilvános IP-címet az Azure virtuális gép.
    * Rendeljen hozzá egy hálózati biztonsági csoportot az Azure virtuális gép hálózati adapteréhez.
    * Terheléselosztó hozzáadása egy rendelkezésre állási csoporthoz.
* **Feladatok a virtuális gépen belül feladatátvétel után:** Ezek a feladatok általában újrakonfigurálja a számítógépen futó alkalmazást, hogy továbbra is megfelelően működjön az új környezetben. Példa:
    * Módosítsa az adatbázis-kapcsolati karakterláncot a készüléken belül.
    * Módosítsa a webkiszolgáló konfigurációját vagy szabályait.


### <a name="run-a-test-failover-on-recovery-plans"></a>Tesztfeladat-átvétel futtatása helyreállítási terveken

A helyreállítási terv segítségével teszt feladatátvételt indíthat el. Használja az alábbi gyakorlati tanácsokat:

- A teljes feladatátvétel futtatása előtt mindig hajtson végre egy tesztfeladat-átvételt egy alkalmazásban. A tesztfeladat-átvételek segítségével ellenőrizheti, hogy az alkalmazás megjelenik-e a helyreállítási helyen.
- Ha úgy találja, hogy kihagyott valamit, indítsa el a tisztítást, majd futtassa újra a teszt feladatátvételt. 
- Futtasson egy teszt feladatátvételt többször, amíg biztos, hogy az alkalmazás zökkenőmentesen helyreáll.
- Mivel minden alkalmazás egyedi, minden egyes alkalmazáshoz testre szabott helyreállítási terveket kell készítenie, és mindegyiken tesztfeladat-átvételt kell futtatnia.
- Az alkalmazások és függőségeik gyakran változnak. Annak érdekében, hogy a helyreállítási tervek naprakészek legyenek, minden negyedévben futtasson egy tesztfeladat-átvételt.

    ![Képernyőkép egy próbateszt-helyreállítási tervről a Site Recovery-ben](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Helyreállítási tervről készült videó megtekintése

Tekintse meg a kétszintű WordPress-alkalmazás helyreállítási tervének kattintásra átvételi feladatátvételt bemutató rövid példavideót.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>További lépések

- [Hozzon létre](site-recovery-create-recovery-plans.md) egy helyreállítási tervet.
- [Feladatátvételek futtatása.](site-recovery-failover.md) 
