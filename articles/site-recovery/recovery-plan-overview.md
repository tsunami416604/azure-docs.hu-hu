---
title: Az Azure Site Recovery helyreállítási tervek segítségével |} Microsoft Docs
description: További tudnivalók az Azure Site Recovery helyreállítási tervek.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: 871c9e8404438f966cab2fc5ab782e254295569e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="about-recovery-plans"></a>A helyreállítási tervek

Ez a cikk ismerteti a helyreállítási terv [Azure Site Recovery](site-recovery-overview.md).

A helyreállítási terv összegyűjti a gépek helyreállítási csoportokba. Testre szabhatja, hogy egy csomag sorrendje, utasításokat és feladatok hozzáadva. Terv van definiálva, miután a feladatátvétel rajta is futtathatja.





## <a name="why-use-a-recovery-plan"></a>A helyreállítási terv miért érdemes használni?

A helyreállítási terv segít egy rendszeres helyreállítási folyamat megadása, amelyek átveheti kis független egység létrehozásával. Egy egység általában jelöl egy alkalmazás adott környezetben. A helyreállítási terv határozza meg, hogyan gépek feladatátvétele, és a sorozat elindítása a feladatátvételt követően. A helyreállítási tervet használja:

* A modell az alkalmazások körül annak függőségeit.
* RTO csökkentése érdekében a helyreállítási feladatok automatizálásához.
- Ellenőrizze, hogy meg van előkészítése az áttelepítési vagy katasztrófa utáni helyreállítás biztosításával, hogy az alkalmazások legyenek-e a helyreállítási terv része.
* A helyreállítási terv feladatátvételi tesztet futtatni, biztosítva vész-helyreállítási vagy áttelepítés a várt módon működik.


## <a name="model-apps"></a>Modell alkalmazások

Tervezze meg, és hozzon létre egy helyreállítási csoportot alkalmazásspecifikus tulajdonságokat rögzítéséhez. Tegyük fel Mérlegeljük, egy SQL Server egy tipikus háromrétegű alkalmazások háttér, köztes és egy webes előtér. Általában testre a helyreállítási terv, hogy a megfelelő sorrendben feladatátvételt követően indítsa el az egyes rétegekbe gépek.

    - Az SQL-háttérrendszer először, a köztes webalkalmazásokba Tovább gombra, és végül a webes előtér.
    - Az indítási sorrend biztosítja, hogy működik-e az alkalmazás által a legutóbbi gép kezd.
    - Ez a sorrend biztosítja, hogy a köztes indításakor, és megpróbál csatlakozni az SQL Server réteget, az SQL Server réteget már fut. 
    - Ez a sorrend is segít, győződjön meg arról, hogy az előtér-kiszolgáló utolsó elindul, úgy, hogy a végfelhasználók számára nem csatlakoznak az alkalmazás URL-CÍMÉT, mielőtt összetevőit a be- és fut, és az alkalmazás készen áll kérések fogadására.

Ez a sorrend létrehozásához adja hozzá a csoportokat a helyreállítási csoporthoz, és hozzáadni a csoportokba. 
    - Rendelés van megadva, ha alkalmazás-előkészítés szolgál. Adott esetben az alkalmazás-helyreállítás RTO javítására műveletek párhuzamosan futnak.
    - Párhuzamos feladatátvételt gépek egy csoportban.
    - A különböző csoporthoz gépek átadja a csoport érdekében, hogy a csoport 2 gépek csak a csoport az 1. a gépek a feladatátvételt és lépések után indítsa el a feladatátvételi.

    ![Példa a helyreállítási terv](./media/recovery-plan-overview/rp.png)

A beállítási helyen, az itt található mi történik, ha a feladatátvételt az a helyreállítási terv: 

1. A leállítási lépés megkísérli kapcsolja ki a helyszíni gépeket. A kivétel: Ha a feladatátvételi teszt futtatása, ebben az esetben az elsődleges hely továbbra is fut. 
2. A leállítási egy párhuzamos feladatátvevő a gépek elindítja a helyreállítási tervben.
3. A feladatátvételi előkészíti a virtuális gépek lemezeit a replikált adatok használatával.
4. Az indítási csoportok sorrendben futnak, és indítsa el a gépeket minden csoportban. Először csoport 1 fut, majd a csoport 2, végül 3 csoport. Bármely csoport egynél több számítógép van, ha a gépek párhuzamos el.


## <a name="automate-tasks"></a>Feladatok automatizálása

Nagy alkalmazások visszaállítása egy összetett feladat lehet. Manuális módszerrel ellenőrizze a folyamat vannak téve a hiba, és a feladatátvételi futtató személy nem is tud összes alkalmazás menő bemutatása. Rendelés ugyanazok a helyreállítási terv segítségével, és automatizálhatja a lépésről lépésre, a műveleteket a feladatátvétel Azure vagy parancsfájlok Azure Automation-forgatókönyv használatával. Nem lehet automatizálni feladatokhoz beilleszthet manuális műveletek szünet a helyreállítási terv. Többféle típusú feladatok konfigurálhatja:

* **Az Azure virtuális gép a feladatátvételt követően a feladatok**: Ha a feladat-visszavétele keresztül az Azure-ba, általában kell műveleteket, így a virtuális gép a feladatátvételt követően csatlakozhatnak. Példa: 
    * A nyilvános IP-cím létrehozása az Azure virtuális gépen.
    * A hálózati biztonsági csoportok hozzárendelése az Azure virtuális gép hálózati adapteréhez.
    * A terheléselosztó hozzáadása egy rendelkezésre állási csoportot.
* **A feladatátvételt követően a virtuális Gépen belül feladatok**: ezek a feladatok általában konfigurálja újra az alkalmazást a számítógépen fut, hogy továbbra is fennáll, az új környezetben megfelelő működéséhez. Példa:
    * Módosítsa az adatbázis-kapcsolati karakterláncot a gépen.
    * A web server konfigurációs vagy a szabályok módosítása.


## <a name="test-failover"></a>A feladatátvételi teszt

A helyreállítási terv feladatátvételi teszt indításához használhatja. Használja az alábbi gyakorlati tanácsokat:

- Mindig végezze el az alkalmazást, a feladatátvételi teszt teljes feladatátvétel futtatása előtt. Feladatátvételi tesztet segítségével ellenőrizze, hogy az alkalmazás megjelenik a helyreállítási helyen.
- Ha már nem talált valamit, indul el, egy tiszta fel, és futtassa a feladatátvételi teszt. 
- Feladatátvételi teszt futtatása többször, amíg nem biztos benne, hogy az alkalmazás zökkenőmentesen helyreállíthatja.
- Mivel minden alkalmazáshoz egyedi, meg kell, amelyeket a minden egyes alkalmazás esetében a helyreállítási terv létrehozása, és minden egyes feladatátvételi teszt futtatása.
- Alkalmazásokat és függőségi viszonyaikat módosulnak. Annak érdekében, hogy a helyreállítási tervek naprakészek legyenek, negyedévente alkalmazásokra vonatkozó feladatátvételi teszt futtatása.

    ![Képernyőfelvétel egy példa a helyreállítási terv teszt a Site Recovery szolgáltatásban](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>Videó megtekintése

Egy kétrétegű WordPress alkalmazás kattintással a feladatátvétel megjelenítő lehetséges videó megtekintése.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>További lépések

- [Hozzon létre](site-recovery-create-recovery-plans.md) helyreállítási tervet.
* További tudnivalók [feladatátvételek futtató](site-recovery-failover.md).  
