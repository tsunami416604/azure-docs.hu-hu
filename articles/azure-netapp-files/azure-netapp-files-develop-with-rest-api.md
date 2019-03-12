---
title: Fejlesztés az Azure NetApp fájlok REST API-val |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure NetApp Files REST API használatának első lépései.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: b-juche
ms.openlocfilehash: b67f7a613cd72e7a69b40741d971382276a38334
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768059"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Fejlesztés az Azure NetApp fájlok REST API-val 

Az Azure Files-NetApp szolgáltatás REST API HTTP-erőforrásokkal kapcsolatos művelet például a NetApp fiók, a kapacitás-készlet, kötetek és a pillanatképek határozza meg. Ez a cikk segít az Azure NetApp fájlok REST API használatának első lépései.

## <a name="access-the-azure-netapp-files-rest-api"></a>Az Azure NetApp fájlok REST API eléréséhez  

1. [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Ha már tette.
2. Egyszerű szolgáltatás létrehozása az Azure Active Directoryban (Azure AD):
    1. Ellenőrizze, hogy [megfelelő engedélyekkel](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    1. Adja meg az Azure CLI az alábbi parancsot:  

            az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

    A parancs kimenete a következő példához hasonlít:  

            { 
                "appId": "appIDgoeshere", 
                "displayName": "APPNAME", 
                "name": "http://APPNAME", 
                "password": "supersecretpassword", 
                "tenant": "tenantIDgoeshere" 
            } 

    Tartsa meg a parancs kimenete.  Szüksége lesz a `appId`, `password`, és `tenant` értékeket. 

3. OAuth-hozzáférési token-kérelem:

    A példák ebben a cikkben a cURL használatával.  Például különböző API-eszközöket is használható [Postman](https://www.getpostman.com/), [alvó üzemmódra képtelen számítógépek](https://insomnia.rest/), és [Paw](https://paw.cloud/).  

    A változók a következő példában cserélje le a parancs kimenete a fenti 2. lépés. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    A kimenet tartalmazza egy hozzáférési tokent az alábbi példához hasonló:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    A megjelenített jogkivonatot a érvényes 3600 másodperc. Ezt követően meg kell kérnie egy új jogkivonatot. 
    Mentse a token egy szövegszerkesztőbe.  Szüksége lesz rá a következő lépéshez.

4. Küldhet teszt hívást, és a jogkivonatot érvényesíteni a hozzáférést a REST API-nak a következők:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Példák az API-val  

Ez a cikk a következő URL-címet használja a kérések az alapterv esetében. Az URL-címet az Azure Files-NetApp névtér a legfelső szintű mutat. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Kell cserélni a `subID` és `resourceGroups` értékek a következő példákban a saját értékeire. 

### <a name="get-request-examples"></a>Kérelem-példák

Egy GET kérelem lekérdezési objektumok Azure NetApp fájlok található egy előfizetésben, használja az alábbi példák mutatják: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>A PUT kérés példák

Egy PUT kérelmet használatával új objektumokat hozhat létre Azure NetApp fájlok, mint az alábbi példák mutatják. A PUT kérés törzsében hozzáadhatja a módosítások formázott JSON-adatokat, vagy azt adhatja meg a fájl olvasásához. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>JSON-példák

Az alábbi példa bemutatja, hogyan hozhat létre a NetApp fiókot:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

Az alábbi példa bemutatja, hogyan hoz létre egy kapacitás-készletet: 

    {
        "name": "MYNETAPPACCOUNT/POOLNAME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
        "location": "westus2",
        "properties": {
            "name": "POOLNAME"
            "size": "4398046511104",
            "serviceLevel": "Premium"
        }
    }

Az alábbi példa bemutatja, hogyan hozhat létre egy új kötet: 

    {
        "name": "MYNEWVOLUME",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
        "location": "westus2",
        "properties": {
            "serviceLevel": "Premium",
            "usageThreshold": "322122547200",
            "creationToken": "MY-FILEPATH",
            "snapshotId": "",
            "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
            }
    }

Az alábbi példa bemutatja, hogyan hozhat létre pillanatképet egy kötet: 

    {
        "name": "apitest2/apiPool01/apiVol01/snap02",
        "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
        "location": "westus2",
        "properties": {
            "name": "snap02",
            "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
        }
    }

> [!NOTE] 
> Meg kell adnia `fileSystemId` egy pillanatkép létrehozásához.  Szerezheti be a `fileSystemId` érték egy GET kéréssel kötetre. 

## <a name="next-steps"></a>További lépések

[Tekintse meg az Azure NetApp fájlok REST API-referencia](https://docs.microsoft.com/rest/api/netapp/)
