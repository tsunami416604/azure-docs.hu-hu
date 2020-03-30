---
title: Az ajánlat közzétételének automatizálása | Azure Piactér
description: A cikk a virtuálisgép-közzétételi munkafolyamat programozott automatizálását ismerteti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 25c7429dc369fb8fc70a135950b16c0a5997656b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280337"
---
<a name="automate-offer-publishing"></a>Ajánlatok közzétételének automatizálása
=========================

Azt is programozott módon automatizálhatja a virtuális gép közzétételi munkafolyamat, az [API-referencia](./cloud-partner-portal-api-overview.md) szakaszapi-k használatával. Az automatizálás tervezésekor két különböző forgatókönyvet kell figyelembe venni: a kezdeti közzétételt és az azt követő ajánlatközzétételt.


<a name="offer-initial-publishing"></a>Ajánlat kezdeti közzététele
-------------------------

Amikor első alkalommal tesz közzé egy ajánlatot, néhány további lépést igényel a piactérre való feltöltés előtt.  Például elő kell készítenie a metaadatokat, és létre kell hoznia egy ajánlattervezetet. A kezdeti közzétételi munkafolyamat az alábbi ábrán látható.

![A kezdeti ajánlat közzétételének interakciói](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

A következő mintakód bemutatja ezeket a lépéseket.

``` csharp
  CreateOfferAndPublish()
  {
      offer = CreateOfferObject()
      // Set all offer attributes
      offer.offerTypeId = "microsoft-azure-virtualmachines",
      // Create offer on server
     result = CloudPartnerPortal.Client.PutOffer(offer);
      if(result.status != success)
      {
        // Do failure actions here
      }
      // Publish created offer
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
        /*Queuing the publish failed, check error message and dofailure actions*/
        return failure;
      }
      // Return success as operation is successfully queued
      return success;
  }

  ValidateAndGoLive()    
  {
      // Confirm the version in preview slot is the version that needs to go live
      offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
      if(!offer[skuName].containsVersion(VMDisk.Version))
      {
          UpdateOfferAndPublish()
      }
      // Go Live on offer and check result
      Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
      if(op.HTTPStatus != OK)
      {
          // Queuing the publish failed, check error message and do failure actions
          return failure;
      }  
     
      // Return success as operation is successfully queued
      return success;
  }
```


<a name="subsequent-offer-publishing"></a>További ajánlat közzététele
---------------------------

Miután a virtuális gép (VM) ajánlat integrálva van egy folyamatos integrációs folyamatba, automatizálhatja a közzétételi munkafolyamat futtatásához minden alkalommal, amikor egy új virtuális merevlemez (VHD) jön létre.  Ezt a munkafolyamatot az alábbi ábra és mintakód szemlélteti.

![A későbbi ajánlati publikációk kölcsönhatásai](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

``` csharp
    UpdateOfferAndPublish()
    {
        // The routine wakes up after a build finishes. 
        // It determines if the current offer is undergoing publishing and take appropriate action
        // Wake up and get draft offer using offer name
        offer = CloudPartnerPortal.Client.GetOffer(offerName);
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
                // Update the list of VMs with new virtual machine. 
                // This assumes that less than 8 disks are present in the SKU
                // If 8 disks are already present, the client must remove one of the previous disks before adding new disk
                offer[skuName].addDisk(VMDisk)
                // Update the offer on server with new VMDisk
                CloudPartnerPortal.Client.PutOffer(offer);
        }
        // Publish offer and check result. Publish is idempotent, so if a publish was already in progress with latest version, this would return success.
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        
        // Return success as operation is successfully queued
        return success;
    }

    CheckLastOperationStatus()
    {
        Operation op = CloudPartnerPortal.Client.GetLastOpertation(offer.offerName)
        if(op.status == completed)
        {
            return success;
        }
        if(op.status == failure)
           // Do failure actions here
        
        else 
           // Last operation is still running
           // Go to sleep.           
    }

    ValidateAndGoLive()
    {
        // Confirm the version in preview slot is the version that needs to go live
        offer = CloudPartnerPortal.Client.GetOffer(offerName, "Preview");
        if(!offer[skuName].containsVersion(VMDisk.Version))
        {
            UpdateOfferAndPublish()
        }
        // Go Live on offer and check result
        Operation op = CloudPartnerPortal.Client.Publish(offer.offerName);
        if(op.HTTPStatus != OK)
        {
              // Queuing the publish failed, check error message and do failure actions
              return failure;
        }
        // Return success as operation is successfully queued
        return success;
    }
```
