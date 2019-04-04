---
title: IBM zD & T fejlesztési-tesztelési környezet telepítése az Azure-ban |} A Microsoft Docs
description: Szolgáltatás (IaaS) az Azure virtuális gép (VM) infrastruktúra üzembe IBM Z fejlesztési és tesztelési környezetet (zD & T).
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894525"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>IBM zD & T fejlesztési-tesztelési környezet telepítése az Azure-ban

A nagyszámítógépes számítási feladatok esetében fejlesztési-tesztelési környezet létrehozása IBM Z rendszereken, telepíthet IBM Z fejlesztése és tesztelése a környezet (zD & T) az Azure virtuális gép (VM) infrastruktúra szolgáltatás (IaaS).

A zD & T, kihasználhatja a költségmegtakarításokat, az x86 platform a kevésbé fontos fejlesztési-tesztelési környezetet, és leküldéses Z rendszer éles környezetben vissza a frissítéseket. További információkért lásd: a [IBM ZD & T telepítési utasításokat](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL).

Az Azure és az Azure Stack támogatja a következő verziókat:

- zD&T Personal Edition
- zD&T Parallel Sysplex
- zD&T Enterprise Edition

ZD & T minden kiadása csak futtassa x86 Linux rendszerek esetében nem a Windows Server. Enterprise Edition vagy a Red Hat Enterprise Linux (RHEL), vagy az Ubuntu vagy a Debian esetében támogatott. Az Azure RHEL és a Debian virtuális Gépet rendszerképek érhetők el.

Ez a cikk bemutatja, hogyan állítható be zD & T Enterprise Edition, az Azure-ban, így használhatja a zD & T Enterprise Edition webkiszolgáló-környezetek létrehozása és kezelése. Telepítése zD & T minden olyan környezetekben nem telepít. Létre kell hoznia ezeket külön-külön telepítési csomagokat. Például az alkalmazás fejlesztők szabályozott Disztribúciók (ADCD) kötetlemezképet tesztelési környezetek. Az IBM elérhető media terjesztési zip-rendszerképek szerepel. Lásd: hogyan [az Azure-ban ADCD környezet beállításával](demo.md).

További információkért lásd: a [zD & T áttekintése](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) az IBM Tudásbázis központban.

Ez a cikk bemutatja, hogyan állíthat be Z fejlesztési és tesztelési környezet (zD & T) Enterprise Edition, az Azure-ban. Ezután használhatja a zD & T Enterprise Edition webkiszolgáló hozhat létre és kezelheti a Z-alapú környezeteket az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> IBM lehetővé teszi, hogy zD & T Enterprise Edition telepíthetők a csak fejlesztési és tesztelési környezetek –*nem* éles környezetben.

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- Az adathordozó, amely csak az IBM-ügyfelek és partnerek számára érhető el hozzáférésre van szüksége. További információ a IBM képviselőjétől kaphat, vagy tekintse meg a kapcsolattartási adatokat a a [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) webhelyén.

- A [licencelési kiszolgáló](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Ez a környezet eléréséhez szükséges. Létrehozás módja attól függ, hogyan licencet, a szoftver IBM:

     - **Licencelési kiszolgáló hardveres** USB hardvereszköz, amely tartalmazza a szoftver összes részének eléréséhez szükséges ésszerű jogkivonatok igényel. Ez az IBM kell beszereznie.

     - **-Alapú szoftver licencelési kiszolgáló** központi licencelési kulcsok a felügyeleti kiszolgáló beállításához szükséges. Ezt a módszert részesíti előnyben, és megköveteli, hogy a kulcsokat, a felügyeleti kiszolgáló IBM fogadjon beállítása.

## <a name="create-the-base-image-and-connect"></a>Az alap rendszerképet létrehozása és csatlakoztatása

1. Az Azure Portalon [hozzon létre egy virtuális Gépet](/azure/virtual-machines/linux/quick-create-portal) kívánt operációsrendszer-konfigurációval. Ez a cikk feltételezi, hogy B4ms virtuális gép (4 vcpu-k és 16 GB memóriája) Ubuntu 16.04 operációs rendszerű.

2. A virtuális gép létrehozása után nyissa meg a 22-es az SSH-hoz, a 21 az FTP és a 9443-as-kiszolgáló bejövő portokat.

3. Látható, az SSH hitelesítő adatainak lekérése a **áttekintése** a virtuális gép paneljén a **Connect** gombra. Válassza ki a **SSH** lapra, és az SSH-bejelentkezési parancs másolása a vágólapra.

4. Jelentkezzen be egy [Bash rendszerhéjat](/azure/cloud-shell/quickstart) , a helyi Számítógépről, majd illessze be a parancsot. A képernyő lesz **ssh\<felhasználóazonosító\>\@\<IP-cím\>**. Ha a rendszer kéri a hitelesítő adatait, adja meg azokat a kezdőkönyvtára kapcsolatot létesíteni.

## <a name="copy-the-installation-file-to-the-server"></a>Másolja a fájlt a kiszolgálóra

A telepítőfájl a webkiszolgáló **ZDT\_telepítése\_EE\_V12.0.0.1.tgz**. Az IBM által biztosított adathordozó szerepel. Az Ubuntu rendszerű virtuális gép fel kell tölteni a fájlt.

1. A parancssorból adja meg, hogy minden rendben az újonnan létrehozott képen naprakész a következő parancsot:

    ```
    sudo apt-get update
    ```

2. A telepítéséhez a következő könyvtár létrehozásakor:

    ```
    mkdir ZDT
    ```

3. Másolja a fájlt a helyi gépen a virtuális géphez:

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> Ez a parancs a kezdőlap címtárban, attól függően, hogy az ügyfél fut-e a Windows vagy Linux-alapú működésmódja ZDT könyvtárba másolja át a telepítőfájlt.

## <a name="install-the-enterprise-edition"></a>Az Enterprise kiadás telepítése

1. Nyissa meg a ZDT könyvtárat, és a ZDT kibontása\_telepítése\_EE\_V12.0.0.1.tgz fájlt a következő parancsokkal:

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. Futtassa a telepítőt:

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. Válassza ki **1** vállalati kiszolgáló telepítése.

4. Nyomja meg **Enter** és figyelmesen olvassa el a licencszerződést. Adja meg a licenc végén **Igen** a folytatáshoz.

5. Amikor a rendszer kéri az újonnan létrehozott felhasználó jelszavának módosításához **ibmsys1**, a paranccsal **sudo passwd ibmsys1** , és adja meg az új jelszót.

6. Ha a telepítés sikerességének ellenőrzéséhez adja meg

    ```
    dpkg -l | grep zdtapp
    ```

7. Győződjön meg arról, hogy a kimenet tartalmazza a karakterláncot **zdtapp 12.0.0.0**jelzi, hogy a csomag gáz telepítése sikeresen befejeződött

### <a name="starting-enterprise-edition"></a>Enterprise Edition indítása

Ne feledje, hogy a kiszolgáló indításakor, akkor fut, amely a telepítési folyamat során létrehozott zD & T felhasználói azonosító alapján.

1. A webkiszolgáló elindításához futtassa a következő parancsot a legfelső szintű felhasználói azonosító használatával:

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. Másolja az URL-cím kimeneti szerint a parancsfájl, amely a következőhöz hasonló:

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. Illessze be az URL-CÍMÉT egy webböngészőben nyissa meg a felügyeleti összetevő a zD & T telepítés.

## <a name="next-steps"></a>További lépések

[Állítsa be az alkalmazás fejlesztők szabályozott terjesztési (ADCD) IBM zD & T v1](./demo.md)
