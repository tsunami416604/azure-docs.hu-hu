---
title: Az IBM zD&T fejlesztési/tesztelési környezettelepítése az Azure-ra | Microsoft dokumentumok
description: Az IBM Z fejlesztési és tesztelési környezet (zD&T) üzembe helyezése az Azure Virtuálisgép (VM) infrastruktúráján szolgáltatásként (IaaS).
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 04/02/2019
tags: ''
keywords: ''
ms.openlocfilehash: 67af4eae03b773fad9cf38964152c1fb9d623bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025935"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Az IBM zD&T fejlesztési/tesztelési környezet telepítése az Azure-ra

Ha fejlesztői/tesztelési környezetet szeretne létrehozni az IBM Z Systems nagyszámítógépes számítási feladataihoz, telepítheti az IBM Z fejlesztési és tesztelési környezetet (zD&T) az Azure Virtual Machine (VM) infrastruktúráján szolgáltatásként (IaaS).

A zD&T segítségével kihasználhatja az x86 platform költségmegtakarítását a kevésbé kritikus fejlesztési és tesztelési környezetekben, majd visszalökheti a frissítéseket a Z Rendszer éles környezetébe. További információt az [IBM ZD&T telepítési útmutatójában talál.](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)

Az Azure és az Azure Stack a következő verziókat támogatja:

- zD&T Personal Edition
- zD&T párhuzamos Sysplex
- zD&T Enterprise Edition

A zD&T összes kiadása csak x86 Linux rendszereken fut, a Windows Server rendszeren nem. Az Enterprise Edition-t red hat enterprise linux (RHEL) vagy Ubuntu/Debian támogatja. Mind az RHEL, mind a Debian virtuálisgép-lemezképek elérhetők az Azure-ban.

Ez a cikk bemutatja, hogyan állíthatja be a zD&T Enterprise Edition-t az Azure-ban, hogy a zD&T Enterprise Edition webkiszolgálót használhassa a környezetek létrehozásához és kezeléséhez. A zD&T telepítése nem telepít semmilyen környezetet. Ezeket külön kell létrehozni a telepítőcsomagok ban. Az Alkalmazásfejlesztők által vezérelt disztribúciók (ADCD) például tesztkörnyezetek kötetképei. Ezek zip képekben találhatók az IBM által elérhető médiaterjesztésen. Tekintse meg, hogyan [állíthat be ADCD-környezetet az Azure-ban.](demo.md)

További információt az IBM Tudásközpontban található [zD&T áttekintésben](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) talál.

Ez a cikk bemutatja, hogyan állíthatja be a Z fejlesztési és tesztelési környezetet (zD&T) Enterprise Edition az Azure-ban. Ezután a zD&T Enterprise Edition webkiszolgálóval z-alapú környezeteket hozhat létre és kezelhet az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> Az IBM lehetővé teszi a zD&T Enterprise Edition telepítését csak fejlesztési/tesztelési környezetekben, az éles környezetekben*nem.*

- Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- Hozzáférésre van szüksége az adathordozóhoz, amely csak az IBM ügyfelei és partnerei számára érhető el. További információért forduljon az IBM képviselőjéhez, vagy tekintse meg a [zD&T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) weboldalon található elérhetőségeket.

