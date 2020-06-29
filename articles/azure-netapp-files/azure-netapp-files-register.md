---
title: Regisztráció a Azure NetApp Filesra | Microsoft Docs
description: Leírja, hogyan regisztrálhat a Azure NetApp Files használatára.
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
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: 6d47da361303a0c421da035fc47608ba363ff82f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483533"
---
# <a name="register-for-azure-netapp-files"></a>Regisztrálás az Azure NetApp Filesra

> [!IMPORTANT] 
> A Azure NetApp Files erőforrás-szolgáltató regisztrálása előtt meg kell kapnia egy e-mailt a Azure NetApp Files csapattól, amely megerősíti, hogy hozzáférést kapott a szolgáltatáshoz. 

Ebből a cikkből megtudhatja, hogyan regisztrálhat a Azure NetApp Files, hogy megkezdhesse a szolgáltatás használatát.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Várólista-kérelem küldése a szolgáltatás eléréséhez

1. A Azure NetApp Files szolgáltatás eléréséhez a [Azure NetApp Files várólista-beküldési lapon](https://aka.ms/azurenetappfiles)keresztül küldje el a várólista-kérést. 

    A várólista-regisztráció nem garantálja a szolgáltatás azonnali elérését. 

2. Várjon egy hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól, mielőtt folytatná a többi feladatot. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>A NetApp erőforrás-szolgáltató regisztrálása

A szolgáltatás használatához regisztrálnia kell az Azure erőforrás-szolgáltatót Azure NetApp Fileshoz.

> [!NOTE] 
> A NetApp erőforrás-szolgáltatót a szolgáltatáshoz való hozzáférés engedélyezése nélkül is sikeresen regisztrálhatja. A hozzáférés engedélyezése nélkül azonban a NetApp-fiókok vagy más Azure NetApp Files-erőforrások létrehozásához Azure Portal vagy API-kérelmek elutasítása a következő hibaüzenettel történik:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. A Azure Portal kattintson a jobb felső sarokban található Azure Cloud Shell ikonra:

      ![Azure Cloud Shell ikon](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Ha több előfizetéssel rendelkezik az Azure-fiókjában, válassza ki a Azure NetApp Files engedélyezési listán szereplőt:
    
        az account set --subscription <subscriptionId>

3. A Azure Cloud Shell-konzolon adja meg a következő parancsot az előfizetés engedélyezési listájának ellenőrzéséhez:
    
        az feature list | grep NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`az előfizetés-azonosítója.

    Ha nem látja a szolgáltatás nevét `Microsoft.NetApp/ANFGA` , nem férhet hozzá a szolgáltatáshoz. Leállítás ebben a lépésben. A folytatás előtt kövesse a várólista-hozzáférés kérése a [szolgáltatáshoz](#waitlist) való hozzáféréshez szolgáltatás eléréséhez című témakör utasításait. 

4. Az Azure Cloud Shell-konzolon adja meg a következő parancsot az Azure erőforrás-szolgáltató regisztrálásához: 
    
        az provider register --namespace Microsoft.NetApp --wait

   A `--wait` paraméter arra utasítja a konzolt, hogy várjon, amíg a regisztráció befejeződik. A regisztrációs folyamat hosszabb időt is igénybe vehet.

5. A Azure Cloud Shell-konzolon adja meg a következő parancsot az Azure-erőforrás-szolgáltató regisztrálásának ellenőrzéséhez: 
    
        az provider show --namespace Microsoft.NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`az előfizetés-azonosítója.  A `state` paraméter értéke a (z) értéket jelöli `Registered` .

6. A Azure Portal kattintson az **előfizetések** panelre.
7. Az előfizetések panelen kattintson az előfizetés-AZONOSÍTÓra. 
8. Az előfizetés beállításaiban kattintson az erőforrás- **szolgáltatók** elemre annak ellenőrzéséhez, hogy a Microsoft. NetApp szolgáltató a regisztrált állapotot jelzi-e: 

      ![Regisztrált Microsoft. NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>További lépések

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)
