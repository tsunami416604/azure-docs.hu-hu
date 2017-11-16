# Áttekintés
## [Mi az a Resource Manager?](resource-group-overview.md)
## [Erőforrás-szolgáltatók és típusaik](resource-manager-supported-services.md)
## [A Resource Manager-alapú és a klasszikus üzemi modell](resource-manager-deployment-model.md)
## [Előfizetés-irányítás](resource-manager-subscription-governance.md)

# Bevezetés
## [Sablon létrehozása és üzembe helyezése](resource-manager-create-first-template.md)
## [VS Code-bővítmény sablonokhoz](resource-manager-vscode-extension.md)
## [A Visual Studio és a Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Példák
## [Kódminták](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [Sablon üzembe helyezése](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [Sablon üzembe helyezése](resource-manager-samples-cli-deploy.md)

# Útmutató
## Sablonok létrehozása
### [Sablon szakaszai](resource-group-authoring-templates.md)
### [Ajánlott eljárások a sablonokhoz](resource-manager-template-best-practices.md)
### [Hivatkozás más sablonokra](resource-group-linked-templates.md)
### [Függőség megadása két erőforrás között](resource-group-define-dependencies.md)
### [Több példány létrehozása](resource-group-create-multiple.md)
### [Hely beállítása](resource-manager-template-location.md)
### [Címkék hozzárendelése](resource-manager-template-tags.md)
### [Gyermekerőforrás nevének és típusának megadása](resource-manager-template-child-resource.md)
### [Erőforrás frissítése](resource-manager-update.md)
### [Objektumok használata a paraméterekhez](resource-manager-objects-as-parameters.md)
### [Állapot megosztása hivatkozott sablonok között](best-practices-resource-manager-state.md)
### [Minták sablonok tervezéséhez](best-practices-resource-manager-design-templates.md)


## Üzembe helyezés
### Azure PowerShell
#### [Sablon üzembe helyezése](resource-group-template-deploy.md)
#### [Sablon üzembe helyezése SAS-token használatával](resource-manager-powershell-sas-token.md)
#### [Sablon exportálása és újbóli üzembe helyezése](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Sablon üzembe helyezése](resource-group-template-deploy-cli.md)
#### [Sablon üzembe helyezése SAS-token használatával](resource-manager-cli-sas-token.md)
#### [Sablon exportálása és újbóli üzembe helyezése](resource-manager-export-template-cli.md)
### Azure Portal
#### [Erőforrások üzembe helyezése](resource-group-template-deploy-portal.md)
#### [Sablon exportálása](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [Erőforráscsoportok közötti üzembe helyezés](resource-manager-cross-resource-group-deployment.md)
### [Folyamatos integráció a Visual Studio Team Services szolgáltatással](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Biztonságos értékek továbbítása üzembe helyezés során](resource-manager-keyvault-parameter.md)

## Kezelés
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [Címkék használata az erőforrások rendszerezéséhez](resource-group-using-tags.md)
### [Erőforrások áthelyezése új csoportba vagy előfizetésbe](resource-group-move-resources.md)
### [Előfizetések rendszerezése felügyeleti csoportokkal](../billing/billing-enterprise-mgmt-group-overview.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Irányítási példák](resource-manager-subscription-examples.md)
### [Felügyelt alkalmazások](../managed-applications/overview.md)

## Vezérlési hozzáférés
### Egyszerű szolgáltatás létrehozása
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [Hitelesítési API az előfizetések hozzáféréséhez](resource-manager-api-authentication.md)
### [Erőforrások zárolása](resource-group-lock-resources.md)

## Naplózás
### [Tevékenységnaplók megtekintése](resource-group-audit.md)
### [Üzembe helyezési műveletek megtekintése](resource-manager-deployment-operations.md)

## Hibaelhárítás
### [Gyakori telepítési hibák](resource-manager-common-deployment-errors.md)
### [A telepítési hibák értelmezése](resource-manager-troubleshoot-tips.md)
### Hibák elhárítása
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### Virtuális gépek telepítési hibái
#### Linux
##### [Üzembe helyezési problémák](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [Kiépítéssel és foglalással kapcsolatos problémák](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [Gyakori hibaüzenetek](../virtual-machines/linux/error-messages.md)
#### Windows
##### [Üzembe helyezési problémák](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [Kiépítéssel és foglalással kapcsolatos problémák](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [Gyakori hibaüzenetek](../virtual-machines/windows/error-messages.md)

# Referencia
## [Sablon formátuma](/azure/templates/)
## [Sablonfüggvények](resource-group-template-functions.md)
### [Tömb- és objektumfüggvények](resource-group-template-functions-array.md)
### [Összehasonlító függvények](resource-group-template-functions-comparison.md)
### [Üzembehelyezési függvények](resource-group-template-functions-deployment.md)
### [Logikai függvények](resource-group-template-functions-logical.md)
### [Numerikus függvények](resource-group-template-functions-numeric.md)
### [Erőforrásfüggvények](resource-group-template-functions-resource.md)
### [Karakterlánc-függvények](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Erőforrások
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Kérelmek szabályozása](resource-manager-request-limits.md)
## [Aszinkron műveletek követése](resource-manager-async-operations.md)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
