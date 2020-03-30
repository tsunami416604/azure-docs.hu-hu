---
title: A Micro Focus Enterprise Server 4.0 és az Enterprise Developer 4.0 telepítése az Azure-ra | Microsoft dokumentumok
description: Az IBM z/OS nagyszámítógépes számítási feladatainak újraüzemeltetése a Micro Focus fejlesztési és tesztelési környezetével az Azure virtuális gépeken (VM-ek).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 05/29/2019
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: a5426c3cd7552b24739f9a20e01d5a4b42bd383c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68834572"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>A Micro Focus Enterprise Server 4.0 és az Enterprise Developer 4.0 telepítése az Azure-ra

Ez a cikk bemutatja, hogyan állíthatja be a [Micro Focus Enterprise Server 4.0-s](https://www.microfocus.com/documentation/enterprise-developer/es30/) és micro Focus Enterprise Developer [4.0-s verziót](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) az Azure-ban.

Az Azure-ban gyakori számítási feladatok egy fejlesztési és tesztelési környezet. Ez a forgatókönyv azért gyakori, mert annyira költséghatékony és könnyen telepíthető és bontható le. Az Enterprise Server segítségével a Micro Focus létrehozta az egyik legnagyobb mainframe rehosting platformot. Z/OS számítási feladatok futtathatók egy olcsóbb x86-os platformon az Azure-on Windows vagy Linux virtuális gépek (VM-ek) használatával.

Ez a telepítő a Windows Server 2016-lemezképet futtató Azure-virtuális gépeket használja az Azure Piactérről, és a Microsoft SQL Server 2017 már telepítve van. Ez a beállítás az Azure Stackre is vonatkozik.

Az Enterprise Server megfelelő fejlesztői környezete az Enterprise Developer, amely microsoft Visual Studio 2017-es vagy újabb, Visual Studio Community (ingyenesen letölthető) vagy Eclipse szolgáltatáson fut. Ez a cikk bemutatja, hogyan telepítheti azt a Visual Studio 2017-es vagy újabb telepített Windows Server 2016 virtuális gépével.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt tekintse meg az alábbi előfeltételeket:

- Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- A Micro Focus szoftver és egy érvényes licenc (vagy próbalicenc). Ha Ön már meglévő Micro Focus-ügyfél, forduljon a Micro Focus képviselőjéhez. Ellenkező esetben [kérjen próbaverziót.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

- Az Enterprise Server és az [Enterprise Developer dokumentációjának](https://www.microfocus.com/documentation/enterprise-developer/#")beszerezése .

> [!NOTE]
> Ajánlott eljárás: hozzon létre egy helyek közötti virtuális magánhálózati (VPN) alagút vagy egy ugródoboz, így szabályozhatja az Azure virtuális gépekhez való hozzáférést.

## <a name="install-enterprise-server"></a>Az Enterprise Server telepítése

1. A nagyobb biztonság és kezelhetőség érdekében fontolja meg egy új erőforráscsoport létrehozását csak ehhez a projekthez – például **az RGMicroFocusEntServer .** Használja a név első részét az Azure-ban, hogy kiválassza az erőforrás típusát, hogy megkönnyítse a listában való helyt.

2. Virtuális gépet hoz létre. Az Azure Piactéren válassza ki a kívánt virtuális gépet és operációs rendszert. Az alábbiakban az alábbiakban ajánlott beállítással áll a következőkben:

    - **Enterprise Server**: Válassza az ES2 v3 virtuális gép (2 vCPU-val és 16 GB memóriával) lehetőséget windows Server 2016 és SQL Server 2017 telepítésével. Ez a lemezkép az Azure Piactérről érhető el. Az Enterprise Server az Azure SQL Database-t is használhatja.

    - **Vállalati fejlesztő:** Válassza a B2 ms vM (2 vCPU és 8 GB memória) lehetőséget a Windows 10 és a Visual Studio telepítésével. Ez a lemezkép az Azure Piactérről érhető el.

3. Az **Alapok szakaszban** adja meg felhasználónevét és jelszavát. Válassza ki a virtuális gépekhez használni kívánt **előfizetést** és **helyet/régiót.** Válassza **az RGMicroFocusEntServer elemet** az erőforráscsoporthoz.

4. Helyezze mindkét virtuális gépet ugyanabba a virtuális hálózatba, hogy kommunikálhassanak egymással.

5. Fogadja el az alapértelmezett beállításokat a többi beállításhoz. Ne feledje, hogy a virtuális gépek rendszergazdája által létrehozott felhasználónevet és jelszót.

6. A virtuális gépek létrehozása után nyissa meg a 9003-as, 86-os és 80-as bejövő portokat a HTTP és a 3389-es portokat az RDP-hez az Enterprise Server számítógépen és 3389-et a fejlesztői számítógépen.

7. Az Enterprise Server virtuális gépre való bejelentkezéshez az Azure Portalon válassza ki az ES2 v3 virtuális gépet. Nyissa meg az **Áttekintés szakaszt,** és válassza a **Csatlakozás** lehetőséget az RDP-munkamenet elindításához. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatokkal.

8. Az RDP-munkamenetből töltse be a következő két fájlt. Mivel Windows rendszert használ, a fájlokat az RDP-munkamenetbe húzhatja:

    - **es\_40.exe**, az Enterprise Server telepítőfájlja.

    - **mflic**, a megfelelő licencfájl – az Enterprise Server nem töltődik be nélküle.

9. A telepítés elindításához kattintson duplán a fájlra. Az első ablakban válassza ki a telepítési helyet, és fogadja el a végfelhasználói licencszerződést.

     ![Micro Focus Enterprise Server telepítőképernyője](media/01-enterprise-server.png)

     A telepítés befejeztével a következő üzenet jelenik meg:

     ![Micro Focus Enterprise Server telepítőképernyője](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>Frissítések keresése

A telepítés után mindenképpen ellenőrizze a további frissítéseket, mivel számos előfeltétel, például a Microsoft C++ újraterjeszthető és a .NET Framework telepítve van az Enterprise Server kiszolgálóval együtt.

### <a name="upload-the-license"></a>A licenc feltöltése

1. Indítsa el a Micro Focus licencfelügyeletet.

2. Kattintson **a Start** \> **Micro Focus License Manager** \> **License Administration gombra,** majd a **Telepítés** fülre. Ha például egy fájlhoz a **Licencfájlban**tallózással keresse meg a virtuális gépre korábban feltöltött **mflic** fájlt, és válassza a **Licencek telepítése**lehetőséget.

     ![A Micro Focus licencfelügyelete párbeszédpanel](media/03-enterprise-server.png)

3. Ellenőrizze, hogy az Enterprise Server bevan-e töltve. Próbálja meg elindítani az Enterprise Server <http://localhost:86/> Administration webhelyet egy böngészőből ezzel az URL-címmel. Az Enterprise Server Administration lap az ábrán látható módon jelenik meg.

     ![Vállalati kiszolgálófelügyelet lap](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>Az Enterprise Developer telepítése a fejlesztői számítógépre

1. Jelölje ki a korábban létrehozott erőforráscsoportot (például **RGMicroFocusEntServer),** majd válassza ki a fejlesztői lemezképet.

2. A virtuális gépbe való bejelentkezéshez nyissa meg az **Áttekintés szakaszt,** és válassza a **Csatlakozás lehetőséget.** Ez a bejelentkezés elindítja az RDP-munkamenetet. Jelentkezzen be a virtuális géphez létrehozott hitelesítő adatokkal.

3. Az RDP-munkamenetből töltse be a következő két fájlt (ha szeretné, húzza a mutatót):

    - **edvs2017.exe**, az Enterprise Server telepítőfájlja.

    - **mflic**, a megfelelő licencfájl (Az Enterprise Developer nem töltődik be nélküle).

4. A telepítés elindításához kattintson duplán az **edvs2017.exe** fájlra. Az első ablakban válassza ki a telepítési helyet, és fogadja el a végfelhasználói licencszerződést. Ha szeretné, válassza **a Rumba 9.5 telepítése lehetőséget** a terminál emulátor telepítéséhez, amire valószínűleg szüksége lesz.

     ![Micro Focus Enterprise Developer for Visual Studio 2017 Setup párbeszédpanel](media/04-enterprise-server.png)

5. A telepítés befejezése után a következő üzenet jelenik meg:

     ![Sikeres beállítási üzenet](media/05-enterprise-server.png)

6. Indítsa el a Micro Focus licenckezelőt ugyanúgy, mint az Enterprise Server esetében. Válassza **a Start** \> Micro Focus License Manager License Administration **(Mikrofókusz** **licenckezelő)** \> **lehetőséget,** majd kattintson a Telepítés fülre.

7. Válassza ki a feltöltendő licencformátum típusát: licencfájlt vagy 16 karakterből álló licenckódot. Ha például egy fájlhoz a **Licencfájlban**tallózással keresse meg a virtuális gépre korábban feltöltött **mflic** fájlt, és válassza a **Licencek telepítése**lehetőséget.

     ![A Micro Focus licencfelügyelete párbeszédpanel](media/07-enterprise-server.png)

Amikor az Enterprise Developer betöltődik, a Micro Focus fejlesztési és tesztelési környezetének üzembe helyezése befejeződött az Azure-ban!

## <a name="next-steps"></a>További lépések

- [A Bank bemutató alkalmazás beállítása](./demo.md)
- [Nagyvállalati kiszolgáló futtatása Docker-tárolókban](./run-enterprise-server-container.md)
- [Nagyszámítógépes alkalmazásmigrálás](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
