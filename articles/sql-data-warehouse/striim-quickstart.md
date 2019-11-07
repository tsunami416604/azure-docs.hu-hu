---
title: Striim – első lépések
description: Gyorsan elsajátíthatja a Striim és a Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 65dc0b8fcece4364f3828fee09221612b999df4e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685348"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>A Striim Azure SQL DW Marketplace-ajánlat telepítési útmutatója

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik SQL Data Warehouse meglévő példányával.

Keresse meg a Striim az Azure Marketplace-en, és válassza ki a Striim az adatintegrációhoz SQL Data Warehouse (szakaszos) beállítást. 

![A Striim telepítése][install]

Konfigurálja a Striim virtuális gépet a megadott tulajdonságokkal, és jegyezze fel a Striim-fürt nevét, jelszavát és rendszergazdai jelszavát.

![Striim konfigurálása][configure]

Az üzembe helyezést követően kattintson \<virtuális gép neve >-masternode elemre a Azure Portalban, kattintson a kapcsolat elemre, és másolja a bejelentkezést a VM helyi fiók használatával 

![A Striim SQL Data Warehousehoz való kapcsolódása][connect]

Töltse le a sqljdbc42. jar fájlt <https://www.microsoft.com/en-us/download/details.aspx?id=54671>ról a helyi gépre. 

Nyisson meg egy parancssori ablakot, és változtassa meg a könyvtárakat a JDBC jar letöltésére. SCP a jar-fájlt a Striim virtuális géphez, és a Azure Portal címről és jelszaváról

![Jar-fájl másolása a virtuális gépre][copy-jar]

Nyisson meg egy másik parancssori ablakot, vagy használjon SSH-segédprogramot SSH-ba a Striim-fürtön

![SSH a fürtbe][ssh]

A következő parancsok végrehajtásával helyezze át a JDBC jar-fájlt a Striim lib könyvtárába, és indítsa el és állítsa le a kiszolgálót.

   1. sudo Su
   2. CD-/tmp
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl leállítása striim-Node
   5. systemctl leállítása striim – adatbázis-kezelő
   6. systemctl Start striim – adatbázis-kezelő
   7. systemctl-striim indítása – csomópont

![A Striim-fürt elindítása][start-striim]

Most nyissa meg a kedvenc böngészőjét, és navigáljon \<DNS-név >: 9080

![Lépjen a bejelentkezési képernyőre][navigate]

Jelentkezzen be a Azure Portalban beállított felhasználónévvel és jelszóval, és válassza ki a kívánt varázslót a kezdéshez, vagy lépjen az alkalmazások oldalra az egérrel a fogd és vidd felhasználói felület használatának megkezdéséhez.

![Bejelentkezés kiszolgálói hitelesítő adatokkal][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
