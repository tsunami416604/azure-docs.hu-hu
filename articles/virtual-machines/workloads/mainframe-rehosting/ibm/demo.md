---
title: Alkalmazásfejlesztők által vezérelt disztribúció (ADCD) beállítása IBM&T v1-ben | Microsoft dokumentumok
description: IBM Z fejlesztési és tesztelési környezet (zD&T) környezet futtatása Azure virtuális gépeken.Run a IBM Z Development and Test Environment (zD&T) environment on Azure Virtual Machines (VM).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 66f80c79219090c27da37dfc1d9149df5604961f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68841385"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Alkalmazásfejlesztők által vezérelt disztribúció (ADCD) beállítása IBM&T v1-ben

IBM Z fejlesztési és tesztelési környezet (zD&T) környezetet futtathat az Azure virtuális gépeken .You can run an IBM Z Development and Test Environment (zD&T) environment on Azure Virtual Machines (VM). Ez a környezet az IBM Z sorozat architektúráját emulálja. Számos Z sorozatú operációs rendszert vagy telepítést (más néven Z példányokat vagy csomagokat) képes üzemeltetni, amelyek az IBM Application Developers Controlled Distributions (ADCD) nevű testreszabott csomagokon keresztül érhetők el.

Ez a cikk bemutatja, hogyan állíthat be egy ADCD-példányt egy zD&T-környezetben az Azure-ban. Az ADC-k teljes Z sorozatú operációs rendszer implementációkat hoznak létre a zD&T-ben futó fejlesztési és tesztelési környezetekben.

