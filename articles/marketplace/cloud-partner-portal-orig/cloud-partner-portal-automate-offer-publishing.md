---
title: Az ajánlat közzétételi automatizálása |} Az Azure Marketplace-en
description: Programozott módon automatizálhatja a virtuális gép közzétételi munkafolyamatot ismerteti.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 0a927c72a82c6aa3c79988c599ea8b840821a2b8
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935903"
---
<a name="automate-offer-publishing"></a>Az ajánlat közzétételi automatizálása
=========================

A virtuális gép közzététele a munkafolyamat, az API-k használatával programozott módon is automatizálható a [API-referencia](./cloud-partner-portal-api-overview.md) szakaszban. Automation tervezése közben vegye figyelembe, hogy két különböző forgatókönyv közül választhat: kezdeti közzétételt és az ezt követő ajánlat közzététele.


<a name="offer-initial-publishing"></a>Ajánlat az első közzététel
-------------------------

Ha közzéteszi első alkalommal egy ajánlatra, néhány további lépések szükségesek a Marketplace-en való feltöltés előtt.  Például kell előkészíteni a metaadatok, és hozzon létre egy ajánlatot piszkozat. A kezdeti közzétételi munkafolyamatot az alábbi ábrán látható.

![Interakciók kiindulási kiadvány ajánlat](media/cloud-partner-portal-automate-offer-publishing/first-time-offer-publishing.png)

Az alábbi mintakód bemutatja ezeket a lépéseket.

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
      offer = CloudPartnerPortal.Client.GetOffer(offerName, “Preview”);
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


<a name="subsequent-offer-publishing"></a>Ezt követő ajánlat közzététele
---------------------------

Miután a virtuális gép (VM) az ajánlat integrálva van egy folyamatos integrációs folyamat, automatizálhatja a közzétételi munkafolyamat futtatását minden alkalommal, amikor létrejön egy új virtuális merevlemez (VHD).  Ezt a munkafolyamatot az alábbi ábra és a mintául szolgáló kód mutatja be.

![Az ezt követő ajánlat kiadványok interakciók](media/cloud-partner-portal-automate-offer-publishing/update-offer-and-publish.png)

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
        offer = CloudPartnerPortal.Client.GetOffer(offerName, “Preview”);
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
