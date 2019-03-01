---
title: Állítsa be az alkalmazás fejlesztők szabályozott terjesztési (ADCD) IBM zD & T v1 |} A Microsoft Docs
description: Egy IBM Z fejlesztési és tesztelési környezet (zD & T) környezet Azure-beli virtuális gépeken (VM-eket) futtatni.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: f8af19056a343abdbafcd4ead8b072330cb41fd9
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192072"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Állítsa be az alkalmazás fejlesztők szabályozott terjesztési (ADCD) IBM zD & T v1

Futtathatja egy IBM Z fejlesztési és tesztelési környezet (zD & T) környezet Azure-beli virtuális gépeken (VM). Ez a környezet emulálja az IBM Z sorozat architektúra. Z sorozat operációs rendszerek vagy telepítések (más néven Z példányok vagy csomagok), amelyhez testre szabott csomagok, az IBM alkalmazás fejlesztők szabályozott Disztribúciók (ADCDs) nevű keresztül elérhető különböző üzemeltetéséhez.

Ez a cikk bemutatja, hogyan állítható be egy ADCD-példányt egy zD & T környezetet az Azure-ban. ADCDs fejlesztési és tesztelési környezetek zD & t futtató teljes Z sorozat operációs rendszer megvalósításait létrehozása

ZD & T, például ADCDs csak az IBM-ügyfelek és partnerek számára érhető el, és kizárólag fejlesztési és tesztelési célokra szolgálnak. Azok a nem éles környezetben használható. Számos IBM-telepítési csomagokat is letölthető keresztül [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) vagy [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- A [zD & T környezet] [ ibm-install-z] az Azure-ban korábban már beállított. Ez a cikk azt feltételezi, hogy használja az ugyanazon Ubuntu 16.04 Virtuálisgép-rendszerképet, korábban létrehozott.

- Az IBM PartnerWorld vagy a Passport Advantage ADCD adathordozó való hozzáférést.

- A [licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez a szükséges IBM zD & T. futtatása Létrehozás módja attól függ, hogyan licencet, a szoftver IBM:

  - **Licencelési kiszolgáló hardveres** USB hardvereszköz, amely tartalmazza a szoftver összes részének eléréséhez szükséges ésszerű jogkivonatok igényel. Ez az IBM kell beszereznie.

  - **-Alapú szoftver licencelési kiszolgáló** központi licencelési kulcsok a felügyeleti kiszolgáló beállításához szükséges. Ezt a módszert részesíti előnyben, és megköveteli, hogy a kulcsokat, a felügyeleti kiszolgáló IBM fogadjon beállítása.

## <a name="download-the-installation-packages-from-passport-advantage"></a>A telepítési csomagok letöltését Passport előny

Hozzáférés ADCD adathordozóra szükség. Az alábbi lépések azt feltételezik, az IBM-ügyfelek, és használja ki a Passport használhatja. IBM partnerek használható [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd).

> [!NOTE]
> Ez a cikk azt feltételezi, hogy az Azure portal eléréséhez és letöltéséhez az IBM adathordozó használja-e a Windows rendszerű számítógépek. Ha Mac gépet használ, vagy asztali Ubuntu, a parancsokat és folyamata az IBM adathordozó beszerzése némileg eltérhet.

1. Jelentkezzen be [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Válassza ki **az ügyfélszoftver-letöltési** és **Media Access**.

3. Válassza ki **ajánlat és a szerződés Program**, és kattintson a **Folytatás**.

4. Adja meg a leírása vagy cikkszám, és kattintson a **Finder**.

5. Másik lehetőségként kattinthat a betűrendben lista megjelenítéséhez és megtekintéséhez theproduct nevét.

6. Válassza ki **az összes operációs rendszer** a a **operációs rendszer a mező**, és **összes nyelv** a a **nyelvek mező**. Kattintson a **Go**.

7. Kattintson a **válassza ki az egyes fájlok** bontsa ki a listát, és a különálló adathordozó letöltése megjelenítéséhez.

8. Ellenőrizze a csomag, válassza ki a letölteni kívánt **letöltése**, majd töltse le a fájlokat a kívánt címtárra.

## <a name="upload-the-adcd-packages"></a>Töltse fel a ADCD csomag(ok)

Most, hogy a csomagok, fel kell tölteni azokat a virtuális gép az Azure-ban.

1. Az Azure Portalon kezdeményezni a **ssh** munkamenetet hozott létre, az Ubuntu virtuális Gépen. Nyissa meg a virtuális gépre, válassza ki a **áttekintése** panelen, és válassza ki **Connect**.

2. Válassza ki a **SSH** lapra, és másolja az ssh parancs a vágólapra.

3. Jelentkezzen be a virtuális gépet a hitelesítő adatait, és a [SSH-ügyfél](/azure/virtual-machines/linux/use-remote-desktop) választott. Ez a bemutató a Linux-bővítmények használja a Windows 10-es, ami bash-rendszerhéjból ad hozzá a Windows-parancssort. A puTTY ugyanolyan jól működik.

4. Amikor bejelentkezett, hozzon létre egy könyvtárat, töltse fel az IBM-csomagokat. Ne feledje Linux rendszer kis-és nagybetűket. Például ez a bemutató feltételezi, hogy a csomagokat a rendszer feltölti:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Az SSH-ügyfelet használjon, például fájlok feltöltése[WinSCP](https://winscp.net/eng/index.php). Mivel a szolgáltatáskapcsolódási pont SSH része, használ 22-es portot Ez az SSH használja. Ha a helyi számítógép nem Windows, beírhatja a [scp-parancs](http://man7.org/linux/man-pages/man1/scp.1.html) az SSH-munkamenetben.

6. A létrehozott Azure virtuális gép könyvtárba a feltöltés elindításához, amely válik, hogy a lemezkép tárolási zD & t

    > [!NOTE]
    > Győződjön meg arról, hogy **ADCDTOOLS. XML** való feltöltés szerepel a **home/MyUserID/ZDT/adcd/nov2017** könyvtár. Erre később még szüksége lesz.

7. Várjon, amíg a fájlokat szeretne feltölteni, időt is igénybe vehet néhány függően a kapcsolatot az Azure-bA.

8. Amikor végzett a feltöltések, lépjen abba a könyvtárba, kötetek és az összes kibontása a **gz** kötetek:

    ```
        gunzip \*.gz
    ```
    
![A Fájlkezelőben megjelenítő gz kötetek kibontása](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>A lemezképet tároló konfigurálása

A következő lépés, hogy zD & T, a feltöltött csomag használatára konfigurálja. A storage folyamat belül zD & T csatlakoztatásához és a képek használata teszi lehetővé. SSH- vagy FTP képes használni.

1. Indítsa el a **zDTServer**. Ehhez a legfelső szinten kell lennie. Adja meg az alábbi két parancs sorrendben:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Jegyezze fel az URL-cím kimeneti a parancs, és az URL-címet a webalkalmazás-kiszolgáló eléréséhez használja. A következőhöz hasonló:
     > https://(your VM name or IP address):9443/ZDTMC/index.html
     >
     > Ne feledje, hogy a webes elérés 9443-as portot használja. Ezzel jelentkezzen be a webkiszolgáló. A felhasználói azonosító ZD & T **zdtadmin** és a jelszó **jelszó**.

    ![IBM zD&T Enterprise Edition Welcome screen](media/02-welcome.png)

3. Az a **gyors üzembe helyezés** lap **konfigurálása**válassza **Képtárolás**.

     ![IBM zD&T Enterprise Edition Quick Start screen](media/03-quickstart.png)

4. Az a **képtárolás konfigurálása** lapon jelölje be **SSH File Transfer Protocol**.

5. A **állomásnév**, típus **Localhost** és a könyvtár elérési útját adja meg, a feltöltött képek. Ha például /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Adja meg a **Felhasználóazonosító** és **jelszó** a virtuális gép számára. Ne használja a ZD & T felhasználói Azonosítót és jelszót.

7. Ellenőrizze, hogy rendelkezik hozzáféréssel, majd válassza ki a kapcsolat ellenőrzéséhez **mentése** a konfiguráció mentéséhez.

## <a name="configure-the-target-environments"></a>A célzott adathalászatban konfigurálása

A következő lépés, hogy a zD & T célkörnyezet beállítása. Az emulált üzemeltetési környezetben, ahol egyidejűleg futtatják a lemezképeket.

1. Az a **gyors üzembe helyezés** lap **konfigurálása**válassza **cél környezetekben**.

2. Az a **célkörnyezetekben konfigurálása** lapon jelölje be **cél hozzáadása**.

3. Válassza ki **Linux**. IBM-környezetekben, Linux- és Cloud(OpenStack), két típusát támogatja, de ez a bemutató a Linux rendszeren futó.

4. Az a **Hozzáadás célkörnyezet** lapon a **állomásnév**, adja meg **localhost**. Tartsa **SSH-port** beállítása **22-es**.

5. Az a **Célkörnyezet címke** mezőbe írja be például a címke **MyCICS.**

     ![Cél környezet képernyő hozzáadása](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD konfigurálása és üzembe helyezése

A korábbi konfigurációs lépések végrehajtását követően konfigurálnia kell a zD & T, a csomag és a cél környezet használata. Újra használhatja a storage folyamat zD & T, amely lehetővé teszi, hogy csatlakoztatása és a lemezképeket. SSH- vagy FTP képes használni.

1. Az a **gyors üzembe helyezés** lap **konfigurálása**válassza **ADCD**. Utasítások jelennek meg, a lépéseket, amelyek kell elvégezni, mielőtt egy ADCD csomagot is csatlakoztathatók közben. Ez megmagyarázza, miért azt nevű tároló könyvtárat a korábban végrehajtott módja.

2. Feltéve, hogy minden a képek feltöltése megfelelő könyvtáraihoz, kattintson a **ADCD LEMEZKÉP** hivatkozás jelenik meg a jobb alsó sarokban (7. lépés az alábbi képernyőképen látható).

     ![IBM zD & T Enterprise Edition - ADCD konfigurálása képernyő](media/05-adcd.png)

## <a name="create-the-image"></a>A rendszerkép létrehozása

Ha az előző konfigurációs lépés befejeződött, a **hozzon létre egy rendszerképet ADCD összetevővel** lap jelenik meg.

1. Válassza ki a kötet (november 2017 ebben az esetben) a különböző csomagok, amelyek az adott kötet megjelenítéséhez.

2. Válassza ki a bemutatóhoz **vásárlói adatokat vezérlő rendszer (CICS) - 5.3**.

3. Az a **lemezképnév** mezőbe írja be például a lemezkép nevét **MyCICS lemezkép**.

4. Válassza ki a **kép létrehozása** gombra a képernyő jobb alsó sarkában.

     ![IBM zD & T Enterprise Edition - ADCD összetevők képernyő-lemezkép létrehozása](media/06-adcd.png)

5. A megjelenő ablakban közben a rendszerkép sikeresen rendszerbe lett állítva, válassza a **rendszerképek üzembe helyezése**.

6. Az a **egy rendszerkép üzembe helyezése a célkörnyezet** lapon, válassza ki az előző lapon létrehozott lemezképet (**MyCICS kép**) és a korábban létrehozott célkörnyezetet (**MyCICS**).

7. A következő képernyőn adja meg a hitelesítő adatait a virtuális gép (azaz nem a ztadmin hitelesítő adatok).

8. A Tulajdonságok panelen adja meg az **központi processzor (CPs)**, mennyisége **System memória (GB)**, és a **telepítési könyvtár** a lemezképhez. Mivel ez egy bemutatót, maradjon kicsi.

9. Ellenőrizze, hogy a be van jelölve a **automatikus probléma IPL parancs után z/operációs rendszer központi telepítését**.

     ![Tulajdonságai képernyő](media/07-properties.png)

10. Válassza ki **teljes**.

11. Válassza ki **rendszerkép üzembe helyezése** származó a **egy rendszerkép üzembe helyezése a célkörnyezet** lapot.

A kép most már telepítheti, és készen áll a 3270 terminálemulátorral csatlakoztatni.

> [!NOTE]
> Ha kap hibaüzenetet nincs elég hely a lemezen, vegye figyelembe, hogy a régió igényel 151 Gb.

Gratulálunk! IBM Nagyszámítógépek környezet az Azure-ban most futtatja.

## <a name="learn-more"></a>Részletek

- [Nagyszámítógépek migrálása: tévhitet és tények](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2-höz pureScale az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [hibaelhárítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Az Azure-migrálás nagyszámítógépes nyelvszakértőinkből](https://azure.microsoft.com/en-us/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
