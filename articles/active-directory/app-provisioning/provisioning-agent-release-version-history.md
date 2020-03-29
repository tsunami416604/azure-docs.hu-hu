---
title: 'Azure AD Connect kiépítési ügynök: Verziókiadási előzmények | Microsoft dokumentumok'
description: Ez a cikk felsorolja az Azure AD Connect kiépítési ügynök összes kiadását, és ismerteti az új funkciókat és a javított problémákat
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183244"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect kiépítési ügynök: verziókiadási előzmények
Ez a cikk az Azure Active Directory Connect kiépítési ügynök kiadott verzióit és szolgáltatásait sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynök új funkciókkal és funkciókkal. A kiépítési ügynök automatikusan frissül, amikor egy új verzió t. 

A Microsoft közvetlen támogatást nyújt a legújabb ügynökverzióhoz és egy verzióhoz.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Kiadás állapota

2019. december 4.: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Támogatja az [Azure AD Connect felhőalapú kiépítését](../cloud-provisioning/what-is-cloud-provisioning.md) a felhasználók, kapcsolattartók és csoportok adatainak szinkronizálásához a helyszíni Active Directoryból az Azure AD-be


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: Automatikus frissítésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* További nyomkövetés és naplózás konfigurálása a kiépítési ügynökkel kapcsolatos problémák hibakereséséhez
* Csak azoknak az Azure AD-attribútumoknak a lekérése, amelyek a leképezésben konfigurálva vannak a szinkronizálás teljesítményének javítása érdekében

### <a name="fixed-issues"></a>Hibák kijavítva:

* Javítottunk egy hibát, amelyben az ügynök nem válaszol, ha problémák merültek fel az Azure AD-kapcsolat hibáival kapcsolatban
* Kijavítottunk egy hibát, amely problémákat okozott, amikor bináris adatokat olvasott be az Azure Active Directoryból
* Javítva egy hiba, amelyben az ügynök nem tudta megújítani a bizalmat a felhőhibrid identitásszolgáltatással

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23.: Letölthető

### <a name="new-features-and-improvements"></a>Új funkciók és fejlesztések

* Átalakította a kiépítési ügynök és az összekötő architektúráját a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* Egyszerűsítette a kiépítési ügynök konfigurációját a felhasználói felület által vezérelt telepítési varázslóval 
* Az automatikus ügyintézőfrissítések támogatása hozzáadva

