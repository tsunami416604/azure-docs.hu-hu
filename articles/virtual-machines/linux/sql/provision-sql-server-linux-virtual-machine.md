---
title: Linux rendszerű SQL Server 2017-virtuálisgép létrehozása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Linux rendszerű SQL Server 2017-virtuálisgépet az Azure Portalon.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 10/22/2019
tags: azure-service-management
ms.topic: conceptual
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ba4eed4dcfd6d8e86c21f1ee5214108c44a8c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060227"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Linux rendszerű SQL Server-virtuálisgép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Ebben a rövid útmutatóban az Azure Portal on hozzon létre egy Linux virtuális gépet az SQL Server 2017 telepítve.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* [Linux rendszerű SQL-virtuálisgép létrehozása a katalógusból](#create)
* [Csatlakozás az új virtuális géphez ssh használatával](#connect)
* [A sa jelszó módosítása](#password)
* [Konfigurálás távoli kapcsolatokhoz](#remote)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free) mielőtt elkezdené.

## <a name="create-a-linux-vm-with-sql-server-installed"></a><a id="create"></a> Linux rendszerű, telepített SQL Serverrel rendelkező virtuális gép létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet.

1. Az **Erőforrás létrehozása** ablaktáblán válassza **a Számítás**lehetőséget.

1. Válassza a **Kiemelt** címsor melletti **Mind** megtekintése lehetőséget.

   ![Az összes virtuálisgép-rendszerkép megjelenítése](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. A keresőmezőbe írja be az **SQL Server 2019**parancsot, és a keresés elindításához válassza az **Enter** lehetőséget.

1. Korlátozza a keresési eredmények kiválasztásával **operációs rendszer** > **Redhat**.

    ![Sql Server 2019 virtuálisgép-lemezképek keresési szűrője](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Válasszon ki egy SQL Server 2019 Linux-lemezképet a keresési eredmények közül. Ez az oktatóanyag **az SQL Server 2019-et használja az RHEL74 rendszeren.**

   > [!TIP]
   > A Fejlesztői kiadás lehetővé teszi az Enterprise edition szolgáltatásainak tesztelését vagy fejlesztését, de az SQL Server licencelési költségei nélkül. Csak a Linux rendszerű virtuális gép futtatásával járó költségeket kell kifizetnie.

1. Kattintson a **Létrehozás** gombra. 


### <a name="set-up-your-linux-vm"></a>A Linux virtuális gép beállítása

1. Az **Alapok** lapon válassza ki az **Előfizetés** és **erőforráscsoport lehetőséget.** 

    ![Alapvető beállítások ablak](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. A **virtuális gép neve**mezőbe írja be az új Linux virtuális gép nevét.
1. Ezután írja be vagy válassza ki a következő értékeket:
   * **Régió**: Válassza ki az Önnek megfelelő Azure-régiót.
   * **Elérhetőségi lehetőségek:** Válassza ki az alkalmazásoknak és adatoknak leginkább megfelelő elérhetőségi és redundanciai beállítást.
   * **Méret módosítása**: Válassza ezt a lehetőséget a gépméretének kiválasztásához, és ha elkészült, válassza **a Kijelölés lehetőséget.** A virtuális gépek méretével kapcsolatban további információt a [Linux rendszerű virtuális gépek méreteit](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) ismertető cikkben talál.

     ![Virtuális gép méretének kiválasztása](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > A fejlesztés és a funkcionális tesztelés, használja a virtuális gép mérete **DS2** vagy magasabb. A teljesítményteszteléshez használjon **DS13** vagy nagyobb méretet.

   * **Hitelesítés típusa**: Válassza az **SSH nyilvános kulcsot.**

     > [!Note]
     > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Felhasználónév**: Adja meg a virtuális gép rendszergazdai nevét.
   * **SSH nyilvános kulcs:** Adja meg az RSA nyilvános kulcsát.
   * **Nyilvános bejövő portok:** Válassza a **Kijelölt portok engedélyezése lehetőséget,** és válassza az **SSH (22)** portot a **Nyilvános bejövő portok kiválasztása** listában. Ebben a rövid útmutatóban ez a lépés az SQL Server-konfiguráció csatlakoztatásához és befejezéséhez szükséges. Ha távolról szeretne csatlakozni az SQL Server kiszolgálóhoz, manuálisan kell engedélyeznie a forgalmat a Microsoft SQL Server által a virtuális gép létrehozása után az interneten keresztüli kapcsolatokhoz használt alapértelmezett portra (1433).

     ![Bejövő portok](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. A következő további lapok beállításait módosíthatja, vagy megtarthatja az alapértelmezett beállításokat.
    * **Lemezek**
    * **Hálózat**
    * **Felügyelet**
    * **Vendég config**
    * **Címkék**

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. A **Véleményezés + létrehozás** ablaktáblán válassza a **Létrehozás lehetőséget.**

## <a name="connect-to-the-linux-vm"></a><a id="connect"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép IP-címét az Azure Portalon találhatja meg.

![IP-cím az Azure Portalon](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Ha Windows rendszeren fut, és nem rendelkezik BASH rendszerhéjjal, telepítsen egy SSH-ügyfelet, például a PuTTY-t.

1. [Töltse le és telepítse a PuTTY-t](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Válassza **a Megnyitás** gombot, és írja be a felhasználónevét és jelszavát a felkérésekközött.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal) ismertető cikkben talál.

> [!Note]
> Ha a rendszer a puty biztonsági riasztást lát arról, hogy a kiszolgáló gazdakulcsa nincs gyorsítótárazva a beállításjegyzékben, válasszon az alábbi lehetőségek közül. Ha megbízik ebben az állomásban, válassza az **Igen** lehetőséget a kulcs putty gyorsítótárhoz való hozzáadásához és a csatlakozás folytatásához. Ha csak egyszer szeretné folytatni a csatlakozást anélkül, hogy a kulcsot hozzáadná a gyorsítótárhoz, válassza a **Nem**lehetőséget. Ha nem bízik meg az állomásban, válassza **a Mégse** lehetőséget a kapcsolat megszüntetéséhez.

## <a name="change-the-sa-password"></a><a id="password"></a> Az SA-jelszó módosítása

Az új virtuális gép egy véletlenszerű SA-jelszóval telepíti az SQL Servert. Állítsa alaphelyzetbe ezt a jelszót, mielőtt csatlakozna az SQL Server szolgáltatáshoz a rendszerösszekötővel.

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

## <a name="configure-for-remote-connections"></a><a id="remote"></a> Konfigurálás távoli kapcsolatokhoz

Ha távolról kell csatlakoznia az Azure-beli virtuális gépen futó SQL Serverhez, ahhoz konfigurálnia kell egy bejövő szabályt a hálózat biztonsági csoportjában. Ez a szabály engedélyezi a forgalmat azon a porton keresztül, amelyet az SQL Server figyel (ez alapértelmezés szerint az 1433-as). A következő lépések bemutatják, hogyan végezheti el ezt a lépést az Azure Portal használatával.

> [!TIP]
> Ha a kiépítés során kiválasztotta az **MS SQL (1433)** bejövő portot, a rendszer már elvégezte ezeket a módosításokat. Továbbléphet a következő szakaszra, amely a tűzfal konfigurálásával foglalkozik.

1. A portálon válassza a **Virtuális gépek** elemet, és válassza ki az SQL Server-t tartalmazó virtuális gépet.
1. A bal oldali navigációs ablak **Beállítások területén**válassza a **Hálózat**lehetőséget.
1. A Hálózat ablakban válassza a **Bejövő port hozzáadása** lehetőséget a Bejövő **portszabályok csoportban.**

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

## <a name="next-steps"></a>További lépések

Most, hogy van egy SQL Server 2017-es virtuális gépe az Azure-ban, helyileg csatlakozhat az **sqlcmd** használatával, és Transact-SQL-lekérdezéseket futtathat.

Ha az Azure virtuális gép távoli SQL Server-kapcsolatok, képesnek kell lennie arra, hogy távolról. A Windows rendszerről egy Linuxon futó SQL Serverhez való csatlakozásra láthat egy példát a [Linuxon futó SQL Serverhez egy Windowson futó SSMS használatával történő csatlakozást](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) ismertető cikkben is. A Visual Studio Code használatával való csatlakozás részleteiről további információt a [Transact-SQL-szkriptek SQL Serverhez a Visual Studio Code használatával történő létrehozását és futtatását](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) ismertető cikkben talál.

A Linux rendszeren lévő SQL Server ről további általános [tudnivalókat az SQL Server 2017 linuxos áttekintése című](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)témakörben talál. További információkat a Linux rendszerű SQL Server 2017-es virtuális gépek használatáról az [SQL Server 2017-es Azure-beli virtuális gépek áttekintésében](sql-server-linux-virtual-machines-overview.md) talál.
