---
title: Azure AD biztonságos hibrid hozzáférés | Microsoft Docs
description: Ez a cikk azokat a partneri megoldásokat ismerteti, amelyekkel az Azure AD-vel integrálhatja az örökölt helyszíni, nyilvános Felhőbeli és saját felhőalapú alkalmazásokat. Az alkalmazások kézbesítési vezérlőit vagy hálózatait az Azure AD-hez való csatlakozással védheti meg örökölt alkalmazásait.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 12/18/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 295891afbb0136e0b05bcd49f4045e0e8bcff6e5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763040"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>Biztonságos hibrid hozzáférés: a régi alkalmazások biztonságossá tétele az App Delivery Controllers és Networks szolgáltatással

Mostantól a helyszíni és a Felhőbeli örökölt hitelesítési alkalmazásokat is védetté teheti az Azure AD-hez való csatlakozással a meglévő Application Delivery Controller vagy Network használatával. Így áthidalhatja a hézagot, és megerősítheti a biztonsági helyzeteket az Azure AD-funkciókkal, például az Azure AD feltételes hozzáféréssel és a Azure AD Identity Protectionával az összes alkalmazásban.

A meglévő hálózatkezelési és kézbesítési vezérlő használatával könnyedén biztosíthatja az üzleti folyamatok számára kritikus fontosságú, de az Azure AD-vel még nem védett korábbi alkalmazásokat. Valószínűleg már mindent megtalál, amire szüksége lehet az alkalmazások védelmének megkezdéséhez.

![A biztonságos hibrid hozzáférést mutató kép](media/secure-hybrid-access/secure-hybrid-access.png)

Az alábbi gyártók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

* [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)
* [Citrix Application Delivery Controller (ADC)](../saas-apps/citrix-netscaler-tutorial.md)
* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
