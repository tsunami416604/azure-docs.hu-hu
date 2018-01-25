## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy egyszerű szolgáltatást a tároló beállításjegyzék eléréséhez, az alábbi parancsfájlt is használhat. Frissítés a `ACR_NAME` változó a tárolót a rendszerleíró adatbázis nevét, és szükség esetén a `--role` értéket a [az ad sp létrehozása-az-rbac] [ az-ad-sp-create-for-rbac] parancs különböző engedélyeket adni. Rendelkeznie kell a [Azure CLI](/cli/azure/install-azure-cli) telepítés után használja ezt a parancsfájlt.

A parancsfájl futtatása után tekintse meg a szolgáltatás egyszerű **azonosító** és **jelszó**. Miután a hitelesítő adatait, konfigurálhatja az alkalmazások és szolgáltatások, a tároló beállításjegyzék, mint az egyszerű szolgáltatás felé történő hitelesítésre.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>Egy meglévő egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést biztosít egy meglévő egyszerű szolgáltatást, egy új szerepkört kell rendelnie az egyszerű szolgáltatásnév. Csakúgy, mint egy új szolgáltatás létrehozása egyszerű, lekéréses leküldéses és lekéréses és tulajdonosi hozzáférés megadásához.

Az alábbi parancsfájl használ a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] megadását parancs *lekéréses* szolgáltatásnevet engedélyeket ad meg a `SERVICE_PRINCIPAL_ID` változó. Módosítsa a `--role` értéket, ha egy eltérő szintű hozzáférési biztosítani szeretné.

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create