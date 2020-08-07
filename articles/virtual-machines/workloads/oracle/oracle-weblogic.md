---
title: Mi az az Azure-beli Oracle WebLogic Server?
description: Ismerje meg, hogyan futtathatja az Oracle WebLogic Servert Microsoft Azureon.
services: virtual-machines-linux
documentationcenter: ''
author: edburns
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2020
ms.author: edburns
ms.openlocfilehash: fe247e75040f658beb94a66176f802993268a7d8
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87851858"
---
# <a name="what-is-oracle-weblogic-server-on-azure"></a>Mi az az Azure-beli Oracle WebLogic Server?

Ez az oldal a WebLogic-kiszolgáló (WLS) Azure Virtual Machines-on való futtatásának megoldásait ismerteti.  Ezeket a megoldásokat az Oracle és a Microsoft közösen fejleszti ki.

Az Oracle WebLogic Server a világ első Felhőbeli natív, nagyvállalati Java platform-kiszolgálója a többrétegű elosztott vállalati alkalmazások fejlesztéséhez és üzembe helyezéséhez. Az Azure WebLogic Server lehetővé teszi a felhő-számítástechnika megölelését.  Nagyobb választékot és rugalmasságot biztosít a WebLogic áttelepítéshez, beleértve a Java EE-alkalmazások Azure-felhőbe való átemelését és átváltását.   A WLS az Azure-ban nagy hatással van egy kis erőfeszítésre. Az ajánlatok lehetővé teszik az üzletági (LOB) alkalmazások gyors elindítását.  Minden ajánlat automatikusan kiépíti a virtuális hálózati, a tárolási és a Linux-erőforrásokat.  A WebLogic-kiszolgáló a nulla erőfeszítéssel van telepítve.  A WLS az Azure-ban egy hálózati biztonsági csoporttal, a terheléselosztás az Azure app Gateway szolgáltatással, a hitelesítés és a Azure Active Directory használatával automatikusan csatlakozik a meglévő adatbázishoz.

:::image type="content" source="media/oracle-weblogic/wls-on-azure.gif" alt-text="Az Azure Portalon üzembe helyezheti a WebLogic-kiszolgálót az Azure-ban":::

A különböző forgatókönyvek teljesítéséhez négy ajánlat áll rendelkezésre: egyetlen csomópont rendszergazdai kiszolgáló nélkül, egyetlen csomópont rendszergazdai kiszolgálóval, fürttel és dinamikus fürttel.  Próbálja ki az ajánlatokat, ingyenesen elérhetők.

_Ezek az ajánlatok saját licencet kapnak_. Feltételezik, hogy már megkapta a megfelelő licenceket az Oracle-vel, és megfelelő licenccel rendelkezik az ajánlatok futtatásához Microsoft Azure.

_Ha az áttelepítési forgatókönyveket az ezen ajánlatokat fejlesztő mérnöki csapattal szorosan szeretné használni, válassza az [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) _ [Azure piactéren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)a kapcsolatfelvétel gombot. A program-menedzserek, az építészek és a mérnökök hamarosan megközelítik Önt, és elkezdik az együttműködést!

## <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server – egyetlen csomópont

Ez az ajánlat egyetlen virtuális gépet foglal le, és telepíti a WLS. Nem hoz létre tartományt, vagy nem indítja el a felügyeleti kiszolgálót. Az önálló csomópontok olyan helyzetekben hasznosak, amelyek nagyvállalati konfigurációval rendelkeznek.

## <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval

Ez az ajánlat egyetlen virtuális gépet foglal le, és telepíti a WLS. Létrehoz egy tartományt, és elindítja a felügyeleti kiszolgálót, amely lehetővé teszi a tartomány kezelését.

## <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-fürt

Ez az ajánlat egy magasan elérhető WLS virtuális gépekből álló fürtöt hoz létre. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló el van indítva, amely lehetővé teszi a tartomány kezelését.

## <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server dinamikus fürt

Ez az ajánlat egy magasan elérhető és méretezhető dinamikus fürtöt hoz létre a WLS virtuális gépekhez. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló el van indítva, amely lehetővé teszi a tartomány kezelését.

## <a name="next-steps"></a>Következő lépések

Fedezze fel az ajánlatokat az Azure piactéren.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server – egyetlen csomópont](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-fürt](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server dinamikus fürt](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
