---
title: fájl belefoglalása
description: az előfeltételeket tartalmazó fájl belefoglalása
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
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68426899"
---
> [!IMPORTANT]
> **Az ebben a cikkben foglalt feladatok végrehajtása előtt engedélyezze a jelszó-kivonatok szinkronizálását Azure AD Domain Services.**
>
> Kövesse az alábbi utasításokat az Azure AD-címtárban lévő felhasználók típusától függően. Ha az Azure AD-címtárban a csak felhőalapú és szinkronizált felhasználói fiókokat kombinálja, hajtsa végre mindkét útmutatót. Előfordulhat, hogy nem tudja végrehajtani a következő műveleteket abban az esetben, ha B2B vendég fiókot szeretne használni (például a Gmail vagy a MSA egy másik, az általunk engedélyezett identitás-szolgáltatótól), mert nem rendelkezünk jelszavával a felügyelt tartományra szinkronizált felhasználók számára a címtárban található vendég fiókok. Ezeknek a fiókoknak a teljes információi, köztük a jelszavuk az Azure AD-n kívül esnek, és mivel ezek az információk nem az Azure AD-ben jelennek meg, így nem is szinkronizálható a felügyelt tartományba. 
> - [A csak felhőalapú felhasználói fiókokra vonatkozó utasítások](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [A helyszíni címtárból szinkronizált felhasználói fiókokra vonatkozó utasítások](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
