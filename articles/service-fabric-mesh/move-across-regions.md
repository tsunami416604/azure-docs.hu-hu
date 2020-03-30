---
title: Service Fabric Mesh alkalmazás áthelyezése másik régióba
description: A Service Fabric Mesh-erőforrások at az aktuális sablon egy példányának üzembe helyezésével helyezheti át egy új Azure-régióba.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908162"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Service Fabric Mesh alkalmazás áthelyezése egy másik Azure-régióba

Ez a cikk ismerteti, hogyan helyezheti át a Service Fabric Mesh-alkalmazást és annak erőforrásait egy másik Azure-régióba. Előfordulhat, hogy az erőforrásokat több okból is áthelyezi egy másik régióba. Például a kimaradásokra adott válaszként, csak bizonyos régiókban elérhető funkciók vagy szolgáltatások megszerzésére, a belső szabályzati és irányítási követelmények teljesítésére, vagy a kapacitástervezési követelményekre reagálva.

 [A Service Fabric Mesh nem támogatja](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) az erőforrások közvetlen áthelyezését az Azure-régiókközött. Azonban közvetve áthelyezheti az erőforrásokat, ha telepíti az aktuális Azure Resource Manager-sablon egy példányát az új célrégióba, majd átirányítja a bejövő forgalom és a függőségek az újonnan létrehozott Service Fabric Mesh alkalmazás.

## <a name="prerequisites"></a>Előfeltételek

* A be- és a szolgáltatásháló-kiszolgáló alkalmazás közötti forgalom irányítására szolgáló infó (például [Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)közvetítőként szolgál az ügyfelek és a Service Fabric Mesh alkalmazás közötti forgalom irányításához
* A Service Fabric Mesh (előzetes verzió)`westus` `eastus`elérhetősége `westeurope`a cél Azure-régióban ( , vagy )

## <a name="prepare"></a>Előkészítés

1. Az Azure Resource Manager-sablon és a legutóbbi központi telepítés paramétereinek exportálásával "pillanatképet" készíthet a Service Fabric Mesh-alkalmazás aktuális állapotáról. Ehhez kövesse a sablon [exportálása az](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) Azure Portal használatával történő üzembe helyezés után leírt lépéseket. Használhatja [az Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Az Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)vagy [a REST API használatát](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)is.

2. Adott esetben [exportálja az ugyanazon erőforráscsoportba lévő egyéb erőforrásokat](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) a célrégióban történő átcsoportosításhoz.

3. Tekintse át (és szükség esetén szerkesztheti) az exportált sablont, hogy megbizonyosodjon arról, hogy a meglévő tulajdonságértékek azok, amelyeket a célrégióban használni szeretne. Az `location` új (Azure-régió) egy paraméter, amely et az újratelepítés során fog megadni.

## <a name="move"></a>Áthelyezés

1. Hozzon létre egy új erőforráscsoportot (vagy használjon egy meglévőt) a célrégióban.

2. Az exportált sablon, kövesse az [erőforrások üzembe helyezése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) az Azure Portalon. Használhatja [az Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [Az Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)vagy [a REST API használatát](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)is.

3. A kapcsolódó erőforrások, például [az Azure Storage-fiókok](../storage/common/storage-account-move.md)áthelyezésével kapcsolatos útmutatásért tekintse meg az [Azure-erőforrások régiók közötti áthelyezése](../azure-resource-manager/management/move-region.md)témakörben felsorolt egyes szolgáltatásokra vonatkozó útmutatást.

## <a name="verify"></a>Ellenőrzés

1. Amikor a központi telepítés befejeződött, tesztelje az alkalmazás végpont(oka)t az alkalmazás működésének ellenőrzéséhez.

2. Az alkalmazás állapotát is ellenőrizheti az alkalmazás állapotának[(az hálóalkalmazás megjelenítése)](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)ellenőrzésével, valamint az alkalmazásnaplók és ([az hálókód-csomagnapló](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) parancsainak áttekintésével az [Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)használatával.

## <a name="commit"></a>Véglegesítés

Miután megerősítette a Service Fabric Mesh alkalmazás egyenértékű funkcióját a célrégióban, konfigurálja a bejövő forgalom vezérlőjét (például [az Application Gateway-t)](../application-gateway/redirect-overview.md)az új alkalmazásra irányuló forgalom átirányításához.

## <a name="clean-up-source-resources"></a>Forrásforrások karbantartása

A Service Fabric Mesh alkalmazás áthelyezésének befejezéséhez [törölje a forrásalkalmazást és/vagy a szülő erőforráscsoportot.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>További lépések

* [Azure-erőforrások áthelyezése régiók között](../azure-resource-manager/management/move-region.md)
* [Az Azure-erőforrások régiók közötti áthelyezésének támogatása](../azure-resource-manager/management/region-move-support.md)
* [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás-áthelyezési műveletek támogatása](../azure-resource-manager/management/move-support-resources.md
)
