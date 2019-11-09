---
title: Azure AD biztonságos hibrid hozzáférés | Microsoft Docs
description: Ez a cikk azokat a partneri megoldásokat ismerteti, amelyekkel az Azure AD-vel integrálhatja az örökölt helyszíni, nyilvános Felhőbeli és saját felhőalapú alkalmazásokat. Az alkalmazások kézbesítési vezérlőit vagy hálózatait az Azure AD-hez való csatlakozással védheti meg örökölt alkalmazásait.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b89704bdd2836057db8f7a78244205efb9cc90
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893745"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-app-delivery-controllers-and-networks"></a>Biztonságos hibrid hozzáférés: a régi alkalmazások biztonságossá tétele az App Delivery Controllers és Networks szolgáltatással

Mostantól a helyszíni és a Felhőbeli örökölt hitelesítési alkalmazásokat is védetté teheti az Azure AD-hez való csatlakozással a meglévő Application Delivery Controller vagy Network használatával. Így áthidalhatja a hézagot, és megerősítheti a biztonsági helyzeteket az Azure AD-funkciókkal, például az Azure AD feltételes hozzáféréssel és a Azure AD Identity Protectionával az összes alkalmazásban.

A meglévő hálózatkezelési és kézbesítési vezérlő használatával könnyedén biztosíthatja az üzleti folyamatok számára kritikus fontosságú, de az Azure AD-vel még nem védett korábbi alkalmazásokat. Valószínűleg már mindent megtalál, amire szüksége lehet az alkalmazások védelmének megkezdéséhez.

![A biztonságos hibrid hozzáférést mutató kép](media/secure-hybrid-access/secure-hybrid-access.png)

Az alábbi gyártók előre elkészített megoldásokat és részletes útmutatást nyújtanak az Azure AD-vel való integráláshoz.

* [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)
* [Zscaler privát hozzáférése (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
