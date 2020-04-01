---
title: A Cloud App Security adatainak összekapcsolása az Azure Sentinelhez| Microsoft dokumentumok
description: Ismerje meg, hogyan kapcsolhatja össze a Cloud App Security adatait az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422146"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Adatok csatlakoztatása a Microsoft Cloud App Security alkalmazásbiztonságból 



A [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) összekötő lehetővé teszi a riasztások és a Cloud Discovery naplók streamelése az MCAS-ből az Azure Sentinelbe. [Cloud Discovery logs](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) Ez lehetővé teszi, hogy betekintést nyerjen a felhőalapú alkalmazásokba, kifinomult elemzéseket kapjon a kiberfenyegetések azonosításához és leküzdéséhez, valamint az adatok utazásának szabályozásához.

## <a name="prerequisites"></a>Előfeltételek

- A felhasználónak olvasási és írási engedéllyel kell rendelkeznie a munkaterületen.
- A felhasználónak globális rendszergazdai vagy biztonsági rendszergazdai engedélyekkel kell rendelkeznie a munkaterület bérlőjéhez.
- A Cloud Discovery-naplók Azure Sentinelbe való streameléséhez [engedélyezze az Azure Sentinelt SIEM-ként a Microsoft Cloud App Security szolgáltatásban.](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> A Cloud Discovery-naplók betöltése jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
 
## <a name="connect-to-cloud-app-security"></a>Csatlakozás a Cloud App Security alkalmazásbiztonsághoz

Ha már rendelkezik cloud app security, győződjön meg arról, hogy engedélyezve van [a hálózaton.](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
Ha a Cloud App Security telepítve van, és betöltése az adatokat, a riasztási adatok könnyen streamelhető az Azure Sentinel.


1. Az Azure Sentinel navigációs menüjében válassza az **Adatösszekötők**lehetőséget. Az összekötők listájában kattintson a **Microsoft Cloud App Security** csempére, majd a Jobb alsó ablakösszekötő **lapgombjára.**

1. Válassza ki, hogy mely naplókat szeretné streamelni az Azure Sentinelbe; választhat **riasztások** és **felhőfelderítési naplók** (előzetes verzió). 

1. Kattintson **a Módosítások alkalmazása gombra.**

1. Ha a megfelelő sémát szeretné használni a Log `SecurityAlert` Analytics a Cloud App Security riasztásokhoz, írja be a lekérdezési ablakban. A Cloud Discovery naplók séma, írja be a következőt. `McasShadowItReporting`

> [!NOTE]
> A Cloud Discovery segít észlelni és azonosítani a trendeket azáltal, hogy összesíti az adatokat, amelyek a felhasználók felhőalapú alkalmazásokkal való kapcsolatait alapjául szolgáló adatok összesítik.
>
> Mivel a Cloud Discovery-adatok napi alapon összesítve vannak, vegye figyelembe, hogy a legfrissebb adatok akár 24 órányi értéke nem jelenik meg az Azure Sentinelben. Abban az esetben, ha egy alacsony szintű vizsgálat azonnali adatokat igényel, azt közvetlenül a forrásberendezésben vagy a nyers adatokat tartalmazó szolgáltatásban kell elvégezni.

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a Microsoft Cloud App Securityt az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- A [beépített](tutorial-detect-threats.md) vagy [egyéni](tutorial-detect-threats-custom.md) szabályok használatával első lépések észlelheti a fenyegetéseket az Azure Sentinel segítségével.
