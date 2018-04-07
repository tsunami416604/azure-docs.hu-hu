---
title: MySQL futtató klasszikus Azure virtuális gép létrehozása |} Microsoft Docs
description: Hozzon létre egy Azure virtuális gépen futó Windows Server 2012 R2 és a MySQL-adatbázis, a klasszikus üzembe helyezési modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
ms.openlocfilehash: be2a4232fdb59deb87d6d9340fd22909707294b8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>MySQL telepíthető a Windows Server 2016 rendszerű klasszikus üzembe helyezési modellel létrehozott virtuális gépek
[MySQL](https://www.mysql.com) egy népszerű nyílt forráskódú, akkor az SQL-adatbázis. Ez az oktatóanyag bemutatja, hogyan telepítheti és futtathatja a **MySQL 5.7.18 közösségi változata** egy futó virtuális géphez MySQL-kiszolgálóként **Windows Server 2016**. Lehet, hogy a felhasználói élmény némileg eltérő MySQL vagy Windows Server más verzióiban.

Linux MySQL telepítésével kapcsolatos útmutatásért tekintse meg: [MySQL telepítése Azure](../../linux/mysql-install.md).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>Windows Server 2016 rendszerű virtuális gép létrehozása
Ha még nem rendelkezik a virtuális gép Windows Server 2016 rendszert futtató, ezzel [oktatóanyag](./tutorial.md) a virtuális gép létrehozásához.

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
A virtuális gép létrehozása után hozzácsatolhat adatlemezt nem kötelező. Ad hozzá adatlemezt termelési számítási feladatokhoz, és kevés a hely az operációs rendszer meghajtóján (C:) elkerülése érdekében javasolt, amely magában foglalja az operációs rendszer.

Lásd: [hogyan lehet adatlemezt csatolni egy Windows rendszerű virtuális gép](../attach-managed-disk-portal.md) és kövesse az utasításokat az üres lemez csatolása. Állítsa be a gazdagép gyorsítótár **nincs** vagy **írásvédett**.

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre
A következő lesz [jelentkezzen be a virtuális gép](./connect-logon.md) így MySQL telepítheti.

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Telepítse, MySQL közösségi kiszolgálón futtassa a virtuális gépen
Kövesse az alábbi lépéseket telepítése, konfigurálása és a MySQL Server közösségi verziója fut:

> [!NOTE]
> Letöltése Internet Explorerrel elemek, beállíthatja az Internet Explorer **fokozott biztonsági beállítások** való kikapcsolva, és a letöltési folyamat leegyszerűsítése. A Start menüből, kattintson a felügyeleti eszközök/Server Manager vagy helyi kiszolgáló, majd kattintson az Internet Explorer **fokozott biztonsági beállítások** , és a konfigurációs kikapcsolt).
>
>

1. Miután csatlakozott a virtuális géphez a távoli asztal használatával, kattintson **Internet Explorer** a kezdőképernyőről.
2. Válassza ki a **eszközök** jobb felső sarokban (cogged kerék ikon) gombra, majd **Internetbeállítások**. Kattintson a **biztonsági** lapra, majd a **megbízható helyek** ikonra, majd kattintson a **helyek** gombra. Http://*.mysql.com adhat hozzá a megbízható helyek listájához. Kattintson a **Bezárás**, és kattintson a **OK**.
3. Az a cím az Internet Explorer címsorában, írja be a https://dev.mysql.com/downloads/mysql/.
4. A MySQL hely segítségével keresse meg és töltse le a MySQL Windows Installer legújabb verzióját. Ha a MySQL-telepítő választ, töltse le, amely a teljes beállítása (például a mysql-telepítő-Közösség-5.7.18.0.msi 352.8 MB-os fájlméretben) fájlt, és mentse a telepítő rendelkezik.
5. A telepítő befejezte a letöltését, amikor kattintson **futtatása** elindítani a telepítőt.
6. Az a **licencszerződés** lapon fogadja el a licencszerződést, majd kattintson **következő**.
7. Az a **a telepítés típusának kiválasztása** kattintson, és kattintson a telepítési módot **következő**. A következő lépések feltételezik kiválasztása a **csak a kiszolgáló** telepítés típusa.
8. Ha a **ellenőrizze követelmények** oldal megjelenéskor kattintson **Execute** ahhoz, hogy a telepítő telepíti a hiányzó összetevőket. Kövesse a megjelenő, például a C++ újraterjeszthető csomag futásidejű utasításokat.
9. Az a **telepítési** kattintson **Execute**. Ha a telepítés befejeződött, kattintson **következő**.

10. Az a **termék konfigurációjának** kattintson **következő**.

