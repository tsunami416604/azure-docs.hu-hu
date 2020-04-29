---
title: Ajánlat-közzététel automatizálása | Azure piactér
description: Ismerteti, hogyan lehet programozott módon automatizálni a virtuális gépek közzétételi munkafolyamatát.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 25c7429dc369fb8fc70a135950b16c0a5997656b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280337"
---
<a name="automate-offer-publishing"></a>Ajánlatok közzétételének automatizálása
=========================

A virtuális gép közzétételi munkafolyamatát programozott módon is automatizálhatja az API-k használatával az [API-referenciák](./cloud-partner-portal-api-overview.md) szakaszban. Az automatizálás megtervezése során két különböző forgatókönyvnek kell megfontolnia: az ajánlat kezdeti közzététele és a további ajánlatok közzététele.


<a name="offer-initial-publishing"></a>Ajánlat kezdeti közzététele
-------------------------

Amikor első alkalommal tesz közzé egy ajánlatot, néhány további lépést is igényel a piactérre való feltöltés előtt.  Elő kell készíteni például a metaadatokat, és létre kell hoznia egy ajánlat tervezetét. A kezdeti közzétételi munkafolyamat a következő ábrán látható.

![A kezdeti ajánlat közzétételének interakciói](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

Az alábbi mintakód ezeket a lépéseket mutatja be.

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


<a name="subsequent-offer-publishing"></a>További ajánlatok közzététele
---------------------------

Miután a virtuális gép (VM) ajánlata integrálva van egy folyamatos integrációs folyamatba, automatizálhatja a közzétételi munkafolyamatot, hogy minden alkalommal fusson, amikor új virtuális merevlemezt (VHD-t) hoz létre.  Ezt a munkafolyamatot a következő diagram és mintakód szemlélteti.

![Az ajánlatok további kiadványainak interakciói](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

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
