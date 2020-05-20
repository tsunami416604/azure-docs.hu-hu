---
title: Oracle WebLogic Server Azure-alkalmazások | Microsoft Docs
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
ms.openlocfilehash: 9ba4e3a66ae8ef71b39aa26fd54048381237c2fa
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665214"
---
# <a name="oracle-weblogic-server-azure-applications"></a>Oracle WebLogic Server Azure-alkalmazások

## <a name="oracle-weblogic-server-is-a-scalable-enterprise-ready-java-ee-application-server"></a>Az Oracle WebLogic Server egy skálázható, nagyvállalati használatra kész, Java EE-alapú alkalmazáskiszolgáló.

Az Oracle WebLogic Server a világ első Felhőbeli natív, nagyvállalati Java platform-kiszolgálója a többrétegű elosztott vállalati alkalmazások fejlesztéséhez és üzembe helyezéséhez. Az Azure WebLogic Server lehetővé teszi a felhő-számítástechnika megtartását azáltal, hogy nagyobb választékot és rugalmasságot biztosít a WebLogic áttelepítéséhez, beleértve a Java EE-alkalmazások az Azure-felhőbe való áthelyezését és a lehető legnagyobb hatást. Az ajánlatok lehetővé teszik az üzleti alkalmazások gyors elindítását a virtuális hálózat, a tárolás és a Linux-erőforrások automatikus üzembe helyezésével, a WebLogic-kiszolgáló telepítésével, a biztonság beállításával egy hálózati biztonsági csoporttal, a terheléselosztást az Azure app Gateway szolgáltatással, a hitelesítés Azure Active Directory és az adatbázis-kapcsolat enyhítésével.

A különböző forgatókönyvek teljesítéséhez négy ajánlat áll rendelkezésre: egyetlen csomópont rendszergazdai kiszolgáló nélkül, egyetlen csomópont rendszergazdai kiszolgálóval, fürttel és dinamikus fürttel.  Próbálja ki, hogy az ajánlatok díjmentesen elérhetők legyenek.

_Ezek az ajánlatok saját licencet kapnak_. Feltételezik, hogy már beszerzett egy Oracle-licenccel rendelkező megfelelő licencet, és a megfelelő licenccel rendelkezik az ajánlatok futtatásához Microsoft Azure.

_Ha azt szeretné, hogy az áttelepítési forgatókönyvek szorosan működjenek az ezen ajánlatokat fejlesztő mérnöki csapattal, [CONTACT ME](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview) csak_ az [Azure piactéren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/oracle.oraclelinux-wls-cluster?tab=Overview)lépjen kapcsolatba velem gombra. A program-menedzserek, az építészek és a mérnökök hamarosan megközelítik Önt, és elindítják az együttműködést!

### <a name="oracle-weblogic-server-single-node"></a>Oracle WebLogic Server – egyetlen csomópont

Ez az ajánlat egyetlen virtuális gépet foglal le, és telepíti az Oracle WebLogic Servert. Nem hoz létre tartományt, vagy nem indítja el a felügyeleti kiszolgálót. Ez olyan forgatókönyvek esetén hasznos, amelyek nagyvállalati konfigurációval rendelkeznek.

### <a name="oracle-weblogic-server-with-admin-server"></a>Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval

Ez az ajánlat egyetlen virtuális gépet foglal le, és telepíti az Oracle WebLogic Servert. Létrehoz egy tartományt, és elindítja a felügyeleti kiszolgálót, amely lehetővé teszi a tartomány kezelését.

### <a name="oracle-weblogic-server-cluster"></a>Oracle WebLogic Server-fürt

Ez az ajánlat egy magasan elérhető Oracle WebLogic Server Virtual Machines-fürtöt hoz létre. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló el van indítva, amely lehetővé teszi a tartomány kezelését.

### <a name="oracle-weblogic-server-dynamic-cluster"></a>Oracle WebLogic Server dinamikus fürt

Ez az ajánlat egy magasan elérhető és méretezhető dinamikus fürtöt hoz létre az Oracle WebLogic Server Virtual Machines szolgáltatásban. Alapértelmezés szerint a felügyeleti kiszolgáló és az összes felügyelt kiszolgáló el van indítva, amely lehetővé teszi a tartomány kezelését.

## <a name="next-steps"></a>További lépések

Fedezze fel az ajánlatokat az Azure piactéren.

> [!div class="nextstepaction"]
> [Oracle WebLogic Server – egyetlen csomópont](https://portal.azure.com/#create/oracle.20191001-arm-oraclelinux-wls20191001-arm-oraclelinux-wls)

> [!div class="nextstepaction"]
> [Oracle WebLogic-kiszolgáló felügyeleti kiszolgálóval](https://portal.azure.com/#create/oracle.20191009-arm-oraclelinux-wls-admin20191009-arm-oraclelinux-wls-admin)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server-fürt](https://portal.azure.com/#create/oracle.20191007-arm-oraclelinux-wls-cluster20191007-arm-oraclelinux-wls-cluster)

> [!div class="nextstepaction"]
> [Oracle WebLogic Server dinamikus fürt](https://portal.azure.com/#create/oracle.20191021-arm-oraclelinux-wls-dynamic-cluster20191021-arm-oraclelinux-wls-dynamic-cluster)
