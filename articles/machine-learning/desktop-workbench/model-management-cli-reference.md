---
title: Az Azure Machine Learning Modellkezelési parancssori felület leírása |} A Microsoft Docs
description: Az Azure Machine Learning Modellkezelési parancssori felület hivatkozás.
services: machine-learning
author: aashishb
ms.author: aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 9e69f2e71cce6d689669838785ce992fbbcfa940
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35646607"
---
# <a name="model-management-command-line-interface-reference"></a>Modell felügyeleti parancssori felületének dokumentációja

## <a name="base-cli-concepts"></a>Parancssori felület fogalmait alap:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Fiókregisztrálási parancsok
Egy modellkezelési fiókot kell használnia a szolgáltatások, amelyek üzembe helyezése és kezelése a modellek segítségével. Használat `az ml account modelmanagement -h` a következők megtekintéséhez:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Modellkezelési fiók létrehozása**

Hozzon létre egy modellkezelési fiókot a számlázási a következő paranccsal:

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Helyi argumentumok:

    --location -l       [Required]: Resource location.
    --name -n           [Required]: Name of the model management account.
    --resource-group -g [Required]: Resource group to create the model management account in.
    --description -d              : Description of the model management account.
    --sku-instances               : Number of instances of the selected SKU. Must be between 1 and
                                    16 inclusive.  Default: 1.
    --sku-name                    : SKU name. Valid names are S1|S2|S3|DevTest.  Default: S1.
    --tags -t                     : Tags for the model management account.  Default: {}.
    -v                            : Verbosity flag.

## <a name="environment-commands"></a>Környezet parancsok

    cluster        : Switch the current execution context to 'cluster'.
    delete         : Delete an MLCRP-provisioned resource.
    get-credentials: List the keys for an environment.
    list           : List all environments in the current subscription.
    local          : Switch the current execution context to 'local'.
    set            : Set the active MLC environment.
    setup          : Sets up an MLC environment.
    show           : Show an MLC resource; if resource_group or cluster_name are not provided, shows
                     the active MLC env.

**Az üzembe helyezési környezet beállítása**

A setup parancs használatához közreműködői hozzáférés az előfizetéshez. Ha ezzel nem rendelkezik, akkor ahhoz az erőforráscsoporthoz szükséges Közreműködői hozzáférés, ahová a telepítést végzi. Utóbbi esetben meg kell adnia az erőforráscsoport nevét a telepítési parancs részeként a `-g` jelző használatával. 

Telepítés két lehetőség van: *helyi* és *fürt*. Beállítás a `--cluster` (vagy `-c`) jelző lehetővé teszi, hogy a fürt üzembe helyezése kiosztja az ACS-fürt. Az alapszintű beállítás szintaxisa a következő:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Ez a parancs az Azure machine learning-környezet és a egy tárfiókot, az ACR-beállításjegyzékbe és az App Insights szolgáltatás az előfizetésben létrehozott inicializálja. Alapértelmezés szerint a környezet inicializálása helyi telepítés esetén csak (egyetlen ACS) Ha nem jelző van megadva. Ha a szolgáltatás skálázható van szüksége, adja meg a `--cluster` (vagy `-c`) jelző ACS-fürt létrehozásához.

Parancs részletei:

    --location -l        [Required]: Location for environment resources; an Azure region, e.g. eastus2.
    --name -n            [Required]: Name of environment to provision.
    --acr -r                       : ARM ID of ACR to associate with this environment.
    --agent-count -z               : Number of agents to provision in the ACS cluster. Default: 3.
    --cert-cname                   : CNAME of certificate.
    --cert-pem                     : Path to .pem file with certificate bytes.
    --cluster -c                   : Flag to provision ACS cluster. Off by default; specify this to force an ACS cluster deployment.
    --key-pem                      : Path to .pem file with certificate key.
    --master-count -m              : Number of master nodes to provision in the ACS cluster. Acceptable values: 1, 3, 5. Default: 1.
    --resource-group -g            : Resource group in which to create compute resource. Is created if it does not exist.
                                     If not provided, resource group is created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command fails if user is not logged in.

Globális argumentumok
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Modell-parancsok

    list
    register
    show

**Regisztrálja a modellt**

Regisztrálja a modellt parancsot.

`az ml model register --model [path to model file] --name [model name]`

Parancs részletei:

    --model -m [Required]: Model to register.
    --name -n  [Required]: Name of model to register.
    --description -d     : Description of the model.
    --tag -t             : Tags for the model. Multiple tags can be specified with additional -t arguments.
    -v                   : Verbosity flag.

Globális argumentumok

    --debug              : Increase logging verbosity to show all debug logs.
    --help -h            : Show this help message and exit.
    --output -o          : Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query              : JMESPath query string. See http://jmespath.org/ for more information and
                           examples.
    --verbose            : Increase logging verbosity. Use --debug for full debug logs.

## <a name="manifest-commands"></a>Jegyzékfájl parancsok

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Hozzon létre egy jegyzéket**

A következő parancs létrehoz egy jegyzékfájl a modellhez. 

`az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]`

Parancs részletei:

    --manifest-name -n [Required]: Name of the manifest to create.
    -f                 [Required]: The code file to be deployed.
    -r                 [Required]: Runtime of the web service. Valid runtimes are spark-py|python.
    --conda-file -c              : Path to Conda Environment file.
    --dependency -d              : Files and directories required by the service. Multiple
                                   dependencies can be specified with additional -d arguments.
    --manifest-description       : Description of the manifest.
    --schema-file -s             : Schema file to add to the manifest.
    -p                           : A pip requirements.txt file needed by the code file.
    -v                           : Verbosity flag.

