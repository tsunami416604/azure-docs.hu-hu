---
title: Fejlesztés Az Azure NetApp-fájlokhoz REST API-val | Microsoft dokumentumok
description: Az Azure NetApp Files REST API használatának első lépéseit ismerteti.
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
ms.date: 05/17/2019
ms.author: b-juche
ms.openlocfilehash: 996fbcc7c3c9af0da9160216785ecd54840660e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65957040"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Fejlesztés az Azure NetApp-fájlokhoz REST API-val 

Az Azure NetApp Files szolgáltatás REST API-ja http-műveleteket határoz meg olyan erőforrásokkal szemben, mint például a NetApp-fiók, a kapacitáskészlet, a kötetek és a pillanatképek. Ez a cikk segítséget nyújt az Azure NetApp Files REST API használatának megkezdéséhez.

## <a name="azure-netapp-files-rest-api-specification"></a>Az Azure NetApp Files REST API specifikációja

Az Azure NetApp Files REST API-specifikációja a [GitHubon](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)keresztül jelenik meg:

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Az Azure NetApp Files REST API elérése  

1. [Telepítse az Azure CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ha még nem tette meg.
2. Egyszerű szolgáltatás létrehozása az Azure Active Directoryban (Azure AD):
   1. Ellenőrizze, hogy rendelkezik-e [a megfelelő engedélyekkel.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)

   1. Írja be a következő parancsot az Azure CLI-be:  

           az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE--password $YOURGENERATEDPASSWORDGOESHERE

      A parancs kimenete hasonló a következő példához:  

           { 
               "appId": "appIDgoeshere", 
               "displayName": "APPNAME", 
               "name": "http://APPNAME", 
               "password": "supersecretpassword", 
               "tenant": "tenantIDgoeshere" 
           } 

      Tartsa meg a parancs kimenetét.  Szüksége lesz `appId`a `password`, `tenant` és értékekre. 

3. OAuth-hozzáférési jogkivonat kérése:

    A cikkben szereplő példák cURL-t használnak.  Különböző API-eszközöket is használhat, például [postás,](https://www.getpostman.com/) [álmatlanság](https://insomnia.rest/)és [mancs.](https://paw.cloud/)  

    Cserélje le a következő példában található változókat a fenti 2. 

        curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token

    A kimenet a következő példához hasonló hozzáférési jogkivonatot biztosít:

        eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9

    A megjelenített token 3600 másodpercig érvényes. Ezt követően új jogkivonatot kell kérnie. 
    Mentse a tokent egy szövegszerkesztőbe.  Szükséged lesz rá a következő lépéshez.

4. Küldjön egy teszthívást, és adja meg a jogkivonatot a REST API-hoz való hozzáférés ellenőrzéséhez:

        curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2016-08-01

## <a name="examples-using-the-api"></a>Példák az API használatával  

Ez a cikk a következő URL-címet használja a kérelmek alapkonfigurációjának. Ez az URL-cím az Azure NetApp-fájlok névtér gyökerére mutat. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15`

Az alábbi `subID` példákban lévő értékeket `resourceGroups` a saját értékeivel kell lecserélni. 

### <a name="get-request-examples"></a>Példák get kérés

Get-kérelem segítségével lekérdezheti az Azure NetApp-fájlok objektumait egy előfizetésben, amint azt a következő példák mutatják: 

        #get NetApp accounts 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2017-08-15

        #get capacity pools for NetApp account 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2017-08-15

        #get volumes in NetApp account & capacity pool 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2017-08-15

        #get snapshots for a volume 
        curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2017-08-15

### <a name="put-request-examples"></a>Példák a PUT-kérelemre

A PUT-kérelem segítségével új objektumokat hozhat létre az Azure NetApp-fájlokban, ahogy azt az alábbi példák mutatják. A PUT-kérelem törzse tartalmazhat a módosítások JSON-formátumú adatait, vagy megadhat egy fájlt, amelyből olvasni szeretne. 

        #create a NetApp account  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2017-08-15

        #create a capacity pool  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2017-08-15

        #create a volume  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2017-08-15

        #create a volume snapshot  
        curl -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2017-08-15

### <a name="json-examples"></a>Példák json-példák

A következő példa bemutatja, hogyan hozhat létre NetApp-fiókot:

    { 
        "name": "MYNETAPPACCOUNT", 
        "type": "Microsoft.NetApp/netAppAccounts", 
        "location": "westus2", 
        "properties": { 
            "name": "MYNETAPPACCOUNT" 
        }
    } 

A következő példa bemutatja, hogyan hozhat létre kapacitáskészletet: 

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

A következő példa bemutatja, hogyan hozhat létre új kötetet: 

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

A következő példa bemutatja, hogyan hozhat létre pillanatképet egy kötetről: 

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
> Meg kell `fileSystemId` adnia a pillanatkép létrehozásához.  Az értéket `fileSystemId` get kéréssel kaphatja meg egy kötethez. 

## <a name="next-steps"></a>További lépések

[Az Azure NetApp Files REST API-hivatkozásának megtekintése](https://docs.microsoft.com/rest/api/netapp/)
