---
title: Azure-tárolók rendszerkép-ajánlata | Azure piactér
description: A containers ajánlat Azure Marketplace-en való közzétételének folyamata.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: dsindona
ms.openlocfilehash: 81908de5c2fb3960684ed6cf37952e8815d8a5d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148299"
---
# <a name="containers"></a>Containers

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli Container-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az [Azure Container-ajánlat létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

<table> <tr> <td>Ez a szakasz ismerteti, hogyan tehet közzé egy tároló-rendszerképet az <a href="https://azuremarketplace.microsoft.com">Azure Marketplace</a>-en.  
A tárolói ajánlat típusa támogatja az <a href="https://docs.microsoft.com/azure/aks/index">Azure Kubernetes Service</a> instances-ként kiépített Docker-tároló rendszerképeket, illetve a <a href="https://docs.microsoft.com/azure/container-registry">Azure Container Registry</a> adattárban üzemeltetett <a href="https://docs.microsoft.com/azure/container-instances/container-instances-overview">Azure Container instances</a> . </td> <td><img src="./media/container-icon.png"  alt="Azure container icon" /></td> </tr> </table>

## <a name="offer-components"></a>Ajánlatok összetevői

Ez a szakasz a tárolók közzétételének elemeit ismerteti, és az Azure Marketplace-en a közzétevőre vonatkozó útmutatóként szolgál. A közzététel a következő fő részekre oszlik:

- [Előfeltételek](./cpp-prerequisites.md) – a technikai és üzleti követelmények felsorolása a tárolók létrehozásának vagy közzétételének megkezdése előtt.
- [Ajánlat létrehozása](./cpp-create-offer.md) – felsorolja azokat a lépéseket, amelyek szükségesek az új tároló-ajánlati bejegyzés létrehozásához a Cloud Partner Portal használatával.
- [A technikai eszközök előkészítése](./cpp-create-technical-assets.md) – a tároló megoldás technikai eszközeinek létrehozása az Azure piactéren elérhető ajánlatként.
- [Az ajánlat közzététele](./cpp-publish-offer.md) – az ajánlat elküldése az Azure Marketplace-re való közzétételre.

## <a name="container-publishing-process"></a>Tároló-közzétételi folyamat

A következő ábra a virtuálisgép-ajánlat közzétételének magas szintű lépéseit mutatja be.
![Az ajánlatok közzétételének lépései](./media/containers-offer-process.png)

A tárolók ajánlatának közzétételéhez szükséges magas szintű lépések a következők:

1. Ajánlat létrehozása – részletes információkat nyújt az ajánlatról. Ezek az adatok a következők: az ajánlat leírása, a marketing-anyagok, a támogatási információk és az eszközök specifikációja.
2. Hozza létre az üzleti és technikai eszközöket – hozza létre az üzleti eszközöket (jogi dokumentumok és marketing-anyagok) és a kapcsolódó megoldáshoz tartozó technikai eszközöket (a Azure Container Registryban tárolt tárolók lemezképeit.
3. Hozza létre a SKU-t – hozza létre az ajánlathoz társított SKU (ka) t. Minden közzétenni kívánt rendszerképhez egyedi SKU szükséges.
4. Az ajánlat tanúsítása és közzététele – az ajánlat és a technikai eszközök befejezése után elküldheti az ajánlatot. Ez a Küldés elindítja a közzétételi folyamatot. A folyamat során a megoldás tesztelése, ellenőrzése, minősítése, majd az Azure piactéren az "élő" állapot érhető el.

## <a name="next-steps"></a>További lépések

Mielőtt figyelembe veszi ezeket a lépéseket, meg kell felelnie a tárolónak a Microsoft Azure Marketplaceba való közzétételének [technikai és üzleti követelményeinek](./cpp-prerequisites.md) .
