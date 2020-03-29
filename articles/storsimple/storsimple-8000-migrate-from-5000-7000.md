---
title: Adatok áttelepítése a StorSimple 5000-7000 sorozatból a 8000-es sorozatú eszközbe| Microsoft dokumentumok
description: Áttekintést és az Áttelepítés szolgáltatás előfeltételeit tartalmazza.
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
ms.openlocfilehash: 967c03f3c4201bdcf1529fdda93717b6eb74e771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631655"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Adatok áttelepítése a StorSimple 5000-7000 sorozatból a 8000-es sorozatú eszközre

> [!IMPORTANT]
> - 2019. július 31-én a StorSimple 5000/7000 sorozat a támogatási (EOS) státusz megszűnése felé tart. Azt javasoljuk, hogy a StorSimple 5000/7000 sorozatú ügyfelek a dokumentumban ismertetett alternatívák egyikére térjenek át.
> - Az áttelepítés jelenleg egy támogatott művelet. Ha adatokat kíván áttelepíteni a StorSimple 5000-7000 sorozatú eszközről egy 8000-es sorozatú eszközre, az áttelepítést a Microsoft támogatási szolgálatával kell ütemeznie. A Microsoft támogatási szolgálata ezután engedélyezi az előfizetést az áttelepítéshez. További információt a [Támogatási jegy megnyitása című témakörben talál.](storsimple-8000-contact-microsoft-support.md)
> - A szolgáltatáskérelem beküldése után az áttelepítési terv végrehajtása és az áttelepítés tényleges megkezdése néhány hétig is eltarthat.
> - Mielőtt kapcsolatba lépne a Microsoft támogatási szolgálatával, mindenképpen tekintse át és töltse ki a cikkben megjelölt [áttelepítési előfeltételeket.](#migration-prerequisites)

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja az áttelepítési funkciót, amely lehetővé teszi a StorSimple 5000-7000 sorozatú ügyfelek számára, hogy áttelepítsék adataikat a StorSimple 8000 sorozatú fizikai eszközre vagy egy 8010/8020-as felhőalapú készülékre. Ez a cikk egy letölthető, lépésről-lépésre bemutatja az 5000-7000-es sorozatú örökölt eszközről egy 8000-es sorozatú fizikai vagy felhőalapú készülékre való áttelepítéshez szükséges lépéseket.

Ez a cikk a helyszíni 8000-es sorozatú eszközökre és a StorSimple cloud appliance-ra egyaránt vonatkozik.


## <a name="migration-feature-versus-host-side-migration"></a>Áttelepítési funkció és gazdagépoldali áttelepítés

Az adatokat áthelyezheti az áttelepítési funkcióval vagy a gazdagépoldali áttelepítés végrehajtásával. Ez a szakasz az egyes módszerek sajátosságait ismerteti, beleértve az előnyöket és hátrányokat is. Ezen információk alapján megtudhatja, hogy milyen módszert kíván végrehajtani az adatok áttelepítéséhez.

Az áttelepítési funkció egy vész-helyreállítási (DR) folyamatot szimulál 7000/5000 sorozatból 8000-es sorozatba. Ez a funkció lehetővé teszi az adatok áttelepítését 5000/7000 sorozatú formátumból 8000-es sorozatformátumba az Azure-ban. Az áttelepítési folyamat a StorSimple áttelepítési eszközzel indul. Az eszköz elindítja a biztonsági mentés metaadatainak letöltését és átalakítását a 8000-es sorozatú eszközön, majd a legújabb biztonsági mentést használja az eszközön lévő kötetek felfedéséhez.

|      | Előnyök                                                                                                                                     |Hátrányok                                                                                                                                                              |
|------|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Az áttelepítési folyamat megőrzi az 5000/7000 sorozaton készített biztonsági mentések előzményeit.                                               | Amikor a felhasználók megpróbálnak hozzáférni az adatokhoz, ez az áttelepítés letölti az adatokat az Azure-ból, így az adatok letöltési költségeit.                                     |
| 2.   | A gazdagép oldalon nem történik adat áttelepítése.                                                                                                     | A folyamatnak állásidőre van szüksége a biztonsági mentés kezdete és a 8000-es sorozatban felszínre került legutóbbi biztonsági mentés között (az áttelepítési eszközzel megbecsülhető). |
| 3.   | Ez a folyamat megőrzi a 8000-es sorozatú eszközök összes házirendjét, sávszélesség-sablonját, titkosítását és egyéb beállításait.                      | A felhasználói hozzáférés csak a felhasználók által elért adatokat hozza vissza, és nem hidratálja a teljes adatkészletet.                                                  |
| 4.   | Ez a folyamat további időt igényel az Azure összes régebbi biztonsági mentésének konvertálásához, amely aszinkron módon történik, anélkül, hogy befolyásolna az éles környezetet | Az áttelepítés csak felhőalapú konfigurációs szinten végezhető el.  A felhőkonfigurációban lévő egyes kötetek nem telepíthetők át külön-külön                       |

A gazdagépoldali áttelepítés lehetővé teszi a 8000-es sorozat egymástól független beállítását, és az 5000/7000 sorozatú eszközről a 8000-es sorozatú eszközre történő másolást. Ez egyenértékű az adatok egyik tárolóeszközről a másikra történő áttelepítésével. Az adatok másolásához számos eszköz használható, például a Diskboss, a robocopy.

|      | Előnyök                                                                                                                      |Hátrányok                                                                                                                                                                                                      |
|------|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1.   | Az áttelepítés fokozatosan, térfogatonként közelíthető meg.                                               | A korábbi biztonsági mentések (az 5000/7000 sorozaton) nem lesznek elérhetők a 8000-es sorozatú eszközön.                                                                                                       |
| 2.   | Lehetővé teszi az adatok egyetlen tárfiókba történő összevonását az Azure-ban.                                                       | A 8000-es sorozat első felhőbiztonsági mentése hosszabb időt vesz igénybe, mivel a 8000-es sorozat összes adatának biztonsági mentését az Azure-ra kell készíteni.                                                                     |
| 3.   | A sikeres áttelepítést követően az összes adat helyi a készüléken. Nincsenek késések az adatok elérésekor. | Az Azure storage-felhasználás növekedni fog, amíg az adatok törlődnek az 5000/7000 eszközről.                                                                                                        |
| 4.   |                                                                                                                           | Ha a 7000/5000-es sorozatú eszköz nagy mennyiségű adattal rendelkezik, az áttelepítés során ezeket az adatokat le kell tölteni az azure-ból, ami az Azure-ból történő adatok letöltésével kapcsolatos költségeket és késéseket jelent. |

Ez a cikk csak az 5000/7000 és 8000 sorozatú eszköz áttelepítési funkciójára összpontosít. Az állomásoldali áttelepítésről további információt az [Áttelepítés más tárolóeszközökről](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf)című részében talál.

## <a name="migration-prerequisites"></a>Áttelepítési előfeltételek

Íme az örökölt 5000-es vagy 7000-es sorozatú eszköz és a 8000-es sorozatú StorSimple eszköz áttelepítési előfeltételei.

> [!IMPORTANT]
> Mielőtt szolgáltatási kérelmet nyújtana be a Microsoft támogatási szolgálatához, tekintse át és fejezze be az áttelepítési előfeltételeket.

### <a name="for-the-50007000-series-device-source"></a>Az 5000/7000 sorozatú eszközhöz (forrás)

Az áttelepítés megkezdése előtt győződjön meg arról, hogy:

* Az 5000-es vagy 7000-es sorozatú forráseszközzel rendelkezik; a készülék lehet élő vagy le.

    > [!IMPORTANT]
    > Azt javasoljuk, hogy az áttelepítési folyamat során soros hozzáféréssel rendelkezik ehhez az eszközhöz. Ha bármilyen eszközprobléma merül ne fel, a soros hozzáférés segíthet a hibaelhárításban.

* Az 5000-es vagy 7000-es sorozatú forráseszköz 2.1.1.518-as vagy újabb verziójú szoftververziót futtat. A korábbi verziók nem támogatottak.
* Az 5000-es vagy 7000-es sorozat futtatásának ellenőrzéséhez tekintse meg a webes felhasználói felület jobb felső sarkát. Ez megjeleníti az eszköz által futtatott szoftververziót. Áttelepítés esetén az 5000-es vagy 7000-es sorozatnak a 2.1.1.518-as sorozatot kell futtatnia.

    ![Szoftververzió ellenőrzése az örökölt eszközön](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Ha az élő eszköz nem fut a 2.1.1.518-as vagy újabb verzión, frissítse a rendszert a szükséges minimális verzióra. A részletes útmutatásért olvassa el [A rendszer frissítése a 2.1.1.518-as vagy 2.1.518-as fájlra](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518)című információt.
    * Ha a 2.1.1.518-as fájlt futtatja, nyissa meg a webes felhasználói felületet, és nézze meg, hogy vannak-e értesítések a rendszerleíró adatbázis-visszaállítási hibákról. Ha a rendszerleíró adatbázis visszaállítása nem sikerült, futtassa a rendszerleíró adatbázis visszaállítását. További információt a Rendszerleíró [adatbázis visszaállításának futtatása című](http://onlinehelp.storsimple.com/111_Appliance/2_User_Guides/1_Current_(v2.1.1)/1_Web_UI_User_Guide_WIP/2_Configuration/4_Cloud_Accounts/1_Cloud_Credentials#Restoring_Backup_Registry)segédprogramban talál.
    * Ha olyan lefelé irányuló eszközzel rendelkezik, amely nem a 2.1.1.518-as műveletet futtatja, hajtson végre feladatátvételt egy 2.1.1.518-as vagyonfutó csereeszközre. Részletes utasításokat az 5000/7000-es sorozatú StorSimple-eszköz DR-ben talál.
    * Biztonsági másolatot készít az eszköz adatairól egy felhőbeli pillanatkép készítésével.
    * Ellenőrizze, hogy vannak-e más aktív biztonsági mentési feladatok, amelyek a forráseszközön futnak. Ez magában foglalja a storsimple adatvédelmi konzol gazdagép en a feladatokat. Várja meg, amíg az aktuális feladatok befejeződnek.


### <a name="for-the-8000-series-physical-device-target"></a>A 8000-es sorozatú fizikai eszközhöz (cél)

Az áttelepítés megkezdése előtt győződjön meg arról, hogy:

* A cél 8000-es sorozatú eszköz regisztrálva van és fut. További információ: A [StorSimple eszköz telepítése a StorSimple Manager szolgáltatással.](storsimple-8000-deployment-walkthrough-u2.md)
* A 8000-es sorozatú eszközhöz telepítve van a legújabb StorSimple 8000 Series Update 5, és a 6.3.9600.17845 vagy újabb verzió fut. Ha az eszközön nincs telepítve a legújabb frissítések, az áttelepítés folytatása előtt telepítenie kell a legújabb frissítéseket. További információt a [8000-es sorozatú eszköz legújabb frissítésének telepítése című témakörben talál.](storsimple-8000-install-update-5.md)
* Az Azure-előfizetés e-áttelepítés engedélyezve van. Ha az előfizetés nincs engedélyezve, lépjen kapcsolatba a Microsoft támogatási szolgálatával az áttelepítésre vonatkozó előfizetés engedélyezéséhez.

### <a name="for-the-80108020-cloud-appliance-target"></a>A 8010/8020 felhőalapú készülékhez (cél)

Az áttelepítés megkezdése előtt győződjön meg arról, hogy:

* A célfelhő-berendezés regisztrálva van és működik. További információ: A [StorSimple Cloud Appliance telepítése és kezelése.](storsimple-8000-cloud-appliance-u2.md)
* A felhőalapú készüléken a legújabb StorSimple 8000 Series Update 5 szoftver verzió fut 6.3.9600.17845. Ha a felhőalapú készüléken nem fut az 5.frissítés, hozzon létre egy új 5. További információ: [8010/8020 felhőalapú készülék létrehozása.](storsimple-8000-cloud-appliance-u2.md)

### <a name="for-the-computer-running-storsimple-migration-tool"></a>A StorSimple áttelepítési eszközt futtató számítógéphez

A StorSimple Áttelepítési eszköz egy felhasználói felületen alapuló eszköz, amely lehetővé teszi az adatok áttelepítését egy StorSimple 5000-7000 sorozatból egy 8000-es sorozatú eszközre. A StorSimple Áttelepítés eszköz telepítéséhez olyan számítógépet használjon, amely megfelel az alábbi követelményeknek.

A számítógép rendelkezik internetkapcsolattal és:

* A következő operációs rendszert futtatja
    * Windows 10.Windows 10.
    * Windows Server 2012 R2 (vagy újabb) a StorSimple áttelepítési eszköz telepítéséhez.
* A .NET 4.5.2 van telepítve.
* Legalább 5 GB szabad terület teljen el az eszköz telepítéséhez és használatához.

> [!TIP]
> Ha a StorSimple-eszköz Windows Server-állomáshoz csatlakozik, telepítheti az áttelepítési eszközt a Windows Server gazdaszámítógépre.

#### <a name="to-install-storsimple-migration-tool"></a>A StorSimple áttelepítési eszköz telepítése

Hajtsa végre az alábbi lépéseket a StorSimple Áttelepítés iaspita eszköz telepítéséhez a számítógépre.

1. Másolja a _Mappát StorSimple8000SeriesMigrationTool_ a Windows számítógépre. Győződjön meg arról, hogy a szoftver másolási meghajtója elegendő hellyel rendelkezik.

    Nyissa meg a mappában található _StorSimple8000SeriesMigrationTool.exe.config_ eszközkonfigurációs fájlt. Itt van a fájl részlete.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. A kulcsoknak megfelelő értékeket szerkesztheti, és a következőre cserélheti:

    * `UserName`– Felhasználónév az Azure Portalra való bejelentkezéshez.
    * `SubscriptionName and SubscriptionId`– Az Azure-előfizetés neve és azonosítója. A StorSimple Device Manager szolgáltatás céllapján az **Általános**csoportban kattintson a **Tulajdonságok gombra.** Másolja a szolgáltatáshoz társított Előfizetés-név és Előfizetés-azonosító másolását.
    * `ResourceName`– A StorSimple Eszközkezelő szolgáltatás neve az Azure Portalon. A szerviztulajdonságok alatt is látható.
    * `ResourceGroup`– A StorSimple Eszközkezelő szolgáltatáshoz társított erőforráscsoport neve az Azure Portalon. A szerviztulajdonságok alatt is látható.
    ![A céleszköz szolgáltatástulajdonságainak ellenőrzése](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId`– Az Azure Active Directory-bérlői azonosító az Azure Portalon. Jelentkezzen be a Microsoft Azure-ba rendszergazdaként. A Microsoft Azure portalon kattintson az **Azure Active Directory**elemre. A **Kezelés** területen kattintson a **Tulajdonságok** elemre. A bérlőazonosító a **címtárazonosító** mezőben jelenik meg.
    ![Bérlői azonosító ellenőrzése az Azure Active Directoryhoz](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Mentse a konfigurációs fájlon végzett módosításokat.
4.  Az eszköz elindításához futtassa a _StorSimple8000SeriesMigrationTool.exe-t._ Amikor a rendszer hitelesítő adatokat kér, adja meg az előfizetéséhez társított hitelesítő adatokat az Azure Portalon. 
5.  Megjelenik a StorSimple áttelepítési eszköz felhasználói felülete.
  

## <a name="next-steps"></a>További lépések
Töltse le az [adatok StorSimple 5000-7000 sorozatból 8000-es sorozatú eszközre történő áttelepítésének](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b)lépésenkénti útmutatóját.
