---
title: Telepítse át az adatokat a StorSimple 5000 – 7000 sorozatú eszközt 8000-es sorozat |} A Microsoft Docs
description: Áttekintés és az előfeltételeket a Migrálási szolgáltatás biztosít.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2018
ms.author: alkohli
ms.openlocfilehash: a638487fb0a622f899095989ca5341013299e181
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234408"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Telepítse át az adatokat a StorSimple 5000-7000-es sorozat 8000-es sorozatú eszköz

> [!IMPORTANT]
> - A 2019. július 31-ig. a StorSimple 5000/7000 sorozat megszűnik a támogatás (EOS) állapotát. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozat ügyfelek áttelepítése a a dokumentumban ismertetett alternatív megoldások egyikét.
> - Migrálás jelenleg egy támogatott művelet. Ha azt tervezi, telepítheti át adatait a StorSimple 5000-7000-es sorozatú eszközt 8000-es sorozatú eszközre, kell a Microsoft Support áttelepítési ütemezése. Microsoft Support majd engedélyezi az előfizetés az áttelepítéshez. További információkért lásd: hogyan [hozzon létre egy támogatási jegyet](storsimple-8000-contact-microsoft-support.md).
> - A szolgáltatáskérés fájlban után is igénybe vehet néhány héten belül hajtsa végre a migrálási tervet, és ténylegesen megkezdi az áttelepítést.
> - Forduljon a Microsoft Support, mielőtt lehet arra, hogy áttekintése és teljes körű a [áttelepítési Előfeltételek](#migration-prerequisites) szerepelnek a cikkben.

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja a migrálási szolgáltatás lehetővé teszi a StorSimple 5000-7000-es sorozat migrálhatja az adatokat, hogy a StorSimple 8000 sorozatú fizikai eszköz vagy egy 8010/8020-as felhőalapú berendezés. Ez a cikk is hivatkozik, 5000 – 7000-es sorozat örökölt eszközről egy fizikai 8000-es sorozat, vagy a felhőalapú berendezés az adatok áttelepítéséhez szükséges lépések részletes útmutató letölthető.

Ez a cikk esetében alkalmazható, mind a helyszíni 8000 sorozatú eszköz csakúgy, mint a StorSimple felhőalapú készülék.


## <a name="migration-feature-versus-host-side-migration"></a>Áttelepítési funkcióját és a gazdagép-oldali áttelepítése

Továbbléphet az áttelepítési funkcióját használja az adatok vagy a gazdagép-oldali áttelepítéssel. Ez a szakasz ismerteti, hogy milyen ügyről van az egyes módszerek és a hátrányai többek között. Ezen információk használatával döntse el, melyik módszert kívánja szerezni a áttelepíteni az adatait.

A migrálási szolgáltatás egy vészhelyreállítási (DR) folyamatot a 7000-es/5000-es sorozat a 8000-es sorozat szimulálja. Ez a funkció lehetővé teszi az adatok áttelepítését 5000/7000 sorozat formátumból 8000 series formátum az Azure-ban. Az áttelepítési folyamat a rendszer kezdeményezi a StorSimple-áttelepítési eszköz használata. Az eszköz elindítja a letöltés és a biztonsági mentés metaadatainak átalakítása a 8000-es sorozatú eszköz és a legújabb biztonsági mentés használatával teszi közzé a kötetek az eszközön.

|      | Szakemberek számára                                                                                                                                     |Hátrányok                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Az áttelepítési folyamat megőrzi az 5000/7000 sorozat végrehajtott biztonsági mentések előzményeit.                                               | Felhasználók megpróbálják elérni az adatokat, akkor az áttelepítés az így felmerülő költségek letöltése Azure-ból az adatok letöltéséhez.                                     |
| 2.   | Nincs adat áttelepítése a fogadó oldalon.                                                                                                     | A folyamat a biztonsági mentési és a legutóbbi biztonsági mentés alatt álló illesztett a 8000-es sorozat (becsülhető a migrálási eszközzel) a kezdete közötti állásidő szükséges. |
| 3.   | Ez a folyamat megőrzi az összes szabályzat, sávszélességsablonok, titkosítást és egyéb beállításokat a 8000-es sorozatú eszközökön.                      | Felhasználói hozzáférés csak a felhasználók által elért adatok fog visszaadni, és nem lesz rehidratálási a teljes adatkészletet.                                                  |
| 4.   | Ez a folyamat minden a régebbi biztonsági másolatok az Azure-ban, amely aszinkron módon történik éles befolyásolása nélkül átalakítása hosszabb időt igényel | Áttelepítés csak olyan felhőbeli konfigurációs szinten hajtható végre.  Egyes köteteket felhőalapú konfigurációban külön-külön nem telepíthetők át                       |

A gazdagép-oldali áttelepítés lehetővé teszi a 8000-es sorozat egymástól függetlenül beállítását, és másolja az adatokat az 5000/7000 sorozatú eszközről 8000-es sorozatú eszköz. Ez egyenértékű azzal, mintha az adatok áttelepítése egyik tárolási eszközről egy másikra. Másolja az adatokat különböző eszközök, például a Diskboss, robocopy szolgálnak.

|      | Szakemberek számára                                                                                                                      |Hátrányok                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | A válaszadásra áttelepítése szakaszosan kötet a kötet szerint történik.                                               | Előző biztonsági másolatokat (5000/7000 sorozat) nem lesz elérhető a 8000-es sorozat eszközön.                                                                                                       |
| 2.   | Lehetővé teszi, hogy az adatok összevonása egy tárfiókba az Azure-ban.                                                       | Első biztonsági mentés a felhőbe a 8000-es sorozat minden olyan adat hosszabb időt vesz igénybe, a 8000-es sorozat igényeknek megfelelően kell készíteni az Azure-bA.                                                                     |
| 3.   | A sikeres áttelepítéshez a következő minden az adatok a helyi, a készüléken. Nincsenek nem az késleltetések az adatok elérése közben. | Azure-tárhelyhasználat növeli az 5000 és 7000-es eszköz az adatok törléséig.                                                                                                        |
| 4.   |                                                                                                                           | Ha a 7000-es/5000-es sorozatú eszköz nagy mennyiségű adatot, migrálás során ezeket az adatokat kell le kell tölteni, ami váltása esetében felmerülő költségek és az késleltetések kapcsolatos adatokat tölti le, az Azure-ból azure-ból |

Ez a cikk csak a migrálási szolgáltatás 5000/7000 8000 sorozatú eszköz összpontosít. Gazdagép-oldali áttelepítés további információért látogasson el [egyéb tárolóeszközökből áttelepítési](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Áttelepítési Előfeltételek

Az alábbiakban az örökölt 5000 és 7000 sorozatú eszköz és a 8000-es sorozat a StorSimple eszköz migrálás előfeltételeinek.

> [!IMPORTANT]
> Tekintse át, és az áttelepítési Előfeltételek befejezését, mielőtt a Microsoft Support szolgáltatási kérelem a fájl.

### <a name="for-the-50007000-series-device-source"></a>Az 5000/7000 sorozat eszköz (forrás)

Az áttelepítés megkezdése előtt győződjön meg arról, hogy:

* Rendelkezik az 5000-es és 7000 sorozatú eszköz; forrás az eszköz lehet élő vagy lefelé.

    > [!IMPORTANT]
    > Azt javasoljuk, hogy a soros hozzáféréshez, az eszközön, az áttelepítési folyamat során. Ott kell lennie minden olyan eszközökkel kapcsolatos problémákat, soros hozzáféréshez segítségére lehetnek a hibakeresésben.

* Az 5000 és 7000 sorozatú forrás eszköz fut-e szoftvert verzió v2.1.1.518 vagy újabb. Korábbi verziók nem támogatottak.
* Annak ellenőrzéséhez, hogy az 5000 és 7000 sorozatú fut a verziót, tekintse meg a webes felhasználói felület jobb felső sarkában. A szoftver verziója, amely az eszköz megjelenik. Az áttelepítéshez az 5000 és 7000 sorozatú kell futnia a v2.1.1.518.

    ![Az örökölt eszköz szoftververzió](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Ha az élő eszköz v2.1.1.518 nem fut, vagy később, frissítse a rendszer szükséges minimális verziója. Részletes útmutatásért tekintse meg [a rendszer frissítése a v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Ha v2.1.1.518 futtatja, nyissa meg a webes felhasználói Felületét, hogy vannak-e a rendszerleíró adatbázis fájlvisszaállítási hibák értesítések. Beállításjegyzék visszaállítás sikertelen, ha futtathat a beállításjegyzék-visszaállítást. További információkért lépjen be, hogyan [futtathat beállításjegyzék-visszaállítást](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Ha nem futott v2.1.1.518 lefelé eszközt, egy helyettesítő v2.1.1.518 futtató eszköz feladatátvételt hajt végre. Részletes útmutatásért tekintse meg az 5000/7000 sorozat a StorSimple eszköz DR.
    * Készítsen biztonsági másolatot az eszköz adatainak felhőbeli pillanatkép.
    * Ellenőrizze, hogy bármely más aktív biztonsági mentési feladatok, amelyek a forrás eszközön futnak. Ez magában foglalja a StorSimple Data Protection konzol gazdagépen a feladatokat. Várjon, amíg a jelenlegi feladatok végrehajtásához.


### <a name="for-the-8000-series-physical-device-target"></a>A 8000-es sorozatú fizikai eszköz (cél)

Az áttelepítés megkezdése előtt győződjön meg arról, hogy:

* A cél 8000 sorozatú eszköz a regisztrált és futnak. További információkért lásd: hogyan [a StorSimple Manager szolgáltatással a StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
* A 8000-es sorozatú eszköz a legújabb StorSimple 8000 Series Update 5 telepítve van, és 6.3.9600.17845 vagy újabb verziója fut-e. Ha az eszköz nem rendelkezik a legújabb frissítések, telepítse a legújabb frissítéseket, a folytatás előtt a migrálást szeretné. További információkért lásd: hogyan [a legújabb frissítés telepítése a 8000-es sorozat eszközön](storsimple-8000-install-update-5.md).
* Az Azure-előfizetés engedélyezve van az áttelepítéshez. Ha az előfizetés nincs engedélyezve, forduljon a Microsoft Support az áttelepítéshez az előfizetés engedélyezése érdekében.

### <a name="for-the-80108020-cloud-appliance-target"></a>A 8010/8020-as felhőalapú berendezés (cél)

Az áttelepítés megkezdése előtt ellenőrizze, hogy:

* A cél felhőalapú berendezés a regisztrált és futó. További információkért lásd: hogyan [üzembe helyezése és kezelése a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).
* A felhőalapú berendezés a legújabb StorSimple 8000 Series Update 5 6.3.9600.17845 szoftververzió fut. A felhőalapú berendezés Update 5 nem fut, ha egy új frissítési 5 felhőalapú berendezés létrehozása, mielőtt folytatná a migrálást. További információkért lásd: hogyan [8010/8020-as felhőalapú berendezés létrehozása](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>A StorSimple Migrálási eszközt futtató számítógépen

A StorSimple eszköz olyan Felhasználóifelület-alapú eszköz, amely lehetővé teszi az adatok áttelepíthetők a StorSimple 8000 sorozatú eszközre 5000 – 7000 sorozatú. A StorSimple-áttelepítési eszköz telepítéséhez, amely megfelel a következő számítógépet használni.

A számítógép rendelkezik internetkapcsolattal, és:

* A következő operációs rendszer fut.
    * Windows 10-es.
    * A Windows Server 2012 R2 (vagy újabb) a StorSimple eszköz telepítéséhez.
* A .NET 4.5.2-es verziója telepítve van.
* Legalább 5 GB szabad hely telepítését és használatát az eszköz rendelkezik.

> [!TIP]
> Ha a StorSimple-eszköz egy Windows Server-gazdagép csatlakozik, az áttelepítési eszköz is telepítheti a Windows Server-állomás számítógépen.

#### <a name="to-install-storsimple-migration-tool"></a>A StorSimple eszköz telepítése

A következő lépésekkel a StorSimple eszköz telepítése a számítógépre.

1. Másolja a mappát _StorSimple8000SeriesMigrationTool_ a Windows-számítógépre. Győződjön meg arról, hogy rendelkezik-e elegendő lemezterület a meghajtón, amelyen a szoftver másolódik.

    Nyissa meg az eszköz konfigurációs fájlt _StorSimple8000SeriesMigrationTool.exe.config_ mappában. Ez a kódrészlet a fájl.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Módosítsa a kulcsokhoz tartozó értékeit, és cserélje le:

    * `UserName` – Jelentkezzen be az Azure portal a felhasználó neve.
    * `SubscriptionName and SubscriptionId` – Név és azonosító az Azure-előfizetés. A StorSimple-Eszközkezelő szolgáltatásban kezdőlapjára kerül, a **általános**, kattintson a **tulajdonságok**. Másolja ki az előfizetés nevét, és a szolgáltatáshoz tartozó előfizetés-azonosító.
    * `ResourceName` – Az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás neve. A szolgáltatás tulajdonságok is látható.
    * `ResourceGroup` – Az Azure Portalon a StorSimple-Eszközkezelő szolgáltatással kapcsolatos az erőforráscsoport neve. A szolgáltatás tulajdonságok is látható.
    ![Ellenőrizze a szolgáltatások tulajdonságai az céleszköz](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` – Az azure Active Directory-bérlő azonosítója az Azure Portalon. Jelentkezzen be rendszergazdaként a Microsoft Azure. A Microsoft Azure-portálon kattintson **Azure Active Directory**. A **kezelés**, kattintson a **tulajdonságok**. A bérlő azonosítója megjelenik a **címtár-azonosító** mezőbe.
    ![Az Azure Active Directory bérlői azonosító ellenőrzése](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Mentse a konfigurációs fájlban történt módosításokat.
4.  Futtassa a _StorSimple8000SeriesMigrationTool.exe_ az eszköz elindításához. Amikor a rendszer kéri a hitelesítő adatokat, az Azure Portalon előfizetéséhez tartozó hitelesítő adatok megadása. 
5.  A StorSimple-áttelepítési eszköz felhasználói felületén jelenik meg.
  

## <a name="next-steps"></a>További lépések
Töltse le a részletes útmutatást [telepítse át az adatokat a storsimple 5000-7000-es sorozat a 8000-es sorozatú eszköz](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
