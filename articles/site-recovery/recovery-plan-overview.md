---
title: A vészhelyreállítás az Azure Site Recovery helyreállítási tervek használatával |} A Microsoft Docs
description: Ismerje meg a helyreállítási tervek használatával vész-helyreállítási az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 520f30b5fabebf299b5407a502b76d7d30850bfd
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904882"
---
# <a name="about-recovery-plans"></a>A helyreállítási tervek ismertetése

Ez a cikk ismerteti a helyreállítási tervek [Azure Site Recovery](site-recovery-overview.md).

A helyreállítási terv összegyűjti a gépek helyreállítási csoportokba. Testre szabhatja, hogy egy csomag sorrendben, utasításokat és feladatok hozzáadva. Miután egy csomagban van definiálva, feladatátvétel, futtathatja.



## <a name="why-use-a-recovery-plan"></a>Miért érdemes használni egy olyan helyreállítási tervet?

A helyreállítási terv segít egy rendszeres helyreállítási folyamat meghatározása átadhatja a kicsi független egységek létrehozásával. Egy egységet általában egy a környezetében lévő alkalmazás jelöli. A helyreállítási terv határozza meg, hogyan gépek átadja a feladatokat, és a feladatütemezés elindítása a feladatátvételt követően. A helyreállítási tervek használja:

* A modell egy alkalmazás körül annak függőségeit.
* RTO csökkentése érdekében a helyreállítási feladatok automatizálására.
* Győződjön meg arról, hogy Ön még az áttelepítési vagy katasztrófa utáni helyreállítás által készített meggyőződött arról, hogy alkalmazásait egy helyreállítási terv részeként.
* A helyreállítási terv feladatátvételi tesztet futtatni, annak biztosítása érdekében a vész-helyreállítási vagy az áttelepítés a várt módon működik.


## <a name="model-apps"></a>Modell-alkalmazások

Tervezhet és alkalmazás-specifikus tulajdonságok rögzítése helyreállítási csoport létrehozása. Példaként vegyünk egy SQL Servert használó egy tipikus háromrétegű alkalmazás háttér, a közbenső szoftver és a webes előtérrendszer. Általában a helyreállítási terv testreszabása, azt minden egyes rétegben található gépek a megfelelő sorrendben a feladatátvételt követően.

    - Az SQL-háttérrendszer először, a közbenső szoftver el kell indulnia a Tovább, és végül a webes előtérrendszer.
    - Az indítási sorrend biztosítja, hogy működik-e az alkalmazás által az utolsó gép indításakor.
    - Ez a sorrend biztosítja, hogy amikor a közbenső szoftver elindul, és megpróbál csatlakozni az SQL Server réteget, az SQL Server-csomag már fut. 
    - Ebben a sorrendben is segít, győződjön meg arról, hogy az előtér-kiszolgáló utolsó elindul, úgy, hogy a felhasználók nem csatlakoznak az alkalmazás URL-címe, előtt minden be és fut, és az alkalmazás készen áll a kérelmek fogadásához.

Szeretne létrehozni ebben a sorrendben, csoportokat hozzáadni a helyreállítási csoporthoz, és hozzáadni azokat a csoportokat.
- A megadott sorrendben, ahol alkalmazás-előkészítés szolgál. Adott esetben az alkalmazás RTO-helyreállítás javítása érdekében a műveletek párhuzamosan futnak.
- Párhuzamos feladatátvételt egyetlen csoportban lévő gépek.
- Gépek különféle csoportokba átadja a feladatokat csoport sorrendben, hogy a csoport 2 gépek a feladatátvétel indítása, csak az 1. csoport összes gép feladatátvétele és lépések után.

    ![A példában a helyreállítási terv](./media/recovery-plan-overview/rp.png)

A Testreszabás érvényben, az itt ez történik, ha feladatátvételt végez a helyreállítási terv: 

