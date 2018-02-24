---
title: "Az Azure Machine Learning modell kezelése parancssori felület referencia |} Microsoft Docs"
description: "Az Azure Machine Learning modell kezelése parancssori felület hivatkozás."
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: hjerez
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2017
ms.openlocfilehash: 219c61d1842369caadaf8e85dcb039242c37ef6c
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="model-management-command-line-interface-reference"></a>Modell felügyeleti parancssori felület referenciája

## <a name="base-cli-concepts"></a>Alap CLI fogalmak:

    account : Manage model management accounts. 
    env     : Manage compute environments.
    image   : Manage operationalization images.
    manifest: Manage operationalization manifests.
    model   : Manage operationalization models.
    service : Manage operationalized services.

## <a name="account-commands"></a>Fiók parancsok
A modell felügyeleti fiók a szolgáltatásokat, amelyek lehetővé teszik, telepítéséhez és kezeléséhez modellek használatához szükséges. Használjon `az ml account modelmanagement -h` megtekintéséhez a következő:

    create: Create a Model Management Account.
    delete: Delete a specified Model Management Account.
    list  : Gets the Model Management Accounts in the current subscriptiong.
    set   : Set the active Model Management Account.
    show  : Show a Model Management Account.
    update: Update an existing Model Management Account.

**Modell felügyeleti fiók létrehozása**

Hozzon létre egy modell felügyeleti fiókot a következő parancsot. Ez a fiók számlázási fog történni.

`az ml account modelmanagement create --location [Azure region e.g. eastus2] --name [new account name] --resource-group [resource group name to store the account in]`

Helyi argumentumai:

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

**A telepítési környezet beállítása**

A setup parancs szükséges hozzá közreműködői hozzáférése az előfizetéshez. Ha ezzel nem rendelkezik, akkor ahhoz az erőforráscsoporthoz szükséges Közreműködői hozzáférés, ahová a telepítést végzi. Utóbbi esetben meg kell adnia az erőforráscsoport nevét a telepítési parancs részeként a `-g` jelző használatával. 

Központi telepítés esetén két lehetőség áll rendelkezésre: *helyi* és *fürt*. Beállítás a `--cluster` (vagy `-c`) jelző lehetővé teszi, hogy a fürtöt tartalmazó környezetben, amely látja el az ACS-fürthöz. A telepítő alapvető szintaxisa a következő:

`az ml env setup [-c] --location [location of environment resources] --name[name of environment]`

Ez inicializálja az Azure gépi tanulási a környezet egy tárfiókot, ACR beállításjegyzék és App Insights szolgáltatás létrehozása az előfizetésben. Alapértelmezés szerint a környezet inicializálását helyi telepítés esetén csak (nincs ACS) Ha nem jelző van megadva. Ha a szolgáltatás méretezésére van szüksége, adja meg a `--cluster` (vagy `-c`) jelzőjét, hogy az ACS-fürtöt létrehozni.

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
    --resource-group -g            : Resource group in which to create compute resource. Will be created if it does not exist.
                                     If not provided, resource group will be created with 'rg' appended to 'name.'.
    --service-principal-app-id -a  : App ID of service principal to use for configuring ML compute.
    --service-principal-password -p: Password associated with service principal.
    --storage -s                   : ARM ID of storage account to associate with this environment.
    --yes -y                       : Flag to answer 'yes' to any prompts. Command will fail if user is not logged in.

Globális argumentumok
```
    --debug                        : Increase logging verbosity to show all debug logs.
    --help -h                      : Show this help message and exit.
    --output -o                    : Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query                        : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose                      : Increase logging verbosity. Use --debug for full debug logs.
```
## <a name="model-commands"></a>Modell parancsok

    list
    register
    show

A modell regisztrálása

A modell parancsot.

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

## <a name="manifest-commands"></a>Manifest parancsok

    create: Create an Operationalization Manifest. This command has two different
            sets of required arguments, depending on if you want to use previously registered
            model/s.
    list
    show

**Jegyzékfájl létrehozása**

A modell jegyzékfájlt hoz létre. 

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

Regisztrált modellhez argumentumok

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

**Lemezkép létrehozása**

A képfájl beállítással, a jegyzék előtt hozunk létre hozhat létre. 

`az ml image create -n [image name] --manifest-id [the manifest ID]`

Vagy a jegyzékfájl létrehozása, és egyetlen paranccsal lemezképet. 

`az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]`

Parancs részletei:

    --image-name -n [Required]: The name of the image being created.
    --image-description       : Description of the image.
    --image-type              : The image type to create. Defaults to "Docker".
    -v                        : Verbosity flag.

Regisztrált jegyzék argumentumok

    --manifest-id             : [Required] Id of previously registered manifest to use in image creation.

Nem regisztrált jegyzék argumentumok

    --conda-file -c           : Path to Conda Environment file.
    --dependency -d           : Files and directories required by the service. Multiple dependencies can
                                be specified with additional -d arguments.
    --model-file -m           : [Required] Model file to register.
    --schema-file -s          : Schema file to add to the manifest.
    -f                        : [Required] The code file to be deployed.
    -p                        : A pip requirements.txt file needed by the code file.
    -r                        : [Required] Runtime of the web service. Valid runtimes are python|spark-py.


## <a name="service-commands"></a>Szolgáltatás-parancsok
Az alábbi parancsokat a szolgáltatás esetében támogatottak. Minden parancs paramétereinek megtekintéséhez használja a -h lehetőséget. Tegyük fel például, `az ml service create realtime -h` megjelenítéséhez létrehozása parancs részleteinek megadása.

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

Szolgáltatás létrehozása a korábban létrehozott lemezképet, a következő paranccsal:

`az ml service create realtime --image-id [image to deploy] -n [service name]`

Szolgáltatás létrehozása jegyzékfájl és lemezképet, egyetlen parancs, a következő paranccsal:

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


Vegye figyelembe a a `-d` jelző kapcsolódó függőségek: Ha a nevét adja meg egy könyvtárat, amely még nincs kötegelt (zip, bont stb.), könyvtárhoz automatikusan tar'ed lekérdezi és a másik végpont nem kombinált jelszavat, majd automatikusan átadása. 

Ha már kötegelt könyvtár át, azt fájlként kezeli, és adja át mentén. Nem lesz átengedett automatikusan; az várhatóan kezelni, a kódban.

**Szolgáltatás-részletek**

A szolgáltatás részletes adatai, beleértve az URL-cím használata (mintaadatok Ha létrehozott egy séma) beolvasása.

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

Argumentumok--azonosító -i: [szükséges] elleni pontozása céljából felügyeletiszolgáltatás-azonosító.
-d: a webes szolgáltatás hívása a használni kívánt adatokat.
-v: részletességi jelzőt.

Globális argumentumok

    --debug    : Increase logging verbosity to show all debug logs.
    --help -h  : Show this help message and exit.
    --output -o: Output format.  Allowed values: json, jsonc, table, tsv. Default: json.
    --query    : JMESPath query string. See http://jmespath.org/ for more information and examples.
    --verbose  : Increase logging verbosity. Use --debug for full debug logs.
