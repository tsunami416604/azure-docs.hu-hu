---
title: Azure Stack Edge január 2021 Update Impact | Microsoft Docs
description: Ismerteti IoT Edge szerepkör-felügyelet hatását a Azure Stack Edge-eszközökön a január 2021 frissítés telepítését követően.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: 4b54f75b7d90e4b3a0a11d2ecdc676bb48eeee99
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335682"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge"></a>A Azure Stack Edge szerepkör-felügyeleti módosításai IoT Edge

Az Azure Stack Edge-eszköz szerepkör-kezelésének IoT Edge az API, az SDK és a Azure PowerShell legújabb verzióját használja a 2021 januári kiadás miatt. Ez a cikk részletesen ismerteti azokat a módosításokat, amelyek az API, az SDK és a PowerShell-parancsmagok legújabb verziójának használatakor szükségesek a Azure Stack Edge-ben való IoT Edge szerepkör-kezeléshez.

A januári 2021-es frissítés csak Azure Stack Edge Pro-GPU, Azure Stack Edge Pro R és Azure Stack Edge mini R-eszközök esetén érhető el, és a cikkben szereplő információk csak ezekre az eszközökre vonatkoznak. 

## <a name="iot-edge-role-management-changes"></a>Szerepkör-kezelés IoT Edge módosítása

Miután telepítette a választható január 2021 eszköz szoftverét az Azure Stack Edge-eszközön, az API, az SDK és a PowerShell-parancsmagok legújabb verzióját kell használnia IoT Edge szerepkör-kezeléshez.

- Ha az 2019-08-01-es verzióval rendelkező szerepkör-kezelési API-t használja, frissítsen az API-verzióra a 2021-es kiadásban. 
- Ha a szerepkör-kezelést SDK-s verziójú 1.0.0-on keresztül használja, frissítsen a verzióra a 2021 januárjában.
- Ha Azure PowerShell-parancsmagokkal (előzetes verzió) (például,,, vagy) szerepkör-kezelést használ `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` `Remove-AzStackEdgeRole` , meg kell várnia az új parancsmagokat, amelyek felszabadulnak a Feb 2021-ben.

A fenti módosítások csak akkor szükségesek, ha január 2021 frissítést alkalmaz. Ha továbbra is a szoftver meglévő verzióját szeretné megtartani, nincs hatással IoT Edge szerepkör-felügyeletre. Javasoljuk azonban, hogy frissítse az eszközt új funkciókkal és biztonsági fejlesztésekkel. 


## <a name="api-usage"></a>API-használat

Ha IoT Edge szerepkör-kezelést API-n keresztül hajtja végre, használja az új API-verziót (2020-12-01), amelyet később közzé fog tenni. Ha a jelenlegi szerepkör-API-t használja, és telepítette az eszköz közelgő verzióját, a Put, a GET, a DELETE Kubernetes szerepkört kell áthelyeznie, majd az IoT addon API-t.


### <a name="for-put-method"></a>PUT metódus esetén

#### <a name="current-http-request"></a>Jelenlegi HTTP-kérelem 

- Az API-hívások a következő URI-n történnek: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "

- A kérelem törzse a következőképpen néz ki:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="upcoming-http-request"></a>Közelgő HTTP-kérelem 

- A Kubernetes szerepkörhöz tartozó API-hívások a következő URI-n lesznek elvégezve: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01 "

    A kérelem törzse a következőképpen néz ki:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- Az IoT Edge addon API-hívásai a következő URI-n találhatók: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "


    A kérelem törzse a következőképpen néz ki:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-get-method"></a>GET metódus

#### <a name="current-http-response"></a>Aktuális http-válasz

- Az API-hívások a következő URI-val lesznek elvégezve: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "


- A válasz törzse a következőképpen néz ki:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="upcoming-http-response"></a>Közelgő http-válasz

- Az API-hívások a következő URI-val lesznek elvégezve: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "
- A válasz törzse a következőképpen néz ki: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-delete-method"></a>TÖRLÉSi metódus

### <a name="current"></a>Aktuális

Az API-hívások a következő URI-val lesznek elvégezve: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "

### <a name="upcoming"></a>Közelgő

Az API-hívások a következő URI-val lesznek elvégezve: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "


## <a name="sdk-usage"></a>SDK-használat

Ha az SDK-t használja, és telepítette a januári 2021-es eszköz frissítését, akkor az alábbi táblázatban látható módon meg kell változtatnia IoT Edge szerepkör beállításának módját. Ezután töltse le és telepítse a közelgő NuGet csomagot, hogy az új SDK-ra váltson az alábbi mintában látható módon.

### <a name="current-sdk-sample"></a>Aktuális SDK-minta

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="new-sdk-sample"></a>Új SDK-minta

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Parancsmag használata

Ha,, `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` vagy parancsmagot használ, `Remove-AzStackEdgeRole` meg kell várnia az új, a 2021 februárjában tervezett verziót.

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

#### <a name="i-am-using-azure-stack-edge-pro--fpga-does-the-january-2021-update-affect-the-fpga-model"></a>Azure Stack Edge Pro-FPGA használok. Befolyásolja a januári 2021-es frissítés a FPGA modellt?

Nem. A januári 2021-es frissítés csak Azure Stack Edge – GPU, Azure Stack Edge Pro R és Azure Stack Edge mini R-eszközökre vonatkozik. Ez a frissítés nem érinti a Azure Stack Edge Pro – FPGA szolgáltatást, és nem igényel semmilyen módosítást IoT Edge szerepkör-felügyelethez.

#### <a name="after-i-update-my-azure-stack-edge-pro---gpu-to-the-new-device-software-in-january-2021-are-any-of-the-existing-services-affected"></a>Miután Frissítettem a Azure Stack Edge Pro-GPU-t az új eszközre a 2021-es verzióban, a meglévő szolgáltatások bármelyike érintett?

Nem. A konfigurált szolgáltatások a januári 2021-es eszköz frissítésének telepítését követően nem lesznek hatással. <!--check w/ Anoob, what existing services you are talking about in this question-->

#### <a name="what-are-the-high-level-changes-to-iot-edge-management-api-sdk-or-cmdlet"></a>Mik a IoT Edge Management API, az SDK vagy a parancsmag magas szintű változásai?

IoT Edge a Kubernetes szerepkörhöz tartozó addon. Ez azt jelenti, hogy meg kell győződnie arról, hogy a Kubernetes először van konfigurálva, majd végrehajtja a IoT Edge konfigurációját.


## <a name="next-steps"></a>További lépések

- Útmutató a [frissítések alkalmazásához](azure-stack-edge-gpu-install-update.md).