A zD&T-hez hasonlóan az ADCD-k is csak az IBM ügyfelei és partnerei számára érhetők el, és kizárólag fejlesztési és tesztelési célokat szolgálnak. Nem használhatók éles környezetben. Számos IBM telepítőcsomag tölthető le a [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) vagy az [IBM PartnerWorld szolgáltatáson](https://www.ibm.com/partnerworld/public)keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- A [zD&T-környezetben,][ibm-install-z] amelyet korábban beállított az Azure-ban. Ez a cikk feltételezi, hogy ugyanazt az Ubuntu 16.04 virtuális gépkorábban létrehozott lemezképet használja.

- Hozzáférés az ADCD adathordozókhoz az IBM PartnerWorld vagy a Passport Advantage szolgáltatáson keresztül.

- [Licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez az IBM zD&T futtatásához szükséges. A létrehozás módja attól függ, hogyan licenceli a szoftvert az IBM-től:

  - **A hardveralapú licencelési kiszolgálóhoz** olyan USB-hardvereszköz szükséges, amely tartalmazza a szoftver minden részének eléréséhez szükséges racionális jogkivonatokat. Ezt az IBM-től kell beszereznie.

  - **A szoftveralapú licencelési kiszolgáló** megköveteli, hogy központi kiszolgálót állítson be a licencelési kulcsok kezeléséhez. Ez a módszer ajánlott, és megköveteli, hogy állítsa be a kulcsokat kapott az IBM a felügyeleti kiszolgálón.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Töltse le a telepítőcsomagokat a Passport Advantage-ből

Hozzáférés az ADCD adathordozóhoz szükséges. Az alábbi lépések feltételezik, hogy Ön IBM-ügyfél, és használhatja a Passport Advantage-t. Az IBM partnerek használhatják [az IBM PartnerWorld programot.](https://www.ibm.com/partnerworld/public)

> [!NOTE]
> Ez a cikk feltételezi, hogy a Windows PC-t az Azure Portal elérésére és az IBM-adathordozók letöltésére használják. Ha Mac vagy Ubuntu asztali számítógépet használ, az IBM média beszerzésére vonatkozó parancsok és folyamat némileg eltérhetnek.

1. Jelentkezzen be a [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer).

2. Válassza a **Szoftverletöltések** és **médiaelérés lehetőséget.**

3. Válassza a **Programajánlat és -szerződés számát,** majd kattintson a **Folytatás**gombra.

4. Írja be az alkatrész leírását vagy az alkatrészszámot, és kattintson a **Finder**gombra.

5. Ha meg szeretné tetszés szerint, kattintson az ábécésorrendben lévő sorrendre a termék név szerinti megjelenítéséhez és megtekintéséhez.

6. Válassza az Operációs **rendszer mező** **összes operációs rendszere** lehetőséget, és a Nyelvek **mezőben** minden nyelv **lehetőséget.** Ezután kattintson az **Ugrás**gombra.

7. Kattintson **az Egyes fájlok kijelölése** elemre a lista kibontásához és a letöltendő médiafájlok megjelenítéséhez.

8. Ellenőrizze a letölteni kívánt csomag(oka)t, válassza **a Letöltés**lehetőséget, majd töltse le a fájlokat a kívánt könyvtárba.

## <a name="upload-the-adcd-packages"></a>Az ADCD csomag(ok) feltöltése

Most, hogy rendelkezik a csomag(ok), fel kell töltenie őket a virtuális gép az Azure-ban.

1. Az Azure Portalon kezdeményezzen egy **ssh** munkamenetet a létrehozott Ubuntu virtuális géppel. Nyissa meg a virtuális gépét, válassza az **Áttekintő** panelt, és válassza a **Csatlakozás**lehetőséget.

2. Jelölje ki az **SSH** lapot, majd másolja az ssh parancsot a vágólapra.

3. Jelentkezzen be a virtuális gépre a hitelesítő adatok és a választott [SSH-ügyfél](/azure/virtual-machines/linux/use-remote-desktop) használatával. Ez a demó a Windows 10 Linux-bővítményeit használja, amely bash héjat ad a Windows parancssorához. PuTTY szerkezet csak ugyancsak.

4. Ha be van jelentkezve, hozzon létre egy könyvtárat az IBM-csomagok feltöltéséhez. Ne feledje, hogy a Linux a kis- és nagybetűket is érzékenyen tartja. Ez a bemutató például azt feltételezi, hogy a csomagok at feltöltik:

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. Töltse fel a fájlokat egy SSH-ügyfél, például[WinSCP](https://winscp.net/eng/index.php)használatával. Mivel az SCP az SSH része, a 22-es portot használja, amit az SSH használ. Ha a helyi számítógép nem Windows, az SSH-munkamenetbe beírhatja az [scp parancsot.](http://man7.org/linux/man-pages/man1/scp.1.html)

6. Indítsa el a feltöltést a létrehozott Azure virtuálisgép-könyvtárba, amely a zD&T rendszerkép-tárolójává válik.

    > [!NOTE]
    > Győződjön meg arról, hogy **Az ADCDTOOLS. Az XML** szerepel az **otthoni/MyUserID/ZDT/adcd/nov2017** könyvtárba való feltöltésben. Erre később még szüksége lesz.

7. Várja meg, amíg a fájlok feltöltése, amely eltarthat egy ideig az Azure-hoz való csatlakozástól függően.

8. Amikor a feltöltések befejeződtek, keresse meg a kötetek könyvtárát, és bontsa ki az összes **gz** kötetet:

    ```
        gunzip \*.gz
    ```
    
![Kibontott gz-köteteket megjelenítő fájlkezelő](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>A lemezképtároló konfigurálása

A következő lépés a zD&T konfigurálása a feltöltött csomag(ok) használatára. A zD&T-n belüli képtárolási folyamat lehetővé teszi a képek csatlakoztatását és használatát. Használhatja az SSH-t vagy az FTP-t.

1. Indítsa el a **zDTServer**. Ehhez a gyökérszinten kell lennie. Írja be a következő két parancsot sorrendben:
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. Jegyezze fel a parancs URL-kimenetét, és használja ezt az URL-címet a webkiszolgáló eléréséhez. Úgy néz ki, hasonló:
     > https://(a virtuális gép neve vagy IP-címe):9443/ZDTMC/index.html
     >
     > Ne feledje, hogy a webes hozzáférés a 9443-as portot használja. Ezzel jelentkezhet be a webkiszolgálóra. A felhasználói azonosító ZD&T **zdtadmin** és a jelszó **jelszó**.

    ![IBM zD&T Enterprise Edition üdvözlőképernyő](media/02-welcome.png)

3. A **Gyorselső** lap **Konfigurálás**területén válassza a **Képtárolás**lehetőséget.

     ![IBM zD&T Enterprise Edition rövid kezdőképernyő](media/03-quickstart.png)

4. A **Lemezképtárolás konfigurálása** lapon válassza az **SSH Fájlátviteli protokoll lehetőséget.**

5. Az **Állomásnév**mezőbe írja be a **Localhost (Helytár)** mezőbe, és adja meg a képek feltöltési helyének elérési útját. Például /home/MyUserID/ZDT/adcd/nov2017/volumes.

6. Adja meg a virtuális gép **felhasználói azonosítóját** és **jelszavát.** Ne használja a ZD&T felhasználói azonosítót és jelszót.

7. Ellenőrizze, hogy van-e hozzáférése a kapcsolatnak, majd a Konfiguráció mentéséhez válassza a **Mentés** gombot.

## <a name="configure-the-target-environments"></a>A célkörnyezetek konfigurálása

A következő lépés a zD&T célkörnyezet konfigurálása. Ez az emulált üzemeltetett környezet az, ahol a képek futnak.

1. A **Gyorselső** lap **Konfigurálás**csoportjában válassza a **Célkörnyezetek**lehetőséget.

2. A **Célkörnyezetek konfigurálása** lapon válassza **a Cél hozzáadása**lehetőséget.

3. Válassza a **Linux**lehetőséget. Az IBM kétféle környezetet támogat, a Linuxot és a Cloud(OpenStacket), de ez a demó Linuxon fut.

4. A **Célkörnyezet hozzáadása** lap **Host name (Állomásnév)** lapján írja be a **localhost**értéket. Az **SSH portot** állítsa **22-re.**

5. A **Célkörnyezet felirat** mezőbe írjon be egy címkét, például **a MyCICS címet.**

     ![Célkörnyezet hozzáadása képernyő](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Az ADCD konfigurálása és telepítése

Az előző konfigurációs lépések elvégzése után konfigurálnia kell a zD&T-t a csomag(ok) és a célkörnyezet használatára. Ismét használja a kép tárolási folyamat zD&T, amely lehetővé teszi, hogy felmászik, és használja a képeket. Használhatja az SSH-t vagy az FTP-t.

1. A **Gyorskezdés** lap **Konfigurálás**területén válassza az **ADCD**lehetőséget. Megjelenik egy utasításkészlet, amely ismerteti az ADCD-csomagok csatlakoztatása előtt elvégzendő lépéseket. Ez megmagyarázza, hogy miért neveztük el a célkönyvtárat úgy, ahogy korábban tettük.

2. Feltételezve, hogy az összes képet feltöltötték a megfelelő könyvtárakba, kattintson az **ADCD** képre linkre a jobb alsó sarokban (a 7. lépésben látható a következő képernyőképen).

     ![IBM zD&T Enterprise Edition - ADCD képernyő konfigurálása](media/05-adcd.png)

## <a name="create-the-image"></a>A rendszerkép létrehozása

Amikor az előző konfigurációs lépés befejeződött, megjelenik a Kép létrehozása az **ADCD-összetevők használatával** lap.

1. Válassza ki a kötetet (ebben az esetben 2017 nov.) az adott kötetben lévő különböző csomagok megjelenítéséhez.

2. Ehhez a bemutatóhoz válassza **az Ügyfélinformáció-ellenőrző rendszer (CICS) - 5.3**lehetőséget.

3. A **Kép neve** mezőbe írja be a kép nevét, például **MyCICS Image**.

4. Válassza a **Kép létrehozása** gombot a jobb alsó sarokban.

     ![IBM zD&T Enterprise Edition - Kép létrehozása az ADCD Components képernyő használatával](media/06-adcd.png)

5. A megjelenő ablakban, amely ből megmondja, hogy a rendszerkép telepítése sikeresen megtörtént, válassza **a Lemezképek telepítése lehetőséget.**

6. A **Lemezkép telepítése a célkörnyezetbe** lapon jelölje ki az előző oldalon létrehozott lemezképet (**MyCICS Image**) és a korábban létrehozott célkörnyezetet (**MyCICS**).

7. A következő képernyőn adja meg a virtuális gép hitelesítő adatait (azaz nem a ztadmin hitelesítő adatokat).

8. A Tulajdonságok ablaktáblán adja meg a **központi processzorok (CP-k)** számát, a **rendszermemória (GB)** és a futó lemezkép **telepítési könyvtárát.** Mivel ez egy demó, tartsd kicsiben.

9. Győződjön meg arról, hogy a jelölőnégyzet be van jelölve az **IPL parancs automatikus kiadása z/OS-re a telepítés után.**

     ![Tulajdonságok képernyő](media/07-properties.png)

10. Válassza a **Kész**lehetőséget.

11. Válassza **a Lemezkép telepítése** lehetőséget a **Lemezkép központi telepítéséhez a célkörnyezetbe** lapon.

A lemezkép most már üzembe helyezhető, és készen áll egy 3270-es terminálemulátor csatlakoztatására.

> [!NOTE]
> Ha hibaüzenet jelenik meg, amely szerint nincs elég lemezterület, vegye figyelembe, hogy a régió151 Gb-ot igényel.

Gratulálunk! Most ibm nagyszámítógépes környezetet futtat az Azure-on.

## <a name="learn-more"></a>Részletek

- [Mainframe migráció: mítoszok és tények](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [hibaelhárítással](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Nagyszámítógép demystifying az Azure-migráláshoz](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
