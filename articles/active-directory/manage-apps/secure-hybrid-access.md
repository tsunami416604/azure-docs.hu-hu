---
title: Azure AD biztonságos hibrid hozzáférés | Microsoft dokumentumok
description: Ez a cikk az örökölt helyszíni, nyilvános felhőbeli vagy magánfelhő-alkalmazások Azure AD-vel való integrálására szolgáló partnermegoldásokat ismerteti. Az örökölt alkalmazások at az alkalmazáskézbesítési vezérlők vagy hálózatok Azure AD-hez való csatlakoztatásával biztosíthatja.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 12/18/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e97b95e290ef74ffd98a3396ffe4705270132b2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75433768"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>Biztonságos hibrid hozzáférés: Az örökölt alkalmazások biztonságossá tétele alkalmazáskézbesítési vezérlőkkel és hálózatokkal

Mostantól megvédheti a helyszíni és a felhőbeli örökölt hitelesítési alkalmazásokat, ha csatlakoztatja őket az Azure AD-hez a meglévő alkalmazáskézbesítési vezérlővel vagy hálózattal. Így áthidalhatja a szakadékot, és erősítheti a biztonsági állapotát az összes alkalmazásban az Azure AD-képességekkel, például az Azure AD feltételes hozzáféréssel és az Azure AD-identitásvédelemmel.

A meglévő hálózati és kézbesítési vezérlő használatával könnyedén megvédheti azokat az örökölt alkalmazásokat, amelyek még mindig kritikus fontosságúak az üzleti folyamatok szempontjából, de amelyeket az Azure AD-vel korábban nem tudott megvédeni. Valószínű, hogy már mindent megtud, amire szüksége van az alkalmazások védelméhez.

![Biztonságos hibrid hozzáférést ábrázoló kép](media/secure-hybrid-access/secure-hybrid-access.png)

A következő szállítók előre elkészített megoldásokat és részletes útmutatást kínálnak az Azure AD-vel való integrációhoz.

* [Akamai vállalati alkalmazáshozzáférés (EAA)](../saas-apps/akamai-tutorial.md)
* [Citrix alkalmazáskézbesítési vezérlő (ADC)](../saas-apps/citrix-netscaler-tutorial.md)
* [F5 Nagy IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
