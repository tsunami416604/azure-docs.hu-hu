# Áttekintés

## [Mi az a Resource Manager?](resource-group-overview.md)

## [Erőforrás-szolgáltatók és típusaik](resource-manager-supported-services.md)

## [A Resource Manager-alapú és a klasszikus üzemi modell](resource-manager-deployment-model.md)

## [Előfizetés-irányítás](resource-manager-subscription-governance.md)

## [Felügyelt alkalmazások](managed-application-overview.md)


# Bevezetés

## [Sablon exportálása](resource-manager-export-template.md)

## [Az első saját sablon létrehozása](resource-manager-create-first-template.md)

## [A Visual Studio és a Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)


# Példák

## PowerShell

### [Sablon üzembe helyezése](resource-manager-samples-powershell-deploy.md)


## Azure CLI

### [Sablon üzembe helyezése](resource-manager-samples-cli-deploy.md)


# Útmutató

## Sablonok létrehozása

### [Ajánlott eljárások a sablonokhoz](resource-manager-template-best-practices.md)

### [Sablon szakaszai](resource-group-authoring-templates.md)

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

### PowerShell

#### [Sablon üzembe helyezése](resource-group-template-deploy.md)

#### [Sablon üzembe helyezése SAS-token használatával](resource-manager-powershell-sas-token.md)

#### [Sablon exportálása és újbóli üzembe helyezése](resource-manager-export-template-powershell.md)

### Azure CLI

#### [Sablon üzembe helyezése](resource-group-template-deploy-cli.md)

#### [Sablon üzembe helyezése SAS-token használatával](resource-manager-cli-sas-token.md)

#### [Sablon exportálása és újbóli üzembe helyezése](resource-manager-export-template-cli.md)

### [Portal](resource-group-template-deploy-portal.md)

### [REST API](resource-group-template-deploy-rest.md)

### [Erőforráscsoportok közötti üzembe helyezés](resource-manager-cross-resource-group-deployment.md)

### [Folyamatos integráció a Visual Studio Team Services szolgáltatással](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

### [Biztonságos értékek továbbítása üzembe helyezés során](resource-manager-keyvault-parameter.md)


## Kezelés

### [PowerShell](powershell-azure-resource-manager.md)

### [Azure CLI](xplat-cli-azure-resource-manager.md)

### [Portál](resource-group-portal.md)

### [REST API](resource-manager-rest-api.md)

### [Címkék használata az erőforrások rendszerezéséhez](resource-group-using-tags.md)

### [Erőforrások áthelyezése új csoportba vagy előfizetésbe](resource-group-move-resources.md)

### [Irányítási példák](resource-manager-subscription-examples.md)


## Vezérlési hozzáférés

### Egyszerű szolgáltatás létrehozása

#### [PowerShell](resource-group-authenticate-service-principal.md)

#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)

#### [Portal](resource-group-create-service-principal-portal.md)

### [Hitelesítési API az előfizetések hozzáféréséhez](resource-manager-api-authentication.md)

### [Erőforrások zárolása](resource-group-lock-resources.md)


## Erőforrás-házirendek beállítása

### [Mik azok az erőforrás-házirendek?](resource-manager-policy.md)

### [Szabályzat hozzárendelése a portál használatával](resource-manager-policy-portal.md)

### [Szabályzat hozzárendelése szkriptek használatával](resource-manager-policy-create-assign.md)

### Példák

#### [Címkék](resource-manager-policy-tags.md)

#### [Elnevezési konvenciók](resource-manager-policy-naming-convention.md)

#### [Hálózat](resource-manager-policy-network.md)

#### [Storage](resource-manager-policy-storage.md)

#### [Linux rendszerű virtuális gép](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

#### [Windows rendszerű virtuális gép](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)


## Felügyelt alkalmazások használata

### [Szolgáltatáskatalógusban elérhető alkalmazás közzététele](managed-application-publishing.md)

### [Szolgáltatáskatalógusban elérhető alkalmazás felhasználása](managed-application-consumption.md)

### [Piactéren elérhető alkalmazás közzététele](managed-application-author-marketplace.md)

### [Piactéren elérhető alkalmazás felhasználása](managed-application-consume-marketplace.md)

### [Felhasználóifelület-definíciók létrehozása](managed-application-createuidefinition-overview.md)


## Naplózás

### [Tevékenységnaplók megtekintése](resource-group-audit.md)

### [Üzembe helyezési műveletek megtekintése](resource-manager-deployment-operations.md)


## Hibaelhárítás

### [Gyakori telepítési hibák](resource-manager-common-deployment-errors.md)

### [RequestDisallowedByPolicy hiba](resource-manager-policy-requestdisallowedbypolicy-error.md)


# Referencia

## [Sablon formátuma](/azure/templates/)

## [Sablonfüggvények](resource-group-template-functions.md)

### [Tömb- és objektumfüggvények](resource-group-template-functions-array.md)

### [Összehasonlító függvények](resource-group-template-functions-comparison.md)

### [Üzembehelyezési függvények](resource-group-template-functions-deployment.md)

### [Numerikus függvények](resource-group-template-functions-numeric.md)

### [Erőforrásfüggvények](resource-group-template-functions-resource.md)

### [Karakterlánc-függvények](resource-group-template-functions-string.md)

## [Felhasználói felületet definiáló függvények](managed-application-createuidefinition-functions.md)

## [Felhasználói felületet definiáló elemek](managed-application-createuidefinition-elements.md)

### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)

### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)

### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)

### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)

### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)

### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)

### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)

### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)

### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)

### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)

### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)

### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)

### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)

## [PowerShell](/powershell/module/azurerm.resources)

## [Azure CLI](/cli/azure/resource)

## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)

## [Java](/java/api/com.microsoft.azure.management.resources)

## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)

## [REST](/rest/api/resources/)


# Erőforrások

## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)

## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=azure-resource-manager)

## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)

## [Kérelmek szabályozása](resource-manager-request-limits.md)

## [Aszinkron műveletek követése](resource-manager-async-operations.md)

## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)

