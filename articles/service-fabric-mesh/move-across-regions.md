---
title: Service Fabric Mesh-alkalmazás áthelyezése egy másik régióba
description: Service Fabric Mesh-erőforrásokat áthelyezhet úgy, hogy az aktuális sablon egy példányát egy új Azure-régióba telepíti.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908162"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Service Fabric Mesh-alkalmazás áthelyezése egy másik Azure-régióba

Ez a cikk azt ismerteti, hogyan helyezheti át a Service Fabric Mesh-alkalmazást és annak erőforrásait egy másik Azure-régióba. Az erőforrásokat több okból is áthelyezheti egy másik régióba. Például az kimaradásokra adott válaszként az egyes régiókban elérhető funkciók vagy szolgáltatások megszerzéséhez, a belső házirend-és irányítási követelmények teljesítéséhez, vagy a kapacitás megtervezésének követelményeire adott válaszként.

 [Service Fabric Mesh nem támogatja](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) az erőforrások közvetlen áthelyezését az Azure-régiók között. Az erőforrásokat azonban közvetve áthelyezheti a jelenlegi Azure Resource Manager-sablon egy példányának az új célcsoportba való telepítésével, majd átirányíthatja a bejövő forgalmat és a függőségeket az újonnan létrehozott Service Fabric Mesh-alkalmazásba.

## <a name="prerequisites"></a>Előfeltételek

* A beáramlási vezérlő (például [Application Gateway](https://docs.microsoft.com/azure/application-gateway/)), hogy közvetítőként szolgáljon az ügyfelek és a Service Fabric Mesh alkalmazás közötti adatforgalom továbbításához
* Service Fabric Mesh (előzetes verzió) rendelkezésre állása a cél Azure-régióban (`westus`, `eastus`vagy `westeurope`)

## <a name="prepare"></a>Előkészületek

1. Készítse el a Service Fabric Mesh alkalmazás aktuális állapotának pillanatképét a Azure Resource Manager sablon és paraméterek exportálásával a legújabb telepítésből. Ehhez kövesse a [sablon exportálása a telepítés után](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) a Azure Portal használatával című témakör lépéseit. Használhatja az [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates)-t, a [Azure PowerShellt](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)és a [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)is.

2. Ha alkalmazható, [exportálja ugyanazon erőforráscsoport más erőforrásait](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) az újratelepítéshez a célként megadott régióban.

3. Tekintse át (és szükség esetén szerkessze) az exportált sablont annak biztosítására, hogy a meglévő tulajdonságértékek a megcélzott régióban használni kívánt értékek legyenek. Az új `location` (Azure-régió) egy paraméter, amelyet az újratelepítése során fog megadni.

## <a name="move"></a>Áthelyezés

1. Hozzon létre egy új erőforráscsoportot (vagy használjon egy meglévőt) a célként megadott régióban.

2. Az exportált sablonnal kövesse az [erőforrások telepítése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) a Azure Portal használatával című témakör lépéseit. Használhatja az [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli)-t, a [Azure PowerShellt](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)és a [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)is.

3. A kapcsolódó erőforrások, például az [Azure Storage-fiókok](../storage/common/storage-account-move.md)áthelyezésével kapcsolatos útmutatásért tekintse meg az [Azure-erőforrások régiókban való mozgatása](../azure-resource-manager/management/move-region.md)című témakörben felsorolt egyes szolgáltatások útmutatását.

## <a name="verify"></a>Ellenőrzés

1. Ha a telepítés befejeződött, tesztelje az alkalmazás-végpont (oka) t az alkalmazás működésének ellenőrzéséhez.

2. Az alkalmazás állapotát az [Azure Service Fabric Mesh](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)parancssori felület használatával ellenőrizheti az alkalmazás állapotának ([az Mesh app show](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) ellenőrzésével és az alkalmazás naplófájljainak és ([az Mesh Code-Package-log](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) parancsainak a megtekintésével is.

## <a name="commit"></a>Véglegesítés

Miután megerősítette a Service Fabric Mesh alkalmazás egyenértékű funkcióit a megcélzott régióban, konfigurálja a bejövő vezérlőt (például [Application Gateway](../application-gateway/redirect-overview.md)), hogy átirányítsa a forgalmat az új alkalmazásra.

## <a name="clean-up-source-resources"></a>Forrás erőforrásainak törlése

A Service Fabric Mesh alkalmazás áthelyezésének befejezéséhez [törölje a forrásoldali alkalmazást és/vagy a szülő erőforráscsoportot](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások áthelyezése régiók között](../azure-resource-manager/management/move-region.md)
* [Azure-erőforrások régiók közötti áthelyezésének támogatása](../azure-resource-manager/management/region-move-support.md)
* [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Művelet-támogatás áthelyezése az erőforrásokhoz](../azure-resource-manager/management/move-support-resources.md
)