11. Az a **típusa és a hálózatkezelés** lapján adja meg a kívánt konfigurációs típusa és a kapcsolódási beállításokat, többek között a TCP-portot, ha szükséges. Válassza ki **speciális beállítások megjelenítése**, és kattintson a **következő**.
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. Az a **fiókok és szerepkörök** lapján adjon meg egy erős jelszót a MySQL legfelső szintű. További MySQL felhasználói fiókok hozzáadása, igény szerint, és kattintson a **következő**.

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. Az a **Windows-szolgáltatás** lapon adja meg az alapértelmezett beállításokra vonatkozó változások a MySQL-kiszolgálót futtató Windows-szolgáltatás, igény szerint, és kattintson a **következő**.

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. A választási lehetőségek a a **beépülő modulok és bővítmények** lap opcionálisak. Kattintson a **tovább** a folytatáshoz.
15. Az a **speciális beállítások** lapon szükség esetén adja meg a naplózási beállítások módosításait, és kattintson a **következő**.

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. Az a **kiszolgáló beállításainak alkalmazása** kattintson **Execute**. Ha a konfigurációs lépéseket befejeződött, kattintson **Befejezés**.
17. Az a **termék konfigurációjának** kattintson **következő**.
18. A a **telepítési teljes** lapján kattintson **vágólapra másolás napló** Ha vizsgálja meg később, és kattintson a kívánt **Befejezés**.
19. Írja be a kezdőképernyőről **mysql**, és kattintson a **MySQL 5.7 parancssori ügyfél**.
20. Adja meg a gyökér szintű jelszavát, amelyet a 12. lépés, és lehetősége lesz a kérdés ahol adhat ki parancsokat, amelyekkel beállíthatja a MySQL. A parancsokat és szintaxist, lásd: [MySQL dokumentációjának](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html).

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. Kiszolgáló alapértelmezett beállításait, például a kiindulási és adatok könyvtárak és meghajtókat is konfigurálhatja. További információkért lásd: [6.1.2 kiszolgáló konfigurációs alapértelmezés szerint](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Végpontok konfigurálása

A MySQL szolgáltatás az interneten lévő ügyfélszámítógépek számára elérhető legyen konfigurálja a TCP-port a végpont és egy Windows tűzfalszabály létrehozásához. Alapértelmezett port meg, amelyen a MySQL-kiszolgáló szolgáltatás figyeli a MySQL-ügyfelek értéke 3306. Megadhat egy másik portra, mindaddig, amíg a port pedig a megadott érték konzisztens a **típusa és a hálózatkezelés** oldalon (az előző eljárásban 11. lépés).

> [!NOTE]
> Éles környezetben való használathoz fontolja meg a MySQL-kiszolgáló szolgáltatás elérhetővé tételére az interneten lévő összes számítógép biztonsági következményekkel járhat. Definiálhat a forrás IP-címek, amelyek számára engedélyezett a végpont a egy hozzáférés-vezérlési lista (ACL) használata. További információkért lásd: [hogyan állítsa be végpontok egy virtuális géphez](setup-endpoints.md).
>
>

A végpont a MySQL-kiszolgáló szolgáltatásának konfigurálása:

1. Az Azure portálon kattintson **virtuális gépek (klasszikus)**, kattintson a MySQL-virtuális gép nevét, majd **végpontok**.
2. A parancssávon kattintson **Hozzáadás**.
3. Az a **végpont hozzáadása** írja be egy egyedi nevet az **neve**.
4. Válassza ki **TCP** protokollt.
5. Írja be például a portszám **3306**, mindkét **nyilvános Port** és **magánhálózati Port**, és kattintson a **OK**.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>A MySQL-kezelési forgalom engedélyezése a Windows tűzfal szabály hozzáadása
Egy Windows tűzfalszabály, amely lehetővé teszi a MySQL-forgalom az internetről hozzáadásához futtassa a következő parancsot egy _emelt szintű Windows PowerShell-parancssorában történő_ a MySQL server virtuális gépen.

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>A távoli kapcsolat tesztelése
A MySQL-kiszolgálói szolgáltatást futtató Azure virtuális géphez a távoli kapcsolat teszteléséhez a felhőalapú szolgáltatás, amely tartalmazza a VN DNS-nevét kell megadnia.

1. Az Azure portálon kattintson **virtuális gépek (klasszikus)**, kattintson a MySQL-kiszolgáló virtuális gép nevét, majd **áttekintése**.
2. A virtuális gép irányítópultról, vegye figyelembe a **DNS-név** érték. Például:

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. A helyi számítógépről MySQL vagy a MySQL-ügyfél operációs rendszert futtató a következő parancsot a MySQL felhasználóként bejelentkezéshez.

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   A MySQL felhasználónév használata esetén például _dbadmin3_ és a _testmysql.cloudapp.net_ DNS-beli név a virtuális gép, a következő paranccsal MySQL tudott elindulni:

     MySQL -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>További lépések
MySQL futtatásával kapcsolatos további tudnivalókért tekintse meg a [MySQL dokumentáció](http://dev.mysql.com/doc/).
