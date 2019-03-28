---
title: Gyors üzembe helyezési Striim az Azure SQL Data Warehouse |} A Microsoft Docs
description: Gyors használatbavétel a Striim és az Azure SQL Data warehouse-bA.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: a835fbd427408afae9ea6ae1b1bc2a84c18b0c34
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540454"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW Piactéri ajánlat telepítési útmutató

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik egy meglévő példányát az SQL Data warehouse-bA.

Az Azure Marketplace-en Striim kereshet, és válassza ki a Striim Adatintegráció az SQL Data warehouse-ba (előkészített) lehetőséget 

![Telepítse a Striim][install]

A Striim virtuális gép konfigurálása a megadott tulajdonságokkal, jegyezze fel a Striim fürt nevét, a jelszó és a rendszergazdai jelszó

![Striim konfigurálása][configure]

Üzembe helyezése után kattintson a \<virtuális gép neve >-masternode az Azure Portalon kattintson a Kapcsolódás elemre, és másolja a virtuális gép helyi fiókjával bejelentkezés 

![Striim csatlakozhat az SQL Data warehouse-bA][connect]

Töltse le a sqljdbc42.jar a <https://www.microsoft.com/en-us/download/details.aspx?id=54671> a helyi gépen. 

Nyisson meg egy parancssori ablakot, és módosítsa a könyvtárat, amelybe letöltötte a JDBC jar. Szolgáltatáskapcsolódási pont a jar-fájlt a Striim virtuális gépre, a cím és jelszó lekérése az Azure Portalon

![Másolja a jar-fájlt a virtuális géphez][copy-jar]

Nyisson meg egy másik parancssori ablakot, vagy használjon egy ssh segédprogram ssh a Striim be a fürtben

![SSH-t a fürt][ssh]

Hajtsa végre az alábbi parancsokat a JDBC jar-fájl áthelyezése a Striim lib könyvtárba, és indítsa el, és a kiszolgáló leállítására.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-csomópont
   5. striim – dbms systemctl stop
   6. systemctl kezdő striim – dbms
   7. systemctl kezdő striim-csomópont

![A Striim fürt elindításához][start-striim]

Most nyissa meg a kedvenc böngészőjét, és navigáljon \<DNS-neve >: 9080

![Keresse meg a bejelentkezési képernyőn][navigate]

Jelentkezzen be a felhasználónevét és jelszavát, állítsa be az Azure Portalon, és válassza az előnyben részesített varázsló első lépései, vagy az alkalmazások oldalon húzza a használatának megkezdéséhez, és dobja el a felhasználói felület

![Kiszolgáló hitelesítő adataikkal jelentkezzenek be][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
