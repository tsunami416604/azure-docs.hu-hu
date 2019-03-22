---
title: Linux rendszerű SQL Server 2017-virtuálisgép létrehozása az Azure-ban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Linux rendszerű SQL Server 2017-virtuálisgépet az Azure Portalon.
services: virtual-machines-linux
author: MashaMSFT
manager: craigg
ms.date: 12/5/2018
ms.topic: conceptual
tags: azure-service-management
ms.devlang: na
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 468db9d62a98e079fbe6954843e23a518eaabd0a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011720"
---
# <a name="provision-a-linux-sql-server-virtual-machine-in-the-azure-portal"></a>Linux rendszerű SQL Server-virtuálisgép létrehozása az Azure Portalon

> [!div class="op_single_selector"]
> * [Linux](provision-sql-server-linux-virtual-machine.md)
> * [Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)

Ez a rövid útmutató az Azure Portalon használhatja a telepített SQL Server 2017 Linux rendszerű virtuális gép létrehozásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* [Linux rendszerű SQL-virtuálisgép létrehozása a katalógusból](#create)
* [Csatlakozás az új virtuális géphez ssh használatával](#connect)
* [Az SA-jelszó módosítása](#password)
* [Konfigurálás távoli kapcsolatokhoz](#remote)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a id="create"></a> Linux rendszerű, telepített SQL Serverrel rendelkező virtuális gép létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A bal oldali panelen válassza ki a **erőforrás létrehozása**.

1. Az a **erőforrás létrehozása** ablaktáblán válassza előbb **számítási**.

1. Válassza ki **összes** mellett a **kiemelt** fejléc.

   ![Az összes virtuálisgép-rendszerkép megjelenítése](./media/provision-sql-server-linux-virtual-machine/azure-compute-blade.png)

1. A Keresés mezőbe írja be a **SQL Server 2017**, és válassza ki **Enter** kereséséhez.

1. Korlátozza a keresési eredményeket kiválasztásával **operációs rendszer** > **Redhat**. Ezután a **közzétevő**, válassza a **Microsoft**.

    ![Keresési szűrő az SQL Server 2017-alapú virtuálisgép-rendszerképekhez](./media/provision-sql-server-linux-virtual-machine/searchfilter.png)

1. Válasszon egy Linux rendszerű SQL Server 2017-rendszerképet a keresési eredmények közül. Ebben az oktatóanyagban **ingyenes SQL Server-licenc: Az SQL Server 2017 Developer, a Red Hat Enterprise Linux 7.4**.

   > [!TIP]
   > A Developer edition teszi lehetővé az Enterprise edition, de nincs SQL Server licencelési költségeit funkcióját végzett tesztelést és fejlesztést. Csak a Linux rendszerű virtuális gép futtatásával járó költségeket kell kifizetnie.

1. A **telepítési modell kiválasztása**, válassza ki a számítási feladat igényeinek megfelelő telepítési modell kiválasztása.

    > [!Note]
    > Új számítási feladatok esetén használjon **Resource Manager**. Szeretne csatlakozni a meglévő virtuális hálózattal, válassza ki a virtuális hálózat telepítési módját a számítási feladatok számára. Üzembe helyezési modellel kapcsolatos további információkért lásd: [Azure Resource Manager és klasszikus üzembe helyezési modellek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

1. Kattintson a **Létrehozás** gombra.

### <a name="set-up-your-linux-vm"></a>A Linux rendszerű virtuális gép beállítása

1. Az a **alapjai** lapon jelölje be a **előfizetés** és **erőforráscsoport**. 

    ![Alapvető beállítások ablak](./media/provision-sql-server-linux-virtual-machine/basics.png)

1. A **virtuális gép neve**, adjon meg egy nevet az új Linux rendszerű virtuális gép.
1. Ezután írja be vagy válassza ki a következő értékeket:
   * **Régió**: Válassza ki az Önnek megfelelő Azure-régióban.
   * **Rendelkezésre állási beállítások**: Válassza ki a rendelkezésre állás és redundancia a legjobb a alkalmazásait és adatait.
   * **Méretének módosítása**: Ezt a beállítást válassza ki a gép méretét, és ha ezzel elkészült, válassza ki a **kiválasztása**. A virtuális gépek méretével kapcsolatban további információt a [Linux rendszerű virtuális gépek méreteit](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes) ismertető cikkben talál.

     ![Virtuális gép méretének kiválasztása](./media/provision-sql-server-linux-virtual-machine/vmsizes.png)

   > [!TIP]
   > Fejlesztési és tesztelési célokra, használhatja a Virtuálisgép-méretet **DS2** vagy újabb verziója. A teljesítményteszteléshez használjon **DS13** vagy nagyobb méretet.

   * **Hitelesítési típus**: Válassza ki **nyilvános SSH-kulcs**.

     > [!Note]
     > Választhat, hogy a hitelesítéshez egy SSH-s nyilvános kulcsot vagy egy jelszót használ. Az SSH használata biztonságosabb. Az SSH-kulcs létrehozásával kapcsolatban lásd az [SSH-kulcsok az Azure-ban történő létrehozásának lépéseit Linux és Mac rendszeren Linux rendszerű virtuális gépek számára](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

   * **Felhasználónév**: Adja meg a rendszergazda a virtuális gép nevét.
   * **Nyilvános SSH-kulcs**: Adja meg a nyilvános RSA-kulcsot.
   * **Nyilvános bejövő portok**: Válassza a **lehetővé teszi a kiválasztott portok** , és válassza ki a **SSH (22)** a portot a **nyilvános bejövő portok kiválasztása** listája. Ebben a rövid útmutatóban az ebben a lépésben csatlakozhat, és az SQL Server konfigurálása az szükség. Ha távolról szeretne az SQL Serverhez kapcsolódni, válassza ki az **MS SQL (1433)** lehetőséget is a 1433-as port megnyitásához az interneten keresztüli kapcsolatokhoz.

   ![Bejövő portok](./media/provision-sql-server-linux-virtual-machine/port-settings.png)

1. Ne módosítsa a beállításokat a következő további lappal kíván, vagy meghagyhatja az alapértelmezett beállításokat.
    * **Lemezek**
    * **Hálózat**
    * **Felügyeleti**
    * **Vendég-config**
    * **Címkék**

1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Az a **felülvizsgálat + létrehozása** ablaktáblán válassza **létrehozás**.

## <a id="connect"></a> Kapcsolódás a Linux rendszerű virtuális géphez

Ha már megnyitott egy BASH-parancssort, csatlakozzon az Azure-beli virtuális géphez az **ssh** paranccsal. A következő parancsban helyettesítse be a Linux rendszerű virtuális gép felhasználónevét és IP-címét a csatlakozáshoz.

```bash
ssh azureadmin@40.55.55.555
```

A virtuális gép IP-címét az Azure Portalon találhatja meg.

![IP-cím az Azure Portalon](./media/provision-sql-server-linux-virtual-machine/vmproperties.png)

Ha Windows-ban futtatott, és nem rendelkezik bash, telepítse az SSH-ügyfelet, például a putty-kapcsolaton keresztül.

1. [Töltse le és telepítse a PuTTY-t](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Futtassa a PuTTY-t.

1. A PuTTY konfigurációs képernyőjén adja meg a virtuális gép nyilvános IP-címét.

1. Válassza ki **nyílt** , és írja be a felhasználónevét és jelszavát, amikor a rendszer kéri.

A Linux rendszerű virtuális gépekhez való csatlakozásról további információt a [Linux rendszerű virtuális gép az Azure-ban a Portal használatával történő létrehozását](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-portal) ismertető cikkben talál.

> [!Note]
> Ha a kiszolgáló-állomás kulcsát a beállításjegyzékben nem gyorsítótárazott információ PuTTY biztonsági riasztást látja, az alábbi lehetőségek közül választhat. Ha ez a gazdagép megbízható, válassza ki a **Igen** adja hozzá a kulcsot a putty-kapcsolaton keresztül a gyorsítótárba, és folytatja a csatlakozás. Ha csak egyszer csatlakozásról lebonyolító, hozzáadása nélkül a kulcs a gyorsítótárhoz, jelölje be **nem**. Ha ez a gazdagép nem megbízható, válassza ki a **Mégse** kénytelen volt megszakítani ezt a kapcsolatot.

## <a id="password"></a> Az SA-jelszó módosítása

Az új virtuális gép egy véletlenszerű SA-jelszóval telepíti az SQL Servert. Ez a jelszó alaphelyzetbe állítása, mielőtt az SA-bejelentkezéssel csatlakozni az SQL Serverhez.

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

> [!TIP]
> Ha a kiépítés során kiválasztotta az **MS SQL (1433)** bejövő portot, a rendszer már elvégezte ezeket a módosításokat. Továbbléphet a következő szakaszra, amely a tűzfal konfigurálásával foglalkozik.

1. A portálon válassza a **Virtuális gépek** elemet, és válassza ki az SQL Server-t tartalmazó virtuális gépet.
1. A bal oldali navigációs ablaktáblán alatt **beállítások**válassza **hálózatkezelés**.
1. A hálózatkezelés ablakban válassza ki a **vegye fel a bejövő portot** alatt **bejövő portszabályokat**.

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

Ha konfigurálta az Azure virtuális Géphez a távoli SQL Server-kapcsolatokhoz, való távoli kapcsolódást kell lennie. A Windows rendszerről egy Linuxon futó SQL Serverhez való csatlakozásra láthat egy példát a [Linuxon futó SQL Serverhez egy Windowson futó SSMS használatával történő csatlakozást](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-ssms) ismertető cikkben is. A Visual Studio Code használatával való csatlakozás részleteiről további információt a [Transact-SQL-szkriptek SQL Serverhez a Visual Studio Code használatával történő létrehozását és futtatását](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode) ismertető cikkben talál.

A linuxon futó SQL Server általános információ: [áttekintése az SQL Server 2017 linuxon](https://docs.microsoft.com/sql/linux/sql-server-linux-overview). További információkat a Linux rendszerű SQL Server 2017-es virtuális gépek használatáról az [SQL Server 2017-es Azure-beli virtuális gépek áttekintésében](sql-server-linux-virtual-machines-overview.md) talál.
