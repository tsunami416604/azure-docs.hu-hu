---
title: Striim gyorsindítás
description: A Striim és az Azure SQL Data Warehouse segítségével gyorsan ismerkedés.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349947"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL DW piactér-ajánlattelepítési útmutató

Ez a rövid útmutató feltételezi, hogy már rendelkezik az SQL Data Warehouse egy már meglévő példányával.

Striim keresése az Azure Piactéren, és válassza a Striim adatintegráció az SQL Data Warehouse (Szakaszos) opciót 

![Striim telepítése][install]

Konfigurálja a Striim virtuális gép ét a megadott tulajdonságokkal, megjegyezve a Striim-fürt nevét, jelszavát és rendszergazdai jelszavát

![Striim konfigurálása][configure]

Üzembe helyezés után \<kattintson a virtuális gép nevére>-masternode az Azure Portalon, kattintson a Csatlakozás elemre, és másolja a bejelentkezést a virtuális gép helyi fiókjával 

![Striim csatlakoztatása az SQL Data Warehouse-hoz][connect]

Töltse le az sqljdbc42.jar-t <https://www.microsoft.com/en-us/download/details.aspx?id=54671> a helyi gépre. 

Nyisson meg egy parancssori ablakot, és módosítsa a könyvtárakat a JDBC-edény letöltési helyére. SCP a jar fájlt a Striim virtuális gép, a cím és a jelszó beszerzése az Azure Portalon

![Jar fájl másolása a virtuális gépre][copy-jar]

Nyisson meg egy másik parancssori ablakot, vagy használjon ssh segédprogramot a Striim fürtbe

![SSH a fürtbe][ssh]

Hajtsa végre a következő parancsokat a JDBC jar fájl Striim lib könyvtárába való áthelyezéséhez, és indítsa el és állítsa le a kiszolgálót.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-csomópont
   5. systemctl stop striim-dbms
   6. systemctl start striim-dbms
   7. systemctl start striim-node

![A Striim-fürt indítása][start-striim]

Most nyissa meg kedvenc böngészőjét, és keresse meg a \<DNS-név>:9080

![Navigálás a bejelentkezési képernyőre][navigate]

Jelentkezzen be az Azure Portalon beállított felhasználónévvel és jelszóval, és válassza ki a kívánt varázslót az első lépésekhez, vagy lépjen az Alkalmazások lapra a drag and drop felhasználói felület használatának megkezdéséhez

![Bejelentkezés kiszolgálói hitelesítő adatokkal][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
