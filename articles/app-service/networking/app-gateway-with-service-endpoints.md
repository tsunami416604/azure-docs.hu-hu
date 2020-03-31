---
title: Alkalmazásátjáró-integráció szolgáltatásvégekkel - Azure App Service | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy az Application Gateway hogyan integrálható az Azure App Service szolgáltatás-végpontokkal védett szolgáltatással.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 5e32baa10e98f0f57a861f8cebfb7506ad615631
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980061"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Alkalmazásátjáró integrációja szolgáltatásvégekkel
Az App Service három változata van, amelyek az Azure Application Gateway-rel való integráció némileg eltérő konfigurációját igénylik. A változatok közé tartozik a rendszeres App Service - más néven több-bérlős, belső terheléselosztó (ILB) App Service Environment (ASE) és a külső ASE. Ez a cikk bemutatja, hogyan konfigurálhatja az App Service (több-bérlős) és az ILB-vel kapcsolatos szempontok at.

## <a name="integration-with-app-service-multi-tenant"></a>Integráció az App Service szolgáltatással (több-bérlős)
App Service (több-bérlős) rendelkezik egy nyilvános internet-néző végpont. [A szolgáltatás-végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md) használatával csak egy adott alhálózatból egy Azure virtuális hálózaton belül, és blokkolja minden mást. A következő esetben ezt a funkciót fogjuk használni annak biztosítására, hogy egy App Service-példány csak egy adott Application Gateway-példányból érkező forgalmat fogadhat.

![Alkalmazásátjáró integrációja az App Service szolgáltatással](./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png)

Az App Service és az Application Gateway létrehozása mellett két részből áll a konfiguráció. Az első rész a szolgáltatásvégpontok engedélyezése a virtuális hálózat alhálózatában, ahol az alkalmazásátjáró telepítve van. A szolgáltatásvégpontok biztosítják, hogy az alhálózatot az App Service felé elhagyó összes hálózati forgalom az adott alhálózati azonosítóval lesz címkézve. A második rész az adott webalkalmazás hozzáférési korlátozásának beállítása annak érdekében, hogy csak az adott alhálózati azonosítóval címkézett forgalom engedélyezett legyen. Beállíthatja, hogy a különböző eszközök függően preferencia.

