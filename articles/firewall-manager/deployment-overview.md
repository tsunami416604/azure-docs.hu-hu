---
title: Azure Firewall Manager előzetes verziójának üzembe helyezése – áttekintés
description: A Azure Firewall Manager előzetes verziójához szükséges magas szintű telepítési lépések ismertetése
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: df87e652d2969d4ae12e97a2b455648cf39382c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502029"
---
# <a name="azure-firewall-manager-preview-deployment-overview"></a>Azure Firewall Manager előzetes verziójának üzembe helyezése – áttekintés

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager előzetes verzióját több módon is üzembe helyezheti, de a következő általános folyamat ajánlott.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A Azure Firewall Manager előzetes verzióját explicit módon engedélyezni kell a `Register-AzProviderFeature` PowerShell-paranccsal.
>Futtassa a következő parancsokat egy PowerShell-parancssorból:
>
>```azure-powershell
>connect-azaccount
>Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
>```
>A szolgáltatás regisztrációjának befejezéséhez akár 30 percet is igénybe vehet. Futtassa a következő parancsot a > regisztrációs állapotának megtekintéséhez:
>
>`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`



## <a name="general-deployment-process"></a>Általános telepítési folyamat

1. A hub és a küllő architektúra létrehozása

   - Hozzon létre egy biztonságos virtuális hubot a Azure Firewall Managerrel, és adja hozzá a virtuális hálózati kapcsolatokat.<br>*vagy*<br>
   - Hozzon létre egy virtuális WAN-hubot, és vegyen fel virtuális hálózati kapcsolatokat.
2. Biztonsági szolgáltatók kiválasztása

   - Biztonságos virtuális központ létrehozásakor történt.<br>*vagy*<br>
   - Meglévő virtuális WAN-központ konvertálása a virtuális központ biztonságossá tételéhez.
3. Tűzfalszabály létrehozása és hozzárendelése a hubhoz

   - Csak Azure Firewall használata esetén alkalmazható.
   - A harmadik féltől származó biztonsági szolgáltatások (SECaaS) házirendjei a partnerek felügyeleti felületén keresztül konfigurálhatók.
4. Útvonal-beállítások konfigurálása a forgalom biztonságos hubhoz való irányításához

   - A biztonságos hub útvonal-beállítási lapján könnyedén irányíthatja a forgalmat a biztonságos hubhoz a felhasználó által megadott útvonalak (UDR) nélküli szűréshez és naplózáshoz a küllős virtuális hálózatokon.

> [!NOTE]
> - Régiónként legfeljebb egy hub virtuális WAN-t használhat. Ennek eléréséhez azonban több virtuális WAN-t is hozzáadhat a régióhoz.
> - A vWAN lévő hubok esetében nem lehet átfedésben lévő IP-szóközök.
> - A hub-VNet kapcsolatainak ugyanabban a régióban kell lenniük, mint a hub-nak.

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: a felhőalapú hálózat védelme a Azure Firewall Manager előzetes verziójával a Azure Portal használatával](secure-cloud-network.md)