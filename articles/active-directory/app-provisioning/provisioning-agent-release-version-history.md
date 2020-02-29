---
title: 'Azure AD Connect kiépítési ügynök: verzió kiadásának előzményei | Microsoft Docs'
description: Ez a cikk felsorolja Azure AD Connect kiépítési ügynök összes kiadását, valamint ismerteti az új szolgáltatásokat és a rögzített problémákat.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183244"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect kiépítési ügynök: verziók kiadásának előzményei
Ez a cikk a kiadott Azure Active Directory Connect kiépítési ügynök verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynököt új funkciókkal és funkciókkal. A kiépítési ügynök automatikusan frissül, amikor megjelent egy új verzió. 

A Microsoft közvetlen támogatást biztosít az ügynök legújabb verziójához és egy korábbi verzióhoz.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Kiadás állapota

2019. december 4., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* A [Azure ad Connect felhőalapú](../cloud-provisioning/what-is-cloud-provisioning.md) kiépítésének támogatásával szinkronizálhatja a felhasználókat, a kapcsolattartókat és a csoport adatait a helyszíni Active Directoryból az Azure ad-be


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: kiadva az automatikus frissítéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* További nyomkövetési és naplózási beállítások konfigurálása a kiépítési ügynökkel kapcsolatos problémák felderítéséhez
* Csak azokat az Azure AD-attribútumokat lehet beolvasni, amelyek a leképezésben vannak konfigurálva a szinkronizálás teljesítményének növeléséhez

### <a name="fixed-issues"></a>Hibák kijavítva:

* Kijavítva egy olyan hibát, amelyben az ügynök nem válaszoló állapotba került, ha probléma merült fel az Azure AD-beli kapcsolatok meghibásodása esetén
* Kijavított egy hibát, amely problémát okozott, amikor a bináris adatok beolvasása Azure Active Directory
* Kijavítva egy olyan hibát, amelyben az ügynök nem tudta megújítani a bizalmi kapcsolatot a Cloud Hybrid Identity Service használatával

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Kitatarozta a kiépítési ügynököt és az összekötő architektúráját a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* A kiépítési ügynök konfigurációjának egyszerűsítése a felhasználói felületen alapuló telepítővarázsló használatával 
* Az automatikus ügynök frissítéseinek támogatása

