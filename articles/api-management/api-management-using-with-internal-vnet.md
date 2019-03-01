---
title: Az Azure API Management használata a belső virtuális hálózatokkal |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be, és az Azure API Management konfigurálása a belső virtuális hálózathoz
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apimpm
ms.openlocfilehash: 9a2cf35203c673d6296754360ac4f794241d4c43
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57008678"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Belső virtuális hálózathoz az Azure API Management szolgáltatás használatával
Azure virtuális hálózatokkal az Azure API Management API-k nem érhető el az interneten is kezelheti. Számos VPN technológia érhetők el a kapcsolatot. Az API Management egy virtuális hálózaton belül két fő módban telepíthető:
* Külső
* Belső

Az API Management üzembe helyezi a módban a belső virtuális hálózathoz, ha a szolgáltatás végpontok (átjáró, a fejlesztői portál, az Azure Portalon, közvetlen felügyelet és a Git) már csak jelennek meg, hogy a hozzáférést a virtuális hálózatokon belüli. A Szolgáltatásvégpontok egyike a nyilvános DNS-kiszolgáló van regisztrálva.

Az API Management belső módban, akkor érhető el a következő esetekben:

* Győződjön meg arról, API-kat biztonságosan elérhetik a privát adatközpontban lévő üzemeltetett kívül, harmadik felek által site-to-site- vagy Azure ExpressRoute VPN-kapcsolatok használatával.
* Engedélyezze a hibrid felhős rendszerekben teszi elérhetővé a felhőalapú API-k és a egy közös átjárón keresztül a helyszíni API-k.
* Egy átjáró egyetlen végpont használatával több földrajzi helyeken üzemeltetett API-k kezelése. 

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben leírt lépések végrehajtásához rendelkeznie:

+ **Aktív Azure-előfizetéssel**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Az Azure API Management-példány**. További információkért lásd: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Az API Management létrehozása a belső virtuális hálózathoz
Az API Management szolgáltatás a belső virtuális hálózathoz egy belső terheléselosztóval (ILB) mögött helyezkedik el.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Engedélyezze a virtuális hálózati kapcsolat az Azure portal használatával

