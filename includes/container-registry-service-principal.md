## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy egyszerű szolgáltatást a tároló beállításjegyzék eléréséhez, az alábbi parancsfájlt is használhat. Frissítés a `ACR_NAME` változó a tárolót a rendszerleíró adatbázis nevét, és szükség esetén a `--role` értéket a [az ad sp létrehozása-az-rbac] [ az-ad-sp-create-for-rbac] parancs különböző engedélyeket adni. Rendelkeznie kell a [Azure CLI](/cli/azure/install-azure-cli) telepítés után használja ezt a parancsfájlt.

A parancsfájl futtatása után tekintse meg a szolgáltatás egyszerű **azonosító** és **jelszó**. Miután a hitelesítő adatait, konfigurálhatja az alkalmazások és szolgáltatások, a tároló beállításjegyzék, mint az egyszerű szolgáltatás felé történő hitelesítésre.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Egy meglévő egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést biztosít egy meglévő egyszerű szolgáltatást, egy új szerepkört kell rendelnie az egyszerű szolgáltatásnév. Csakúgy, mint egy új szolgáltatás létrehozása egyszerű, lekéréses leküldéses és lekéréses és tulajdonosi hozzáférés megadásához.

Az alábbi parancsfájl használ a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] megadását parancs *lekéréses* szolgáltatásnevet engedélyeket ad meg a `SERVICE_PRINCIPAL_ID` változó. Módosítsa a `--role` értéket, ha egy eltérő szintű hozzáférési biztosítani szeretné.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

PowerShell parancsfájl mindkét esetben a Githubon, valamint a verzió található:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
