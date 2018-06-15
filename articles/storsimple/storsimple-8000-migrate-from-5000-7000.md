---
title: Telepítse át az adatokat a StorSimple 5000-7000-es adatsorozat 8000 sorozat eszköz |} Microsoft Docs
description: Áttekintés és az áttelepítés funkció Előfeltételek biztosít.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/11/2017
ms.author: alkohli
ms.openlocfilehash: 36df62c4b01c623702707d39c6af59f4752ee6e0
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639869"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>StorSimple 5000-7000-es adatsorozat 8000 sorozat eszközre adatainak áttelepítésére

> [!IMPORTANT]
> - Áttelepítés során jelenleg támogatott. Ha szeretne adatokat telepítsen át az 5000-7000-es adatsorozat eszközét 8000 sorozat-eszközök számára, akkor Microsoft Support áttelepítési ütemezése. Microsoft Support majd engedélyezi az előfizetés az áttelepítéshez. További információkért lásd: hogyan [támogatási jegy megnyitása](storsimple-8000-contact-microsoft-support.md).
> - Után fájlt a kérelem, szükség lehet néhány hétre hajtható végre az áttelepítési terv és a ténylegesen megkezdi az áttelepítést.
> - Forduljon a Microsoft Support, mielőtt kell arra, hogy reivew és teljes a [áttelepítési Előfeltételek](#migration-prerequisites) a cikk szerepelnek.

## <a name="overview"></a>Áttekintés

Ez a cikk az áttelepítési funkciója, amely lehetővé teszi, hogy a StorSimple 5000-7000-es adatsorozat ügyfelek áttelepítése a StorSimple 8000 series fizikai eszköz az adatokat, vagy egy 8010-es/8020-as modellt felhő készüléknek be. Ez a cikk is letölthető részletes útmutató lépéseit adatokat telepítsen át egy 5000-7000-es adatsorozat örökölt egy fizikai 8000 Series vagy -eszköz felhő hivatkozásokat tartalmaz.

Ez a cikk esetén mind a helyszíni 8000 sorozat eszköz, valamint a StorSimple felhő készülék alkalmazható.


## <a name="migration-feature-versus-host-side-migration"></a>Áttelepítési szolgáltatás és a gazdagép-oldali áttelepítése

Áthelyezheti az adatok áttelepítési funkcióval vagy a gazdagép-oldali áttelepítéssel. Ez a szakasz ismerteti a részletekről az egyes módszerek, beleértve az előnyei és hátrányai. Ez az információ segítségével mérje fel, melyik módszert szeretné folytatni az adatok áttelepítéséhez.

Az áttelepítési szolgáltatás egy vész-helyreállítási folyamatot a 7000-es/5000 sorozat 8000 Series szimulálja. Ez a funkció lehetővé teszi az adatok áttelepítését 5000/7000-es adatsorozat formátumból 8000 sorozat formátum az Azure-on. Az áttelepítési folyamat lehet kezdeményezni a StorSimple áttelepítési eszköz használatával. Az eszköz a letöltés és a biztonsági mentés metaadatainak átalakítása elindítja a 8000 sorozat eszközön, és a legfrissebb biztonsági másolat használatával teszi közzé a köteteket az eszközön.

|      | Informatikai szakemberek                                                                                                                                     |Hátrányok                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Az áttelepítési folyamat megőrzi a biztonsági másolatok, amelyeket az 5000 vagy 7000-es sorozatokon előzményeit.                                               | Felhasználók megpróbálják elérni az adatokat, az áttelepítés Azure, így az adatok letöltése költségek nélül töltik le az adatokat.                                     |
| 2.   | Adatok áttelepítése a fogadó oldalon.                                                                                                     | A folyamat a biztonsági mentési és a legújabb biztonsági mentés alatt álló illesztett (becsülhető az áttelepítési eszköz használatával) a 8000 sorozat kezdete közötti állásidő kell. |
| 3.   | Ez a folyamat idejére őrzi meg a házirendek, a sávszélesség sablonok, a titkosítás és a többi beállítás 8000 sorozat eszközeire.                      | Felhasználói hozzáférés vissza csak a felhasználók által elért adatokhoz be fogja hozni, és nem lesz rehydrate a teljes adatkészletet.                                                  |
| 4.   | Ez a folyamat minden a régebbi biztonsági másolatok éles befolyásolása nélkül aszinkron módon történik, amely az Azure-ban konvertálható további időre van szükség. | Áttelepítési csak a felhő konfigurációs szintjén történhet.  Egy felhőkonfigurációk található egyes kötetek külön-külön nem telepíthetők át                       |

A gazdagép-oldali áttelepítés lehetővé teszi, hogy 8000 sorozat egymástól függetlenül beállítását, valamint az adatok másolását 5000/7000-es adatsorozat eszköz 8000 sorozat eszközre. Ez egy tárolóeszköz áttelepítése adatait egy másikra. Számos különféle eszközre például Diskboss, robocopy használatával másolja az adatokat.

|      | Informatikai szakemberek                                                                                                                      |Hátrányok                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | A végpont áttelepítése szakaszosan a kötet-kötet alapon.                                               | Korábbi biztonsági másolatokat (5000/7000-es sorozat) nem lesz elérhető az 8000 sorozat eszközön.                                                                                                       |
| 2.   | Lehetővé teszi, hogy az adatok összevonása egy, az Azure storage-fiók be.                                                       | A felhőalapú 8000 Series első biztonsági mentés a Data hosszabb időt vesz igénybe, a 8000 sorozat igényeknek megfelelően az Azure biztonsági mentésre.                                                                     |
| 3.   | A sikeres áttelepítés következő adatai helyi, a készüléken. Nincsenek nem késések fordulnak elő az adatok elérése közben. | Az Azure storage-fogyasztás mindaddig, amíg az adatok törlődnek az 5000 vagy 7000-es eszközön növeli.                                                                                                        |
| 4.   |                                                                                                                           | Ha a 7000-es/5000 sorozat eszköz nagy mennyiségű adatot, az áttelepítés során ezeket az adatokat kell tölthető le: azure, amely költségeket és a kapcsolódó adatok letöltése az Azure-ból késések gyakorisága |

Ez a cikk csak az áttelepítési funkciót 5000/7000-es 8000 sorozat eszköz összpontosít. Gazdagép-oldali áttelepítési további információkért látogasson el [egyéb tárolóeszközre áttelepítés](http://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating Data to StorSimple Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Áttelepítési Előfeltételek

Az alábbiakban az örökölt 5000 vagy 7000 adatsorozat eszköz és a StorSimple 8000 series eszköz áttelepítési előfeltételeit.

> [!IMPORTANT]
> Tekintse át, majd hajtsa végre az áttelepítési Előfeltételek, mielőtt Microsoft Support szolgáltatáskérés fájl.

### <a name="for-the-50007000-series-device-source"></a>Az adatsorozat 5000/7000-es eszköz (forrás)

Áttelepítés megkezdése előtt győződjön meg arról, hogy:

* Az 5000 rendelkezik, vagy a 7000-es sorozathoz forrás eszköz; az eszköz lehet élő vagy le.

    > [!IMPORTANT]
    > Azt javasoljuk, hogy rendelkezik-e az áttelepítési folyamat során az eszköz soros elérésére. Soros hozzáférés segítségével kell eszköz probléma merül fel, a hibaelhárításban.

* Az 5000 vagy 7000 forrás-eszközön fut szoftver verziója v2.1.1.518 vagy újabb. Korábbi verziói nem támogatottak.
* Az a 5000 vagy 7000 adatsorozat futtatott verziójának ellenőrzéséhez tekintse meg a webes felhasználói felület jobb felső sarkában. A szoftververzió, hogy fut-e az eszköz megjelenik. Az áttelepítéshez a 5000 vagy 7000 adatsorozat kell futnia a v2.1.1.518.

    ![Ellenőrizze a szoftver verziója elavult eszköz](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Ha az élő eszköz v2.1.1.518 nem fut, vagy később, akkor frissítse a rendszer szükséges minimális verziója. Részletes útmutatásért tekintse meg [frissítse a rendszert a v2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
    * Ha v2.1.1.518 futtatja, folytassa a webes felhasználói felület, ha van-e a beállításjegyzék-visszaállítási hibák értesítések. Ha a rendszerleíró adatbázis visszaállítása sikertelen volt, beállításjegyzék visszaállítási fut. További információkért lépjen be, hogyan [beállításjegyzék visszaállítási futtatása](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry).
    * Ha még nem futott v2.1.1.518 lefelé eszköztől, a feladatátvételt egy helyettesítő v2.1.1.518 futtató eszközre elvégezheti. Részletes útmutatásért tekintse meg a 5000/7000-es adatsorozat StorSimple eszköz DR.
    * Készítsen biztonsági másolatot a eszközadatok felhő pillanatkép létrehozása van folyamatban.
    * Ellenőrizze, hogy bármely más aktív biztonsági mentési feladatok, amelyek a forrás eszközön futnak. Ez magában foglalja a feladatok a StorSimple Data Protection konzol gazdagépen. Várjon, amíg a jelenlegi feladatok elvégzéséhez.


### <a name="for-the-8000-series-physical-device-target"></a>8000 sorozat fizikai eszköz (cél)

Áttelepítés megkezdése előtt győződjön meg arról, hogy:

* A figyelt 8000 sorozat eszköz regisztrált és futnak. További információkért lásd: hogyan [StorSimple Manager szolgáltatással a StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).
* A 8000 sorozat eszköz a legújabb StorSimple 8000 Series Update 5 telepítve van, és 6.3.9600.17845 vagy újabb verziójú rendszer fut. Ha az eszköz nincs telepítve a legújabb frissítések, telepítse a legújabb frissítéseket, az áttelepítés folytatásához szeretné. További információkért lásd: hogyan [telepítse a legújabb frissítést 8000 sorozat eszközén](storsimple-8000-install-update-5.md).
* Az Azure-előfizetés engedélyezve van az áttelepítéshez. Ha az előfizetés nincs engedélyezve, forduljon a Microsoft Support ahhoz, hogy az előfizetés az áttelepítéshez.

### <a name="for-the-80108020-cloud-appliance-target"></a>A 8010-es/8020-as modellt felhő alapplatformjaként (cél)

Áttelepítés megkezdése előtt győződjön meg:

* A cél felhő készülék valójában a regisztrált és futnak. További információkért lásd: hogyan [központi telepítése és kezelése a StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md).
* A felhő készülék a legújabb StorSimple 8000 Series Update 5 szoftververzió 6.3.9600.17845 fut. Ha a felhő készülék frissítés 5 nem fut, hozzon létre egy új frissítési 5 felhő készülék áttelepítés folytatása előtt. További információkért lásd: hogyan [hozzon létre egy 8010-es/8020-as modellt felhő készülék](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>A StorSimple áttelepítési eszközt futtató számítógépen

StorSimple áttelepítési eszköz egy olyan Felhasználóifelület-alapú eszköz, amely lehetővé teszi, hogy adatokat telepítsen át egy StorSimple 8000 series eszközön való 5000-7000-es adatsorozat. A StorSimple áttelepítési eszköz telepítéséhez kövesse az alábbi követelményeknek megfelelő számítógép.

A számítógép rendelkezik-e internetkapcsolat, és:

* A következő operációs rendszer fut.
    * Windows 10.
    * Windows Server 2012 R2 (vagy magasabb) StorSimple áttelepítési eszköz telepítéséhez.
* Telepítve van a .NET 4.5.2.
* Legalább 5 GB szabad terület telepítése és használata az eszköz rendelkezik.

> [!TIP]
> Ha a StorSimple eszköz egy Windows Server rendszerű gazdagép csatlakozik, az áttelepítési eszköz is telepítheti a Windows Server-állomáson.

#### <a name="to-install-storsimple-migration-tool"></a>A StorSimple eszköz telepítése

A következő lépésekkel StorSimple áttelepítési eszköz telepítése a számítógépre.

1. Másolja a mappát _StorSimple8000SeriesMigrationTool_ a Windows rendszerű számítógépen. Győződjön meg arról, hogy rendelkezik-e elegendő lemezterület a meghajtón, amelyen a szoftver másolódik.

    Nyissa meg az eszköz konfigurációs fájl _StorSimple8000SeriesMigrationTool.exe.config_ a mappában. Ez a fájl részlet.
    
    ```
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. A kulcsok az értékeket szerkesztheti, és cserélje le:

    * `UserName`– Felhasználónév jelentkezzenek be az Azure-portálon.
    * `SubscriptionName and SubscriptionId`– A név és azonosító az Azure-előfizetéséhez. A StorSimple Device Manager szolgáltatás kezdőlapján, a **általános**, kattintson a **tulajdonságok**. Másolja az előfizetés nevét, és a szolgáltatáshoz tartozó előfizetés-azonosító.
    * `ResourceName`– Az Azure-portálon a StorSimple Device Manager szolgáltatás nevét. A szolgáltatás tulajdonságok is megjelennek.
    * `ResourceGroup`– Az Azure-portálon a StorSimple Device Manager szolgáltatáshoz tartozó erőforráscsoport neve. A szolgáltatás tulajdonságok is megjelennek.
    ![Ellenőrizze a szolgáltatás tulajdonságait a céleszköz](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Az azure Active Directory-Bérlőazonosító beszerzése az Azure portálon. Jelentkezzen be a Microsoft Azure rendszergazdaként. A Microsoft Azure portálon kattintson **Azure Active Directory**. A **kezelése**, kattintson a **tulajdonságok**. A bérlő azonosítója szerepel a **könyvtár-azonosítója** mezőbe.
    ![Ellenőrizze a bérlő azonosítója az Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  A konfigurációs fájl módosításainak mentése.
4.  Futtassa a _StorSimple8000SeriesMigrationTool.exe_ az eszköz. Amikor a rendszer kéri a hitelesítő adatokat, adja meg az Azure-portálon Ön előfizetéséhez rendelve. 
5.  A StorSimple áttelepítési eszköz felhasználói felület állapota.
  

## <a name="next-steps"></a>Következő lépések
Töltse le a részletes útmutatást [adatokat telepítsen át a storsimple-kötet 5000-7000-es adatsorozat 8000 sorozat-eszközök számára](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