Regisztrált modell argumentumok

    --model-id -i                : [Required] Id of previously registered model to add to manifest.
                                   Multiple models can be specified with additional -i arguments.

Nem regisztrált modell argumentumok

    --model-file -m              : [Required] Model file to register. If used, must be combined with
                                   model name.

Globális argumentumok

    --debug                      : Increase logging verbosity to show all debug logs.
    --help -h                    : Show this help message and exit.
    --output -o                  : Output format.  Allowed values: json, jsonc, table, tsv.
                                   Default: json.
    --query                      : JMESPath query string. See http://jmespath.org/ for more
                                   information and examples.
    --verbose                    : Increase logging verbosity. Use --debug for full debug logs.


## <a name="image-commands"></a>Lemezkép-parancsok

    create: Creates a docker image with the model and its dependencies. This command has two different sets of
            required arguments, depending on if you want to use a previously created manifest.
    list
    show
    usage

**Rendszerkép létrehozása**

Beállítással, miután létrehozta a jegyzékfájlt, mielőtt rendszerképet hozhat létre. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Vagy hozzon létre a jegyzéket, és egyetlen paranccsal kép. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Parancs részletei:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Regisztrált jegyzékfájl argumentumok

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Nem regisztrált jegyzékfájl argumentumok

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Szolgáltatás parancsaihoz
Az alábbi parancsokat a szolgáltatás használata támogatott. Az egyes parancsok a paraméterek megjelenítéséhez használja a -h lehetőséget. Például `az ml service create realtime -h` megtekintéséhez hozzon létre parancs részleteinek megadása.

    create
    delete
    keys
    list
    logs
    run
    show
    update
    usage

**Szolgáltatás létrehozása**

Szolgáltatás létrehozása a korábban létrehozott lemezképpel, használja a következő parancsot:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Szolgáltatás létrehozása a jegyzékfájlt és lemezképet, egyetlen paranccsal, a következő parancsot használja:

`az ml service create realtime --model-file [path to model file(s)] -f [path to model scoring file, e.g. score.py] -n [service name] -r [run time included in the image, e.g. spark-py]`

Parancsok részletei:

    -n                                : [Required] Webservice name.
    --autoscale-enabled               : Enable automatic scaling of service replicas based on request demand.
                                        Allowed values: true, false. False if omitted.  Default: false.
    --autoscale-max-replicas          : If autoscale is enabled - sets the maximum number of replicas.
    --autoscale-min-replicas          : If autoscale is enabled - sets the minimum number of replicas.
    --autoscale-refresh-period-seconds: If autoscale is enabled - the interval of evaluating scaling demand.
    --autoscale-target-utilization    : If autoscale is enabled - target utilization of replicas time.
    --collect-model-data              : Enable model data collection. Allowed values: true, false. False if omitted.  Default: false.
    --cpu                             : Reserved number of CPU cores per service replica (can be fraction).
    --enable-app-insights -l          : Enable app insights. Allowed values: true, false. False if omitted.  Default: false.
    --memory                          : Reserved amount of memory per service replica, in M or G. (ex. 1G, 300M).
    --replica-max-concurrent-requests : Maximum number of concurrent requests that can be routed to a service replica.
    -v                                : Verbosity flag.
    -z                                : Number of replicas for a Kubernetes service.  Default: 1.

Regisztrált kép argumentumok

    --image-id                        : [Required] Image to deploy to the service.

Nem regisztrált kép argumentumok

    --conda-file -c                   : Path to Conda Environment file.
    --image-type                      : The image type to create. Defaults to "Docker".
    --model-file -m                   : [Required] The model to be deployed.
    -d                                : Files and directories required by the service. Multiple dependencies can be specified
                                        with additional -d arguments.
    -f                                : [Required] The code file to be deployed.
    -p                                : A pip requirements.txt file of package needed by the code file.
    -r                                : [Required] Runtime of the web service. Valid runtimes are python|spark-py.
    -s                                : Input and output schema of the web service.

Globális argumentumok

    --debug                           : Increase logging verbosity to show all debug logs.
    --help -h                         : Show this help message and exit.
    --output -o                       : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                           : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                         : Increase logging verbosity. Use --debug for full debug logs.


Vegye figyelembe a a `-d` jelző csatolása függőségek: Ha a felhasználó nevét, amely még nem könyvtár kötegelve (zip, tar stb.), automatikusan tar'ed lekérdezi és átadása másik végének átengedett jelszavat, majd automatikusan könyvtárban. 

Ha egy könyvtárban, amely már részét képezi, a címtár-fájlként kezelni és továbbít, mert. Átengedett automatikusan; a kódban, hogy kezeljék várhatóan.

**Szolgáltatás részleteinek beolvasása**

Szolgáltatás adatait, például az URL-CÍMÉT, a használat (beleértve a mintaadatokat Ha sémát lett létrehozva) beolvasása.

`az ml service show realtime --name [service name]`

Parancs részletei:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globális argumentumok

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

**A szolgáltatás futtatásához**

`az ml service run realtime -n [service name] -d [input_data]`

Parancs részletei:

    --id -i    : The service id to show.
    --name -n  : Webservice name.
    -v         : Verbosity flag.

Globális argumentumok

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv.  Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.

Parancs

    az ml service run realtime

Argumenty--azonosító -i: [kötelező] a szolgáltatás azonosítója alapján pontszámot rendelni.
-d: a webszolgáltatás meghívására szolgáló használni kívánt adatokat.
-v: részletességi jelző.

Globális argumentumok

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
