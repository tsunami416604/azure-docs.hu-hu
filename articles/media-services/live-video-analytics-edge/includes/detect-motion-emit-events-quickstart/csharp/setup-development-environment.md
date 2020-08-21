---
ms.openlocfilehash: ea30fed81a7f97b6577f41692274036d2714e0c1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690981"
---
1. A tárház klónozása erről a helyről: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. A Visual Studio Code-ban nyissa meg azt a mappát, ahol a tárház le lett töltve.
1. A Visual Studio Code-ban nyissa meg a *src/Cloud-to-Device-Console-app* mappát. Ott hozzon létre egy fájlt, és nevezze el *appsettings.js*. Ez a fájl a program futtatásához szükséges beállításokat fogja tartalmazni.
1. Másolja a tartalmat a *~/clouddrive/lva-sample/appsettings.jsról a* rövid útmutatóban korábban létrehozott fájlra.

    A szövegnek a következő kimenethez hasonlóan kell kinéznie.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Nyissa meg az *src/Edge* mappát, és hozzon létre egy *. env*nevű fájlt.
1. Másolja a */clouddrive/LVA-Sample/Edge-Deployment/.env* fájl tartalmát. A szövegnek a következő kódhoz hasonlóan kell kinéznie.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    