## <a name="using-azure-portal"></a>Az Azure Portal használata
Az Azure Portal segítségével négy lépést kell végrehajtania a beállítás kiépítéséhez és konfigurálásához. Ha rendelkezik meglévő erőforrásokkal, kihagyhatja az első lépéseket.
1. Alkalmazásszolgáltatás létrehozása az App Service dokumentációjában található rövid útmutatók egyikével, például [a .Net Core rövid útmutatóval](../../app-service/app-service-web-get-started-dotnet.md)
2. Hozzon létre egy alkalmazásátjárót a [portál rövid útmutatójával,](../../application-gateway/quick-create-portal.md)de hagyja ki a háttérrendszer-célok hozzáadása szakaszt.
3. Konfigurálja [az App Service-t háttérrendszerként az Application Gateway programban,](../../application-gateway/configure-web-app-portal.md)de hagyja ki a Hozzáférés korlátozása szakaszt.
4. Végül hozza létre a [hozzáférési korlátozást a szolgáltatás végpontjai használatával.](../../app-service/app-service-ip-restrictions.md#service-endpoints)

Most már elérheti az App Service-t az Application Gateway-en keresztül, de ha közvetlenül próbálja elérni az App Service-t, 403-as HTTP-hibaüzenetet kell kapnia, amely jelzi, hogy a webhely le van állítva.

![Alkalmazásátjáró integrációja az App Service szolgáltatással](./media/app-gateway-with-service-endpoints/web-site-stopped.png)

## <a name="using-azure-resource-manager-template"></a>Az Azure Resource Manager-sablonok használata
Az [Erőforrás-kezelő telepítési sablon][template-app-gateway-app-service-complete] egy teljes forgatókönyvet hoz ki. A forgatókönyv egy szolgáltatás-végállomásokkal és hozzáférési korlátozással zárolt App Service-példányból áll, amely csak az Application Gateway-től érkező forgalmat fogadja. A sablon számos intelligens alapértelmezett értéket és egyedi utójavítást tartalmaz, amelyeket az erőforrásnevekhez adtak hozzá, hogy egyszerű legyen. A felülírásukhoz klónoznia kell a tárta, vagy le kell töltenie a sablont, és szerkesztenie kell azt. 

A sablon alkalmazása a telepítés az Azure-ban gomb található a sablon leírásában, vagy használhatja a megfelelő PowerShell/CLI.

## <a name="using-azure-command-line-interface"></a>Az Azure parancssori felületének használata
Az [Azure CLI-minta](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) üzembe helyezési egy app-szolgáltatás le zárva a szolgáltatás végpontok és a hozzáférési korlátozás csak az Application Gateway-től érkező forgalom fogadására. Ha csak egy meglévő App Service-szolgáltatás forgalmát kell elkülönítenie egy meglévő Application Gateway-től, a következő parancs elegendő.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

Az alapértelmezett konfigurációban a parancs biztosítja mind a szolgáltatásvégpont konfigurációjának beállítását az alhálózaton, mind az App Service hozzáférési korlátozását.

## <a name="considerations-for-ilb-ase"></a>Az ILB ASE-vel kapcsolatos szempontok
Az ILB ASE nincs kitéve az internetnek, és a példány és az application gateway közötti forgalom ezért már elkülönített a virtuális hálózat. Az alábbi [útmutató konfigurálja](../environment/integrate-with-application-gateway.md) az ILB ASE-t, és integrálja azt egy Application Gateway-rel az Azure Portal használatával. 

Ha azt szeretné, hogy csak az Application Gateway alhálózatból érkező forgalom éri el az ASE-t, konfigurálhat egy hálózati biztonsági csoportot (NSG), amely az ASE összes webappsét érinti. Az NSG esetében megadhatja az alhálózati IP-tartományt és a portokat (80/443). Győződjön meg arról, hogy nem bírálja felül a [szükséges NSG-szabályokat](../environment/network-info.md#network-security-groups) az ASE megfelelő működéséhez.

Az egyes webalkalmazások forgalmának elkülönítéséhez ip-alapú hozzáférési korlátozásokat kell használnia, mivel a szolgáltatásvégpontok nem működnek az ASE számára. Az IP-cím nek az Application Gateway-példány privát IP-címének kell lennie.

## <a name="considerations-for-external-ase"></a>A külső ASE-vel kapcsolatos szempontok
A külső ASE egy nyilvános terheléselosztóval rendelkezik, például a több-bérlős App Service.External ASE has a public facing load balancer like multi-tenant App Service. A szolgáltatásvégpontok nem működnek az ASE számára, és ezért kell ip-alapú hozzáférési korlátozásokat használnia az Application Gateway-példány nyilvános IP-címhasználatával. Ha külső ASE-t szeretne létrehozni az Azure Portalon, kövesse ezt a [rövid útmutatót](../environment/create-external-ase.md)

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager-sablon a teljes forgatókönyvhöz"

## <a name="considerations-for-kuduscm-site"></a>A kudu/scm webhely szempontjai
Az scm webhely, más néven kudu, egy admin oldal, amely minden webalkalmazáshoz létezik. Nem lehet visszafordítani az scm-helyet, és valószínűleg azt is szeretné, hogy zárolja az egyes IP-címekre vagy egy adott alhálózatra.

Ha ugyanazokat a hozzáférési korlátozásokat szeretné használni, mint a főwebhely, a következő paranccsal örökölheti a beállításokat.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Ha egyéni hozzáférési korlátozásokat szeretne beállítani az scm-webhelyhez, hozzáférési korlátozásokat adhat hozzá az --scm-site jelző használatával, ahogy az alább látható.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>További lépések
Az App Service-környezetről további információt az [App Service-környezet dokumentációjában](https://docs.microsoft.com/azure/app-service/environment)talál.

A webalkalmazás további biztonsága érdekében az Application Gateway webalkalmazás-tűzfalával kapcsolatos információk az [Azure Web Application Firewall dokumentációjában](../../web-application-firewall/ag/ag-overview.md)találhatók.