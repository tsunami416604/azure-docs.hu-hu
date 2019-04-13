---
title: fájl belefoglalása
description: az előfeltétel-fájlját
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551651"
---
> [!IMPORTANT]
> **Az Azure AD tartományi szolgáltatásokra, Jelszókivonat-szinkronizálás engedélyezése, a cikkben leírt feladatok végrehajtása előtt.**
>
> Az Azure AD-címtárban található felhasználók típusától függően az alábbi utasításokat követve. Végezze el mindkét, ha többféle, csak felhőalapú és szinkronizált felhasználói fiókok Azure AD-címtárát. Nem lehet a következő műveletek végrehajtására, abban az esetben próbált B2B Vendég fiókkal (például a gmail vagy egy másik identitásszolgáltatót, amely lehetővé tesszük az MSA) használata, mivel ezek a felhasználók szinkronizálása a felügyelt tartományhoz ezeket a jelszavát nem tudjuk a címtárban lévő vendégfiókok vannak. Ezeket a fiókokat, beleértve a jelszavukat a teljes körű információkat lenne az Azure AD-en kívül, és ahogy ezt az információt nem szerepel az Azure AD ezért azt nem még lekérése szinkronizálva a felügyelt tartományhoz. 
> - [Csak felhőalapú felhasználói fiókokra vonatkozó utasítások](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [A helyszíni címtárból szinkronizált felhasználói fiókokra vonatkozó utasítások](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
