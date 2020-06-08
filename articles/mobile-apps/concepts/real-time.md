---
title: Valós idejű mobil alkalmazások egyszerű létrehozása az Azure Signaler szolgáltatással
description: Ismerkedjen meg a szolgáltatással, amely lehetővé teszi, hogy valós idejű alkalmazásokat hozzon létre az Azure Signaler szolgáltatás használatával.
author: codemillmatt
ms.assetid: 34a8a070-0123-4678-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: 5a6b9ddf1822ba72585ce05425f304895d0a3295
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482568"
---
# <a name="build-real-time-applications-with-azure-signalr-service"></a>Valós idejű alkalmazások létrehozása az Azure Signaler szolgáltatással

Az [Azure signaler szolgáltatással](https://azure.microsoft.com/services/signalr-service/)könnyedén hozzáadhat valós idejű funkciókat az alkalmazásokhoz. Ez a valós idejű funkció lehetővé teszi, hogy a szolgáltatás a tartalom frissítéseit leküldse a csatlakoztatott mobil alkalmazásokba. Ennek eredményeképpen az alkalmazások frissítése nem szükséges a kiszolgáló lekérdezése vagy új HTTP-kérések elküldése a frissítésekhez.

## <a name="azure-signalr-service-features"></a>Az Azure Signaler szolgáltatás funkciói
- Széles körű ügyféltámogatást biztosít az ügyfelek széles köre, például a web-és mobil böngészők, az asztali alkalmazások, a kiszolgálói folyamat, a IoT-eszközök és a játékkonzolok számára.
- Lehetővé teszi, hogy több példány működjön együtt több millió mobileszköz-kapcsolatra. Emellett több globális régiót is támogat a horizontális felskálázáshoz, a magas rendelkezésre álláshoz vagy a vész-helyreállítási célokra.
- A széles körű API-kat biztosít különböző üzenetkezelési minták számára, hogy üzeneteket küldjön egy adott kapcsolatra, minden kapcsolatra vagy egy adott felhasználóhoz tartozó kapcsolatok egy részhalmazára, vagy egy tetszőleges csoportba helyezték őket.

## <a name="references"></a>Referencia
   - [Azure Portal](https://portal.azure.com)
   - [Az Azure SignalR Service dokumentációja](/azure/azure-signalr/signalr-overview)
   - [Oktatóanyagok](/azure/azure-signalr/signalr-tutorial-authenticate-azure-functions)