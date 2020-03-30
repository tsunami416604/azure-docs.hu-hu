---
title: fájl belefoglalása
description: fájl mellékelése elő-megfeltételekkel
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
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68426899"
---
> [!IMPORTANT]
> **A jelen cikkben szereplő feladatok végrehajtása előtt engedélyezze a jelszókivonat-szinkronizálást az Azure AD tartományi szolgáltatásokkal.**
>
> Kövesse az alábbi utasításokat, attól függően, hogy milyen típusú felhasználók az Azure AD-címtárban. Töltse ki mindkét utasításkészletet, ha csak felhőalapú és szinkronizált felhasználói fiókok vegyesen rendelkezik az Azure AD-címtárban. Előfordulhat, hogy nem tudja végrehajtani a következő műveleteket abban az esetben, ha B2B vendégfiókot próbál használni (például gmail-fiókját vagy MSA-ját egy másik identitásszolgáltatótól, amelyet engedélyezünk), mert nem rendelkezünk a felügyelt tartománnyal szinkronizált felhasználók jelszavával, mivel ezek a következők: vendégfiókokat a címtárban. A teljes körű információkat ezek a fiókok, beleértve a jelszavakat kívül lenne az Azure AD,és mivel ezek az adatok nem az Azure AD ezért nem is kap szinkronizálva a felügyelt tartományba. 
> - [Utasítások a csak felhőalapú felhasználói fiókokhoz](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [A helyszíni címtárból szinkronizált felhasználói fiókokra vonatkozó utasítások](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
