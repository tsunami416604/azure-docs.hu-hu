---
title: Windows-ügyfélképek használata az Azure-ban
description: A Visual Studio előfizetési előnyeinek használata a Windows 7, a Windows 8 vagy a Windows 10 azure-beli telepítéséhez fejlesztési és tesztelési forgatókönyvekhez
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 51e4862fe55428f112841ef7176cdb916653de0b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083274"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Windows-ügyfél használata az Azure-ban fejlesztési/tesztelési helyzetekben
Használhatja a Windows 7, Windows 8 vagy Windows 10 Enterprise (x64) az Azure-ban a fejlesztési és tesztelési forgatókönyvek, feltéve, hogy rendelkezik a megfelelő Visual Studio (korábbi MSDN) előfizetéssel. Ez a cikk ismerteti a Windows 7, A Windows 8.1, a Windows 10 Enterprise azure-beli futtatásának jogosultsági követelményeit, valamint az alábbi Azure Gallery-lemezképek használatát.

![A kép részletei az Azure Portalról](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Windows 10 Pro és Windows 10 Pro N lemezkép esetén az Azure Gallery-ben tekintse meg A Windows 10 üzembe helyezése az [Azure-ban a Több-bérlős üzemeltetési jogok](windows-desktop-multitenant-hosting-deployment.md)
>![pro lemezképének részleteit az Azure Portalon](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Előfizetésre való jogosultság
Az aktív Visual Studio-előfizetők (akik Visual Studio-előfizetési licencet szereztek) fejlesztési és tesztelési célokra használhatják a Windows-ügyfelet. A Windows-ügyfél használható a saját hardver és az Azure virtuális gépek futó bármilyen Azure-előfizetés. A Windows-ügyfél nem telepíthető az Azure-ba, és nem használható az Azure-ban normál éles használatra, illetve nem aktív Visual Studio-előfizetők használják.

Az Ön kényelme érdekében bizonyos Windows 10-es képek az Azure Gallery-ből érhetők el a [jogosult fejlesztési/tesztelési ajánlatokon](#eligible-offers)belül. A Visual Studio bármely ajánlatán belül [megfelelően előkészítheti és létrehozhatja](prepare-for-upload-vhd-image.md) a 64 bites Windows 7, Windows 8 vagy Windows 10 lemezképet, majd [feltöltheti az Azure-ba.](upload-generalized-managed.md) A használat továbbra is az aktív Visual Studio-előfizetők általi fejlesztésre/tesztelésre korlátozódik.

## <a name="eligible-offers"></a>Jogosult ajánlatok
Az alábbi táblázat ismerteti az ajánlatazonosítókat, amelyek az Azure Gallery-en keresztül telepíthetik a Windows 10-et. A Windows 10-es képek csak a következő ajánlatok ban láthatók. Azoknak a Visual Studio-előfizetőknek, akiknek más ajánlattípusban kell futtatniuk a Windows-ügyfelet, megfelelően elő kell [készítenie és létre](prepare-for-upload-vhd-image.md) kell hoznia egy 64 bites Windows 7, Windows 8 vagy Windows 10-lemezképet, majd fel kell [töltenie az Azure-ba.](upload-generalized-managed.md)

| Offer Name | Ajánlat száma | Elérhető ügyfélképek |
|:--- |:---:|:---:|
| [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise- (MPN-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional előfizetők](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional-előfizetők](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium MSDN-nel (előny)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise-előfizetők](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise- (BizSpark-) előfizetők](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Az Azure-előfizetés ellenőrzése
Ha nem ismeri az ajánlatazonosítóját, az alábbi két módon szerezheti be az Azure Portalon keresztül:  

- Az *Előfizetések* ablakban:

  ![Ajánlatazonosító részletei az Azure Portalon](./media/client-images/offer-id-azure-portal.png) 

- Vagy kattintson **a Számlázás gombra,** majd az előfizetés-azonosítójára. Az ajánlatazonosító megjelenik a *Számlázás* ablakban.

Az ajánlatazonosítót az Azure-fiók portál ["Előfizetések" lapján](https://account.windowsazure.com/Subscriptions) is megtekintheti:

![Ajánlatazonosító részletei az Azure-fiók portáljáról](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>További lépések
Most már telepítheti a virtuális gépeket [a PowerShell,](quick-create-powershell.md) [az Erőforrás-kezelő sablonok](ps-template.md)vagy a Visual [Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)használatával.

