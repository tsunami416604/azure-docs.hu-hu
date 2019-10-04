---
title: Regisztráljon a Azure NetApp-fájlok |} A Microsoft Docs
description: Regisztráljon, és az Azure Files-NetApp használatának módját ismerteti.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299217"
---
# <a name="register-for-azure-netapp-files"></a>Regisztrálás az Azure NetApp Filesra

> [!IMPORTANT] 
> Mielőtt regisztrálná az Azure Files-NetApp erőforrás-szolgáltató, kell kapott e-mailt az Azure NetApp fájlok csapat megerősíti, hogy rendelkezik a szolgáltatáshoz való hozzáférést. 

Ebből a cikkből megtudhatja, hogyan regisztrálhat Azure NetApp fájlokat, hogy a szolgáltatás használatának megkezdése.

## <a name="waitlist"></a>A szolgáltatás eléréséhez nagyságától kérelem küldése

1. Igényelnie nagyságától Azure NetApp Files szolgáltatáson keresztül fér hozzá a [Azure NetApp fájlok nagyságától alkalmazásbeküldési oldalára](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Nagyságától regisztráció nem garantálja a közvetlen elérése. 

2. Egyéb feladatok folytatása előtt várja meg, a NetApp fájlokat az Azure-csapat hivatalos megerősítő e-mail. 

## <a name="resource-provider"></a>A NetApp erőforrás-szolgáltató regisztrálása

A szolgáltatás használatához regisztrálnia kell az Azure erőforrás-szolgáltató Azure NetApp fájlok.

> [!NOTE] 
> Sikerült regisztrálni a NetApp erőforrás-szolgáltató szolgáltatás hozzáférési engedély megadása nélkül is lehet. Ugyanakkor anélkül, hogy hozzáférési engedélyt, bármely Azure Portalon vagy a NetApp fiók vagy bármely más NetApp fájlokat az Azure-erőforrás létrehozása API-kérelem a rendszer elutasítja a következő hiba miatt:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Az Azure Portalon kattintson az Azure Cloud Shell ikonra a jobb felső sarokban található:

      ![Az Azure Cloud Shell ikon](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Ha több előfizetéssel rendelkezik az Azure-fiókjával, válassza ki azt, amelyik már szerepel az engedélyezési listán NetApp Azure-fájlok:
    
        az account set --subscription <subscriptionId>

3. Az Azure Cloud Shell-konzolon adja meg annak ellenőrzésére, hogy az előfizetés lett szerepel az engedélyezési listán a következő parancsot:
    
        az feature list | grep NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` az előfizetés-azonosítója.

    Ha nem látja a szolgáltatásnév `Microsoft.NetApp/ANFGA`, nem rendelkezik a szolgáltatáshoz való hozzáférést. Ebben a lépésben állni. Kövesse az utasításokat [igényelnie nagyságától a szolgáltatás elérésével](#waitlist) szolgáltatás hozzáférés kéréséhez a folytatás előtt. 

4. Az Azure Cloud Shell-konzolon adja meg az Azure erőforrás-szolgáltató regisztrálásához a következő parancsot: 
    
        az provider register --namespace Microsoft.NetApp --wait

   A `--wait` paraméter arra utasítja a konzolt, hogy Várjon, amíg a regisztráció befejezéséhez. A regisztrációs folyamat befejezése hosszabb időt is igénybe vehet.

5. Az Azure Cloud Shell-konzolon adja meg, győződjön meg arról, hogy az Azure erőforrás-szolgáltató regisztrálva van-e a következő parancsot: 
    
        az provider show --namespace Microsoft.NetApp

   A parancs kimenete a következőképpen jelenik meg:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` az előfizetés-azonosítója.  A `state` paraméter értéke azt jelzi, hogy `Registered`.

6. Az Azure Portalon kattintson a **előfizetések** panelen.
7. Az előfizetések panelen kattintson az előfizetési azonosítóját. 
8. Kattintson a beállítások az előfizetés **erőforrás-szolgáltatók** , győződjön meg arról, hogy Microsoft.NetApp szolgáltató azt jelzi, hogy a regisztrált állapota: 

      ![Registered Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>További lépések

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)