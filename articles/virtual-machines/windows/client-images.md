---
title: "A Windows ügyfél képeket használ az Azure-ban |} Microsoft Docs"
description: "Visual Studio előfizetés előnyöket használatáról központi telepítése a Windows 7, Windows 8 vagy Windows 10 Azure-ban fejlesztési/Tesztelési forgatókönyvek"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 6db900981daa2139a0d2e951f41fc48d5c291074
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-ügyfél használata az Azure-ban fejlesztési/Tesztelési forgatókönyvek
Használhatja a Windows 7, Windows 8 vagy Windows 10 fejlesztési és tesztelési célú forgatókönyvek az Azure-ban biztosított megfelelő (korábbi nevén MSDN) Visual Studio-előfizetéssel rendelkezik. Ez a cikk ismerteti az Azure-ban és az Azure-katalógus képek használatát futó Windows-ügyfél jogosultsági követelményei.

## <a name="subscription-eligibility"></a>Előfizetés jogosultság
Aktív (személyek szerezték be egy Visual Studio előfizetői licenccel) Visual Studio-előfizetők fejlesztési és tesztelési célra használhatja Windows ügyfél. Windows-ügyfél hardver- és a saját Azure-előfizetés típusú futó Azure virtuális gépek is használhatók. Windows-ügyfél lehet, hogy nem kell telepített használt Azure normális üzemi használatra, vagy azok, akik nem aktív Visual Studio-előfizetők által használt.

Az Ön kényelme érdekében bizonyos Windows 10-lemezképek érhetők el az Azure katalógusából belül [jogosult fejlesztési és tesztelési célú kínál](#eligible-offers). A Visual Studio-előfizetők ajánlat bármilyen típusú belül is [megfelelően készítse elő és hozzon létre](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10-lemezképet, majd [feltöltése az Azure-bA](upload-generalized-managed.md). Használatát marad által aktív Visual Studio-előfizetők fejlesztési és tesztelési célú korlátozódik.

## <a name="eligible-offers"></a>Jogosult ajánlatok
Az alábbi táblázat részletezi az ajánlat azonosítóját, amely jogosult központi telepítése a Windows 10 és az Azure katalógusában. Az itt következő ajánlatok csak láthatók a Windows 10-lemezképeket. Ki kell futtatnia a Windows-ügyfél egy másik ajánlattípus a Visual Studio-előfizetők megkövetelik a [megfelelően készítse elő és hozzon létre](prepare-for-upload-vhd-image.md) egy 64 bites Windows 7, Windows 8 vagy Windows 10 lemezképet és [majd töltse fel az Azure-bA](upload-generalized-managed.md).

| Csomag neve | Csomag száma | Rendelkezésre álló ügyfélkezelési lemezképek |
|:--- |:---:|:---:|
| [Fejlesztés/tesztelés – használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [A Visual Studio Enterprise (MPN) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [A Visual Studio Professional előfizetők](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [A Visual Studio Test Professional előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium MSDN (juttatás)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [A Visual Studio vállalati előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [A Visual Studio Enterprise (BizSpark) előfizetői](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Vállalati fejlesztési és tesztelési célú](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Ellenőrizze az Azure-előfizetéshez
Ha nem ismeri a ajánlat Azonosítóját, szerezheti be, ezek két módszer egyikével az Azure portálon keresztül:  

- Az a *előfizetések* ablakban:

  ![Az ajánlat részletei az Azure-portálon](./media/client-images/offer-id-azure-portal.png) 

- Vagy kattintson a **számlázási** , majd az előfizetés-azonosító. Az ajánlat azonosító szerepel a *számlázási* ablak.

Az ajánlat Azonosítót a is megtekintheti a ["Előfizetések" lapon](http://account.windowsazure.com/Subscriptions) az Azure-fiókportál:

![Az Azure-fiók portálon ajánlat részletei](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Következő lépések
Most már telepítheti a virtuális gépek [PowerShell](quick-create-powershell.md), [Resource Manager-sablonok](ps-template.md), vagy [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