- [Licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez a környezethez való hozzáféréshez szükséges. A létrehozás módja attól függ, hogyan licenceli a szoftvert az IBM-től:

     - **A hardveralapú licencelési kiszolgálóhoz** olyan USB-hardvereszköz szükséges, amely tartalmazza a szoftver minden részének eléréséhez szükséges racionális jogkivonatokat. Ezt az IBM-től kell beszereznie.

     - **A szoftveralapú licencelési kiszolgáló** megköveteli, hogy központi kiszolgálót állítson be a licencelési kulcsok kezeléséhez. Ez a módszer ajánlott, és megköveteli, hogy állítsa be a kulcsokat kapott az IBM a felügyeleti kiszolgálón.

## <a name="create-the-base-image-and-connect"></a>Az alapkép létrehozása és csatlakozás

1. Az Azure Portalon [hozzon létre egy virtuális gép](/azure/virtual-machines/linux/quick-create-portal) a kívánt operációs rendszer konfigurációját. Ez a cikk egy B4ms vM-et feltételez (4 vCPU-val és 16 GB memóriával) Ubuntu 16.04-en.

2. A virtuális gép létrehozása után nyissa meg a 22-es, az FTP-hez és a 9443-as bejövő portokat a webkiszolgálóhoz.

3. A virtuális gép **áttekintése** panelen a **Csatlakozás** gombon keresztül látható SSH-hitelesítő adatokat. Válassza az **SSH** lapot, és másolja az SSH bejelentkezési parancsot a vágólapra.

4. Jelentkezzen be egy [Bash rendszerhéjba](/azure/cloud-shell/quickstart) a helyi számítógépről, és illessze be a parancsot. Ez lesz az űrlap **\<ssh\>felhasználói azonosító\@\<IP-cím\>**. Amikor a rendszer kéri a hitelesítő adatok megadását, adja meg őket, hogy kapcsolatot létesítsen a kezdőkönyvtárral.

## <a name="copy-the-installation-file-to-the-server"></a>A telepítőfájl másolása a kiszolgálóra

A webkiszolgáló telepítőfájlja a **ZDT\_Install\_EE\_V12.0.0.1.tgz**. Ez szerepel az IBM által biztosított adathordozón. Ezt a fájlt fel kell töltenie az Ubuntu virtuális gépre.

1. A parancssorból írja be a következő parancsot, hogy minden naprakész legyen az újonnan létrehozott képen:

    ```
    sudo apt-get update
    ```

2. Hozza létre a telepíteni telepítandó könyvtárat:

    ```
    mkdir ZDT
    ```

3. Másolja a fájlt a helyi gépről a virtuális gépre:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Ez a parancs a telepítőfájlt a kezdőkönyvtár ZDT könyvtárába másolja, amely attól függően változik, hogy az ügyfél Windows vagy Linux operációs rendszert futtat-e.

## <a name="install-the-enterprise-edition"></a>Az Enterprise Edition telepítése

1. Nyissa meg a ZDT könyvtárat,\_és\_bontsa ki a ZDT Telepítése EE\_V12.0.0.1.tgz fájlt a következő parancsokkal:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Futtassa a telepítőt:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Az **1** Enterprise Server telepítéséhez válassza az 1-et.

4. Nyomja **le az Enter billentyűt,** és olvassa el figyelmesen a licencszerződéseket. A licenc végén írja be az **Igen** értéket a folytatáshoz.

5. Amikor a rendszer kéri az újonnan létrehozott felhasználó jelszavának megváltoztatására, **ibmsys1**, használja a **sudo passwd ibmsys1** parancsot, és adja meg az új jelszót.

6. Annak ellenőrzése, hogy a telepítés sikeres volt-e, írja be a

    ```
    dpkg -l | grep zdtapp
    ```

7. Ellenőrizze, hogy a kimenet tartalmazza-e a **zdtapp 12.0.0.0**karakterláncot, jelezve, hogy a csomag gáza sikeresen telepítve van

### <a name="starting-enterprise-edition"></a>Az Enterprise Edition indítása

Ne feledje, hogy amikor a webkiszolgáló elindul, a telepítési folyamat során létrehozott zD&T felhasználói azonosító alatt fut.

1. A webkiszolgáló indításához használja a gyökér felhasználói azonosítót a következő parancs futtatásához:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Másolja az URL-kimenetet a parancsfájlra, amely a következőképpen néz ki:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Illessze be az URL-t egy webböngészőbe, és nyissa meg a zD&T telepítéséhez szükséges felügyeleti összetevőt.

## <a name="next-steps"></a>További lépések

[Alkalmazásfejlesztők által vezérelt disztribúció (ADCD) beállítása IBM&T v1-ben](./demo.md)