1. Tallózással keresse meg az Azure API Management-példány a [az Azure portal](https://portal.azure.com/).
2. Válassza ki a **Virtuális hálózatot**.
3. Konfigurálja az API Management-példány üzembe helyezni a virtuális hálózaton belül.

    ![Az Azure API Management egy belső virtuális hálózat beállítása menüje][api-management-using-internal-vnet-menu]

4. Kattintson a **Mentés** gombra.

Miután az üzembe helyezés sikeres, megjelenik a szolgáltatás belső virtuális IP-cím az irányítópulton.

![Belső virtuális hálózathoz konfigurálni az API Management az irányítópulton][api-management-internal-vnet-dashboard]

> [!NOTE]
> A teszt konzolon érhető el az Azure Portalon nem fog működni a **belső** virtuális hálózat üzembe helyezve szolgáltatás, az átjáró URL-címe nincs regisztrálva a nyilvános DNS-kiszolgálón. Ehelyett használjon a megadott tesztelési konzol a **fejlesztői portál**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>A virtuális hálózati kapcsolat engedélyezése a PowerShell-parancsmagok használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Virtuális hálózati kapcsolat a PowerShell-parancsmagok használatával is engedélyezheti.

* Hozzon létre egy virtuális hálózaton belül egy API Management-szolgáltatás: A parancsmag [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) egy virtuális hálózaton belül az Azure API Management szolgáltatás létrehozása és konfigurálása, hogy a belső virtuális hálózat típusát.

* Frissítse a meglévő környezet egy API Management szolgáltatás egy virtuális hálózaton belül: A parancsmag [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) áthelyezése egy meglévő API Management szolgáltatást egy virtuális hálózaton belül, és konfigurálja úgy, hogy használja a belső virtuális hálózathoz.

## <a name="apim-dns-configuration"></a>DNS-konfiguráció
Ha az API Management a külső virtuális hálózat módban van, a DNS-ben az Azure felügyeli. Belső virtuális hálózathoz a módban kell kezelni a saját útválasztás.

> [!NOTE]
> API Management-szolgáltatás nem fog IP-címekről érkező kéréseket. Csak válaszol a kérelmekre, a gazdagép nevét a Szolgáltatásvégpontok konfigurálva. Ezek a végpontok közé tartoznak az átjáró, az Azure portal és a fejlesztői portált, közvetlen felügyeleti végpont és Git.

### <a name="access-on-default-host-names"></a>Alapértelmezett állomásnevek eléréséhez
Az API Management-szolgáltatás, például a "contoso" nevű létrehozásakor a következő Szolgáltatásvégpontok alapértelmezés szerint vannak konfigurálva:

   * -Átjáróval vagy proxyval: contoso.azure-API.NET webhelyen

   * Az Azure portal és a fejlesztői portál: contoso.portal.azure-API.NET webhelyen

   * Direct management endpoint: contoso.management.azure-api.net

   * Git: contoso.scm.azure-api.net

Ezek az API Management szolgáltatás végpontjainak eléréséhez hozzon létre egy virtuális gép csatlakozik, amely az API Management üzembe lesz helyezve a virtuális hálózat alhálózatán. Ha a szolgáltatás belső virtuális IP-címe 10.0.0.5, leképezheti a hosts fájl, % SystemDrive%\drivers\etc\hosts, a következő:

   * 10.0.0.5 contoso.azure-API.NET webhelyen

   * 10.0.0.5 contoso.portal.azure-API.NET webhelyen

   * 10.0.0.5 contoso.management.azure-API.NET webhelyen

   * 10.0.0.5     contoso.scm.azure-api.net

A Szolgáltatásvégpontok a virtuális gépből létrehozott elérheti. Ha egyéni DNS-kiszolgáló egy virtuális hálózatot használ, is egy DNS-rekordok létrehozása és bárhonnan elérheti ezeket a végpontokat a virtuális hálózaton. 

### <a name="access-on-custom-domain-names"></a>Hozzáférés az egyéni tartománynevek

   1. Ha nem szeretne hozzáférni az API Management szolgáltatás az alapértelmezett állomásnevek, akkor állíthatja egyéni tartománynevek minden a Szolgáltatásvégpontok az alábbi képen látható módon: 

   ![Egyéni tartomány beállítása az API Management szolgáltatáshoz][api-management-custom-domain-name]

   2. Ezután hozhat létre rekordokat a DNS-kiszolgáló eléréséhez a végpontok, amelyek csak a virtuális hálózaton belül elérhető.

## <a name="routing"> </a> Útválasztás
+ Elosztott terhelésű privát virtuális IP-címet az alhálózat címtartományából lesz fenntartva, és az API Management szolgáltatás végpontjainak a virtuális hálózaton belül eléréséhez használt.
+ Elosztott terhelésű nyilvános IP-cím (VIP) is lehet lefoglalni biztosíthat hozzáférést a felügyeleti szolgáltatás végpontja csak 3443-porton keresztül.
+ Egy alhálózat IP-címtartomány (DIP) egy IP-címet a virtuális hálózaton belüli erőforrások eléréséhez használható, és a virtuális hálózaton kívüli erőforrások eléréséhez használható nyilvános IP-cím (VIP).
+ Elosztott terhelésű nyilvános és magánhálózati IP-címek az Azure portal áttekintés/Essentials panelén található.

## <a name="related-content"> </a>Kapcsolódó tartalom
További tudnivalókért tekintse meg a következő cikkeket:
* [Egy virtuális hálózatot az Azure API Management beállítása közben közös hálózati konfigurációs problémák][Common network configuration problems]
* [Virtuális hálózat – gyakori kérdések](../virtual-network/virtual-networks-faq.md)
* [A DNS-rekord létrehozása](https://msdn.microsoft.com/library/bb727018.aspx)

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-menu.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

