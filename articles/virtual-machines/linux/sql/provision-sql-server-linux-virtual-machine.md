---
title: "Linux rendszerű SQL Server 2017-virtuálisgép létrehozása az Azure-ban | Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Linux rendszerű SQL Server 2017-virtuálisgépet az Azure Portalon."
services: virtual-machines-linux
author: rothja
ms.author: jroth
manager: jhubbard
ms.date: 10/25/2017
ms.topic: hero-article
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.technology: database-engine
ms.openlocfilehash: 8d714718e4fc27f0abbfede8fe6d0e2693698ec1
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Linux rendszerű SQL Server-virtuálisgép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Ez a rövid útmutató bemutatja, hogyan hozható létre az Azure Portal segítségével egy Linux rendszerű virtuális gép, amelyen telepítve van az SQL Server 2017.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* [Linux rendszerű SQL-virtuálisgép létrehozása a katalógusból](#create)
* [Csatlakozás az új virtuális géphez ssh használatával](#connect)
* [Az SA-jelszó módosítása](#password)
* [Konfigurálás távoli kapcsolatokhoz](#remote)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a id="create"></a> Linux rendszerű, telepített SQL Serverrel rendelkező virtuális gép létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A bal oldali panelen kattintson az **Új** elemre.

1. Az **Új** panelen kattintson a **Compute** elemre.

1. Kattintson az **Összes megjelenítése** elemre a **Kiemelt** fejléc mellett.

   ![Az összes virtuálisgép-rendszerkép megjelenítése](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. A keresőmezőbe írja be az **SQL Server 2017** kifejezést, és nyomja le az **Enter** billentyűt a keresés indításához.

1. Kattintson a **Szűrés** ikonra, korlátozza a keresést a **Linux-alapú** **Microsoft**-rendszerképekre, majd kattintson a **Kész** elemre.

    ![Keresési szűrő az SQL Server 2017-alapú virtuálisgép-rendszerképekhez](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Válasszon egy Linux rendszerű SQL Server 2017-rendszerképet a keresési eredmények közül. Ebben az oktatóanyagban az **Ingyenes SQL Server-licenc: Red Hat Enterprise Linux 7.4-en futó SQL Server 2017 Developer** nevű rendszerképet használjuk.

   > [!TIP]
   > A Developer kiadás SQL Server-licencköltségek felmerülése nélkül teszi lehetővé az Enterprise kiadás funkcióival végzett tesztelést és fejlesztést. Csak a Linux rendszerű virtuális gép futtatásával járó költségeket kell kifizetnie.

1. Kattintson a **Create** (Létrehozás) gombra.

1. Az **Alapvető beállítások** ablakban adja meg a Linux rendszerű virtuális gép adatait. 

    ![Alapvető beállítások ablak](./media/provision-sql-server-linux-virtual-machine/basics.png)

    > [!Note]
    > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

1. Kattintson az **OK** gombra.

1. A **Méret** ablakban válassza ki a virtuális gép méretét. A további méretek megtekintéséhez válassza az **Összes megtekintése** elemet. A virtuális gépek méretével kapcsolatban további információt a [Linux rendszerű virtuális gépek méreteit](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) ismertető cikkben talál.

    ![Virtuális gép méretének kiválasztása](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Fejlesztési és tesztelési célokra javasoljuk, hogy a virtuális gép mérete legalább **DS2** vagy nagyobb legyen. A teljesítményteszteléshez használjon **DS13** vagy nagyobb méretet.

1. Kattintson a **Kiválasztás** gombra.

1. A **Beállítások** ablakban módosíthatja a beállításokat, vagy meghagyhatja az alapértelmezett beállításokat.

1. Kattintson az **OK** gombra.

1. Az **Összefoglalás** lapon kattintson a **Vásárlás** elemre a virtuális gép létrehozásához.

## <a id="connect"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép IP-címét az Azure Portalon találhatja meg.

![IP-cím az Azure Portalon](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Ha Windows rendszert használ, és nem rendelkezik BASH-parancssorral, telepíthet egy SSH-ügyfelet, például a PuTTY-t.

1. [Töltse le és telepítse a PuTTY-t](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Kattintson az Open (Megnyitás) gombra, és adja meg a felhasználónevét és jelszavát.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal#ssh-to-the-vm) ismertető cikkben talál.

## <a id="password"></a> Az SA-jelszó módosítása

Az új virtuális gép egy véletlenszerű SA-jelszóval telepíti az SQL Servert. Ezt a jelszót alaphelyzetbe kell állítania, mielőtt az SA-bejelentkezéssel csatlakozni tudna az SQL Serverhez.

1. A Linux rendszerű virtuális géphez való csatlakozás után nyisson meg egy új parancsterminált.

1. Módosítsa az SA-jelszót az alábbi parancsokkal:

   ```bash
   sudo systemctl stop mssql-server
   sudo /opt/mssql/bin/mssql-conf set-sa-password
   ```

   Adjon meg egy új SA-jelszót, és erősítse azt meg, amikor a rendszer erre kéri.

1. Indítsa újra az SQL Server szolgáltatást.

   ```bash
   sudo systemctl start mssql-server
   ```

## <a name="add-the-tools-to-your-path-optional"></a>Eszközök hozzáadása az elérési úthoz (nem kötelező)

Alapértelmezés szerint a rendszer több SQL Server-[csomagot](sql-server-linux-virtual-machines-overview.md#packages) is telepít, köztük az SQL Server parancssori eszközcsomagját. Az eszközcsomag tartalmazza az **sqlcmd** és **bcp** eszközt. Kényelmesebb, ha az eszközök elérési útját (`/opt/mssql-tools/bin/`) hozzáadja a **PATH** környezeti változóhoz.

1. A következő parancsok futtatásával módosítsa a **PATH** értékeit a bejelentkezési munkamenetek és az interaktív/nem bejelentkezési munkamenetekre vonatkozóan egyaránt:

   ```bash
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
   echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
   source ~/.bashrc
   ```

## <a id="remote"></a> Konfigurálás távoli kapcsolatokhoz

Ha távolról kell csatlakoznia az Azure-beli virtuális gépen futó SQL Serverhez, ahhoz konfigurálnia kell egy bejövő szabályt a hálózat biztonsági csoportjában. Ez a szabály engedélyezi a forgalmat azon a porton keresztül, amelyet az SQL Server figyel (ez alapértelmezés szerint az 1433-as). A következő lépések bemutatják, hogyan végezheti el ezt a lépést az Azure Portal használatával. 

1. A portálon válassza a **Virtuális gépek** elemet, és válassza ki az SQL Server-t tartalmazó virtuális gépet.

1. A tulajdonságok listájából válassza a **Hálózat** elemet.

1. A **Hálózat** ablakban kattintson a **Hozzáadás** gombra a **Bejövőport-szabályok** alatt.

   ![Bejövőport-szabályok](./media/provision-sql-server-linux-virtual-machine/networking.png)

1. A **Szolgáltatás** listában válassza ki az **MS SQL** lehetőséget.

    ![MS SQL biztonságicsoport-szabálya](./media/provision-sql-server-linux-virtual-machine/sqlnsgrule.png)

1. Kattintson az **OK** gombra a virtuális gép szabályának mentéséhez.

### <a name="open-the-firewall-on-rhel"></a>Tűzfal megnyitása az RHEL-alapú virtuális gépen

Ez az oktatóanyag egy Red Hat Enterprise Linux (RHEL) rendszerű virtuális gép létrehozását ismertette. Ha távolról szeretne csatlakozni egy RHEL rendszerű virtuális géphez, akkor meg kell nyitnia a Linux tűzfalán az 1433-as portot.

1. [Csatlakozzon](#connect) az RHEL rendszerű virtuális géphez.

1. A BASH-parancssorban futtassa a következő parancsokat:

   ```bash
   sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
   sudo firewall-cmd --reload
   ```

## <a name="next-steps"></a>Következő lépések

Most, hogy van egy SQL Server 2017-es virtuális gépe az Azure-ban, helyileg csatlakozhat az **sqlcmd** használatával, és Transact-SQL-lekérdezéseket futtathat.

Ha az Azure-beli virtuális gépet konfigurálta a távoli SQL Server-kapcsolatokhoz, akkor távolról is tudnia kell csatlakozni. A Windows rendszerről egy Linuxon futó SQL Serverhez való csatlakozásra láthat egy példát a [Linuxon futó SQL Serverhez egy Windowson futó SSMS használatával történő csatlakozást](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) ismertető cikkben is. A Visual Studio Code használatával való csatlakozás részleteiről további információt a [Transact-SQL-szkriptek SQL Serverhez a Visual Studio Code használatával történő létrehozását és futtatását](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) ismertető cikkben talál.

További általános információkat a Linuxon futó SQL Serverről [a Linuxon futó SQL Server 2017 áttekintésében](https://docs.microsoft.com/sql/linux/sql-server-linux-overview) talál. További információkat a Linux rendszerű SQL Server 2017-es virtuális gépek használatáról az [SQL Server 2017-es Azure-beli virtuális gépek áttekintésében](sql-server-linux-virtual-machines-overview.md) talál.
