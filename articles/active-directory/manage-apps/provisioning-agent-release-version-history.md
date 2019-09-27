---
title: 'Azure AD Connect kiépítési ügynök: Verzió kiadási előzményei | Microsoft Docs'
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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ce9549765f6a912b3e95f99d11da20347b82ad8
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326465"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect kiépítési ügynök: Verziókiadások előzményei
Ez a cikk a kiadott Azure Active Directory Connect kiépítési ügynök verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynököt új funkciókkal és funkciókkal. A kiépítési ügynök automatikusan frissül, amikor megjelent egy új verzió. 

Javasoljuk, hogy az ügynökök automatikus frissítésének engedélyezésével ellenőrizze, hogy a legújabb funkciókkal és hibajavításokkal rendelkezik-e. A Microsoft közvetlen támogatást biztosít az ügynök legújabb verziójához és egy korábbi verzióhoz.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: Kiadva az automatikus frissítéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* További nyomkövetési és naplózási beállítások konfigurálása a kiépítési ügynökkel kapcsolatos problémák felderítéséhez
* Csak azokat az Azure AD-attribútumokat lehet beolvasni, amelyek a leképezésben vannak konfigurálva a szinkronizálás teljesítményének növeléséhez

### <a name="fixed-issues"></a>Hibák kijavítva:

* Kijavítva egy olyan hibát, amelyben az ügynök nem válaszoló állapotba került, ha probléma merült fel az Azure AD-beli kapcsolatok meghibásodása esetén
* Kijavított egy hibát, amely problémát okozott, amikor a bináris adatok beolvasása Azure Active Directory
* Kijavítva egy olyan hibát, amelyben az ügynök nem tudta megújítani a bizalmi kapcsolatot a Cloud Hybrid Identity Service használatával

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23.: Letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Kitatarozta a kiépítési ügynököt és az összekötő architektúráját a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* A kiépítési ügynök konfigurációjának egyszerűsítése a felhasználói felületen alapuló telepítővarázsló használatával 
* Az automatikus ügynök frissítéseinek támogatása