1. A leállítási lépése megkísérli a helyszíni gépek kikapcsolása. A kivétel az, ha a feladatátvételi teszt futtatása, ebben az esetben az elsődleges hely továbbra is fusson. 
2. A leállítási minden olyan számítógép egy párhuzamos feladatátvevő elindítja a helyreállítási tervben szereplő.
3. A feladatátvétel előkészíti a virtuális gépek lemezeit a replikált adatok felhasználásával.
4. Az indítási csoportok sorrendben futnak, és indítsa el a gépeket az egyes csoportokban. Első lépésként 1 csoport fut, majd a csoport 2, és végül csoport 3. Ha egynél több gép található minden olyan csoportot, majd minden gépen párhuzamosan indítsa el.


## <a name="automate-tasks"></a>Feladatok automatizálása

Helyreállítás nagy méretű alkalmazások összetett feladat lehet. Manuális lépések győződjön meg arról, a folyamat gyakran fordul elő hiba, és a feladatátvétel futtatása a személy nem is tud minden alkalmazás-jainak részleteivel kellene. A helyreállítási terv használatával írnak elő a sorrend, és automatizálhatja az egyes lépéseknél szükséges műveleteket a Azure-ban, vagy parancsfájlok feladatátvétel az Azure Automation-runbookok használatával. Nem lehet automatizálni feladatokhoz manuális műveleteket szünet beszúrhat helyreállítási tervekbe. Van néhány különböző típusú feladatok konfigurálhatja:

* **Az Azure virtuális gépen a feladatátvételt követően a feladatok**: Az Azure-ban használt feladatátvétele, amikor általában kell, hogy a virtuális gép a feladatátvételt követően csatlakozhatnak a műveletek végrehajtásához. Példa: 
    * Hozzon létre egy nyilvános IP-címet az Azure virtuális gépen.
    * A hálózati biztonsági csoport hozzárendelése az Azure virtuális gép hálózati adapteréhez.
    * Terheléselosztó hozzáadása egy rendelkezésre állási csoporthoz.
* **A feladatátvételt követően a virtuális Gépen belül feladatok**: Ezeket a feladatokat jellemzően az alkalmazás fut a gépen, konfigurálja újra a, hogy a hiba továbbra is megfelelően működjenek az új környezetben. Példa:
    * Módosítsa az adatbázis-kapcsolati karakterláncot a gépen.
    * A webalkalmazás-kiszolgáló konfigurációját vagy a szabályok módosítása


## <a name="test-failover"></a>Feladatátvétel tesztelése

A helyreállítási terv teszt feladatátvétel indításához használható. Használja az alábbi gyakorlati tanácsokat:

- Mindig egy teljes feladatátvétel futtatása előtt végezze el egy alkalmazást, a feladatátvételi teszt. Feladatátvételi tesztek segítségével ellenőrizze, hogy az alkalmazás betölt a helyreállítási helyre.
- Ha lemaradt korábban valamit, mentése aktiválhat egy tiszta, és futtassa újból a feladatátvételi tesztet. 
- Feladatátvételi teszt futtatása többször, amíg nem biztos benne, hogy az alkalmazás zökkenőmentesen helyreállítja.
- Mivel minden alkalmazáshoz egyedi, az egyes alkalmazások testre szabott helyreállítási terveket hozhat létre, és a feladatátvételi teszt futtatása az egyes szüksége.
- Alkalmazások és azok függőségeit módosítása gyakran. Annak érdekében, hogy a helyreállítási tervek naprakészek legyenek, minden negyedév minden alkalmazáshoz feladatátvételi teszt futtatása.

    ![Képernyőfelvétel egy példa a helyreállítási terv teszt a Site Recoveryben](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Videó megtekintése

Videó gyors példában egy kattintással a feladatátvételt egy kétrétegű WordPress alkalmazás megjelenítése.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>További lépések

- [Hozzon létre](site-recovery-create-recovery-plans.md) a helyreállítási terv.
- Ismerje meg [futó feladatátvételi teszteket](site-recovery-failover.md).  
