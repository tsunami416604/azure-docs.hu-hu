---
title: Regisztráció az Azure NetApp-fájlokra | Microsoft dokumentumok
description: Az Azure NetApp-fájlok használatának regisztrálása.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274059"
---
# <a name="register-for-azure-netapp-files"></a>Regisztrálás az Azure NetApp Filesra

> [!IMPORTANT] 
> Az Azure NetApp Files erőforrás-szolgáltató regisztrálása előtt e-mailt kell kapnia az Azure NetApp Files csapatától, amely megerősíti, hogy hozzáférést kapott a szolgáltatáshoz. 

Ebből a cikkből megtudhatja, hogyan regisztrálhat az Azure NetApp-fájlokra, hogy megkezdhesse a szolgáltatás használatát.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Várólistára várólista-kérelem küldése a szolgáltatás eléréséhez

1. Várólistás kérelem küldése az Azure NetApp Files szolgáltatás eléréséhez az [Azure NetApp Files várólisták beküldési lapján](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u)keresztül. 

    A várólistára való feliratkozás nem garantálja az azonnali szolgáltatáshozzáférést. 

2. Várjon az Azure NetApp Files csapatának hivatalos visszaigazoló e-mailre, mielőtt folytatná a többi feladatot. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>A NetApp erőforrás-szolgáltató regisztrálása

A szolgáltatás használatához regisztrálnia kell az Azure-erőforrás-szolgáltatót az Azure NetApp-fájlokhoz.

> [!NOTE] 
> A NetApp erőforrás-szolgáltatót még akkor is sikeresen regisztrálhatja anélkül, hogy hozzáférést kapna a szolgáltatáshoz. Hozzáférés-engedélyezés nélkül azonban a következő hibával elutasítunk minden Olyan Azure Portal- vagy API-kérelmet, amely NetApp-fiók vagy más Azure NetApp Files-erőforrás létrehozására irányuló kérelmet tartalmaz:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Az Azure Portalon kattintson az Azure Cloud Shell ikonra a jobb felső sarokban:

      ![Az Azure Cloud Shell ikonja](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Ha több előfizetése van az Azure-fiókjában, válassza ki azt, amelyik az Azure NetApp-fájlok hoz lett a listára:
    
        az account set --subscription <subscriptionId>

3. Az Azure Cloud Shell konzolon adja meg a következő parancsot annak ellenőrzéséhez, hogy az előfizetés szerepel-e az engedélyezési listán:
    
        az feature list | grep NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>`az előfizetésazonosítója.

    Ha nem látja a `Microsoft.NetApp/ANFGA`szolgáltatás nevét, akkor nem fér hozzá a szolgáltatáshoz. Állj meg ennél a lépésnél. A folytatás előtt kövesse [a Várólistára várólista-kérelem beküldése](#waitlist) a szolgáltatáshoz való hozzáférésre vonatkozó utasításokat. 

4. Az Azure Cloud Shell konzolon adja meg a következő parancsot az Azure Resource Provider regisztrálásához: 
    
        az provider register --namespace Microsoft.NetApp --wait

   A `--wait` paraméter arra utasítja a konzolt, hogy várja meg a regisztráció befejezését. A regisztrációs folyamat eltarthat egy ideig.

5. Az Azure Cloud Shell konzolon adja meg a következő parancsot annak ellenőrzéséhez, hogy az Azure Resource Provider regisztrálva van-e: 
    
        az provider show --namespace Microsoft.NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>`az előfizetésazonosítója.  A `state` paraméter értéke `Registered`jelzi .

6. Az Azure Portalon kattintson az **Előfizetések** panelre.
7. Az Előfizetések panelen kattintson az előfizetés-azonosítóra. 
8. Az előfizetés beállításaiban kattintson az **Erőforrás-szolgáltatók** elemre annak ellenőrzéséhez, hogy a Microsoft.NetApp Provider jelzi-e a Regisztrált állapotot: 

      ![Regisztrált Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>További lépések

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)