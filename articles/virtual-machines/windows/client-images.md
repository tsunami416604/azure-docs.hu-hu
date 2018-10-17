---
title: Windows-ügyfélrendszerképek használata az Azure-ban |} A Microsoft Docs
description: Hogyan használható a Visual Studio-előfizetés előnyeit üzembe helyezéséhez Windows 7, Windows 8 vagy az Azure-ban Windows 10-es fejlesztési és tesztelési célra
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: eb227b4b4cbdc40a4e708726bbe0f5d0c7eacb74
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364672"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-ügyfél használata az Azure-ban fejlesztési/tesztelési célra
Használhatja a Windows 7, Windows 8, Windows 10 Enterprise (x64) az Azure-ban fejlesztési/tesztelési célra Ön által vagy egy megfelelő (korábbi nevén MSDN) a Visual Studio-előfizetéssel rendelkezik. Ez a cikk ismerteti a megfelelőségi feltételek Windows 7, Windows 8.1, Windows 10 Enterprise futtatásához az Azure és a következő Azure-katalógus-rendszerképek használata.

![Lemezkép adatait az Azure Portalról](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> A Windows 10 Pro és a Windows 10 Pro N rendszerképet az Azure-katalógusban, tekintse meg [üzembe helyezése az Azure-ban több-Bérlős üzemeltető jogosultságokkal rendelkező Windows 10-es](windows-desktop-multitenant-hosting-deployment.md)
>![Pro lemezkép adatait az Azure Portalról](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Előfizetés való jogosultság
Visual Studio-előfizetők (azok a személyek, akik szerezték be a Visual Studio előfizetési licencre) Windows-ügyfél használhatja fejlesztési és tesztelési célokra. Windows-ügyfél a saját hardver- és Azure virtuális gépeken futó bármilyen típusú Azure-előfizetés is használható. Windows ügyfél előfordulhat, hogy nem kell telepíteni vagy normál éles környezetben az Azure-ban használt, vagy személyek, akik nem aktív Visual Studio-előfizetők által használt.

Az Ön kényelme érdekében bizonyos Windows 10 rendszerű rendszerképek érhetők el az Azure katalógusából belül [jogosult fejlesztési/tesztelési ajánlatok](#eligible-offers). Visual Studio-előfizetők belül bármilyen típusú ajánlatot is [megfelelően készítse elő és hozzon létre](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10-es lemezképet, majd [feltöltése az Azure-bA](upload-generalized-managed.md). Használatát a Visual Studio-előfizetők fejlesztési-tesztelési korlátozott marad.

## <a name="eligible-offers"></a>Jogosult ajánlatok
Az alábbi táblázat ismerteti az ajánlat azonosítók, amelyek jogosultak arra, hogy üzembe helyezése az Azure katalógusában a Windows 10-es. Az alábbi ajánlatok csak láthatók a Windows 10-lemezképeket. Visual Studio-előfizetők, akiknek szükség van egy másik ajánlatra írja be a Windows-ügyfél futtatásához szükséges, hogy [megfelelően készítse elő és hozzon létre](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10-es lemezképet, és [majd töltse fel az Azure-bA](upload-generalized-managed.md).

| Csomag neve | Csomag száma | Rendelkezésre álló ügyfélkezelési képek |
|:--- |:---:|:---:|
| [Fejlesztés/tesztelés használatalapú fizetéssel](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [A Visual Studio Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [A Visual Studio Test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [A Visual Studio Premium MSDN-nel (kedvezményes)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [A Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [A Visual Studio Enterprise (BizSpark) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Nagyvállalati fejlesztés és tesztelés](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Ellenőrizze az Azure-előfizetés
Ha nem ismeri az ajánlat azonosítója, szerezheti be azt az Azure Portalon az alábbi két módszer egyikével:  

- Az a *előfizetések* ablakban:

  ![Az ajánlat részleteit az Azure Portalon](./media/client-images/offer-id-azure-portal.png) 

- Vagy kattintson **számlázási** és kattintson az előfizetési azonosítóját. Az ajánlat azonosítója megjelenik a *számlázási* ablak.

Az ajánlat Azonosítót a is megtekintheti a ["Előfizetések" lapon](http://account.windowsazure.com/Subscriptions) az Azure-fiókportál:

![Az Azure fiókportálon ajánlat részleteit](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>További lépések
Most már telepítheti a virtuális gépek [PowerShell](quick-create-powershell.md), [Resource Manager-sablonok](ps-template.md), vagy [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

