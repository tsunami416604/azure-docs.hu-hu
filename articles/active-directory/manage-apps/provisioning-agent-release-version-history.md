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
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862108"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Azure AD Connect kiépítési ügynök: Verziókiadások előzményei
Ez a cikk a kiadott Azure AD Connect kiépítési ügynök verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynököt új funkciókkal és funkciókkal. A kiépítési ügynökök automatikusan frissülnek, amikor megjelent egy új verzió. 

Javasoljuk, hogy az ügynökök automatikus frissítésének engedélyezésével ellenőrizze, hogy a legújabb funkciókkal és hibajavításokkal rendelkezik-e. A Microsoft közvetlen támogatást biztosít az ügynök legújabb verziójához és egy korábbi verzióhoz.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: Kiadva az automatikus frissítéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* További nyomkövetési és naplózási beállítások konfigurálása a kiépítési ügynökkel kapcsolatos problémák felderítéséhez
* Csak a leképezésben konfigurált AD-attribútumok beolvasásának lehetősége a szinkronizálás teljesítményének növelése érdekében

### <a name="fixed-issues"></a>Hibák kijavítva:

* Javítva a hiba, ha az ügynök nem válaszoló állapotba került, ha problémák léptek fel az AD-kapcsolatok hibáival kapcsolatban
* Kijavított egy hibát, amely problémát okozott, amikor a bináris adatok beolvasása Active Directory
* Kijavítva a hiba, ha az ügynök nem tudta megújítani a bizalmat a Cloud Hybrid Identity Service használatával

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23.: Letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Átalakított kiépítési ügynök &-összekötő architektúrája a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* Egyszerűsített kiépítési ügynök konfigurálása a felhasználói felületen alapuló telepítővarázsló használatával 
* Az automatikus ügynök frissítéseinek támogatása

