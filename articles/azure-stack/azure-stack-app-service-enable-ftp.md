---
title: "FTP-veremben az Azure App Service-ben engedélyezése |} Microsoft Docs"
description: "Az App Service Azure veremben FTP engedélyezéséhez szükséges lépések"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: erikje
ms.openlocfilehash: 9cadc57831ac7f7e5d32b10a4a87dab3fac02958
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-ftp-in-app-service-on-azure-stack"></a>FTP engedélyezése az Azure Stack App Service szolgáltatásában

Miután sikeresen telepítette a veremben Azure App Service, ha engedélyezni szeretné az FTP-közzététel, így a bérlők feltöltheti saját fájljainak és a tartalom, van néhány további lépést kell végrehajtani.  A későbbi kiadásokban ezeket a lépéseket automatizált lesz.

> [!NOTE]
> Ezeket a lépéseket a szolgáltatás vagy az App Service konfigurálása Azure verem erőforrás-szolgáltató a vállalati rendszergazdák vonatkoznak.

## <a name="enable-ftp"></a>FTP engedélyezése

1.  Jelentkezzen be a szolgáltatás-rendszergazdaként a verem Azure portálon.
2.  Keresse meg a **hálózati illesztőt** válassza ki a **FTP-hálózati** alatt **erőforráscsoport** - **App Service-helyi**. ![Az Azure verem hálózati illesztők][1]
3.  Megjegyzés: a **nyilvános IP-cím** , a **FTP-hálózati**. 
![Az Azure verem hálózati illesztő részletei][2]
4.  Ezután keresse meg a **virtuális gépek** válassza ki a **FTP0-VM**. ![A verem Azure virtuális gépek][3]
5.  A virtuális gép használata a távoli asztal munkamenetet nyit meg a **Connect** gombra, és jelentkezzen be a munkamenet az App Service üzembe helyezése során beállított rendszergazdai hitelesítő adatokkal.  
![A verem Azure virtuális gép részletei][4]
6.  Nyissa meg **Internet Information Service (IIS) kezelője** az FTP virtuális gépen (FTP0-VM).
7.  A **helyek** válasszon **FTP-hely üzemeltetéséhez**.
8.  Nyissa meg **az FTP-Tűzfaltámogatás**. ![IIS-kezelőt az App Service FTP0-VM][5]
9.  Adja meg, az FTP-hálózati adapter, majd kattintson a nyilvános IP-cím **alkalmaz** ![IIS-kezelő FTP-Tűzfaltámogatás][6]

## <a name="validate-the-enabling-of-ftp"></a>Ellenőrizze a FTP engedélyezése

1.  Jelentkezzen be a verem Azure portál vagy a szolgáltatás-rendszergazda vagy a bérlőt.
2.  Keresse meg a **alkalmazásszolgáltatások** válassza ki egy webes, mobil, vagy létrehozott API-alkalmazás. ![App Services][7]
3.  Az alkalmazás részletek feljegyzés az **FTP-állomásnév** és **FTP vagy üzembe helyező felhasználónév**. ![Az App Service-alkalmazás adatait][8]
> [!NOTE]
> Ha nem látja a bejegyzés **FTP vagy üzembe helyező felhasználónév**, meg kell adnia a központi telepítési hitelesítő adatok használatával a **üzembe helyezési hitelesítő adatok** panelen.

4.  Nyissa meg a Windows Intézőt, meg kell adnia az FTP-állomásnév például ftp://ftp.appservice.azurestack.local fájl címsor
5.  Amikor a rendszer kéri adja meg a **üzembe helyezési hitelesítő adatok** lépésben feljegyzett 3, ha a szolgáltatás engedélyezve van az app service alkalmazás tartalmát könyvtárlista látni fogja. ![FTP-fájlok listázása][9]
<!--Image references-->
[1]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interfaces.png
[2]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-network-interface-details.png
[3]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines.png
[4]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-virtual-machines-FTP0-VM.png
[5]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager.png
[6]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-IIS-Manager-FTP-Firewall-Support.png
[7]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-services.png
[8]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-app-service-app-detail.png
[9]: ./media/azure-stack-app-service-enable-ftp/azure-stack-app-service-enable-ftp-validate-ftp-file-listing.png
