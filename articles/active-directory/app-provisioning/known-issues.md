---
title: Az alkalmazások üzembe helyezésének ismert problémái az Azure AD-ben
description: Ismerje meg az Azure AD-ben végzett automatizált alkalmazások üzembe helyezésével kapcsolatos ismert problémákat.
author: kenwith
ms.author: kenwith
manager: celestedg
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/12/2020
ms.reviewer: arvinh
ms.openlocfilehash: 127629cb0102c2736995364db9202cd837d99a17
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214280"
---
# <a name="known-issues-application-provisioning"></a>Ismert problémák: az alkalmazás kiépítés
Ismert problémák az alkalmazások kiépítés közbeni használatáról. A UserVoice-on futó alkalmazás-kiépítési szolgáltatással kapcsolatban az [Azure ad Application UserVoice](https://aka.ms/appprovisioningfeaturerequest)című témakörben talál visszajelzést. Szorosan Figyeljük a UserVoice, így javíthatjuk a szolgáltatást. 

> [!NOTE]
> Ez nem az ismert problémák átfogó listája. Ha ismeri a nem felsorolt problémát, küldjön visszajelzést az oldal alján.

## <a name="authorization"></a>Engedélyezés 

**Nem sikerült menteni a sikeres kapcsolódási teszt után**

Ha sikeresen tesztel egy kapcsolatokat, de nem tudja menteni, akkor túllépte a hitelesítő adatok megengedett tárolási korlátját. További információ: a [rendszergazdai hitelesítő adatok mentése](application-provisioning-config-problem-storage-limit.md).

**Nem lehet menteni**

A mentéshez ki kell tölteni a bérlői URL-címet, a titkos jogkivonatot és az értesítő e-mailt. Csak az egyiket lehet megadnia. 

**A kiépítési mód nem módosítható a manuálisra**

Miután az első alkalommal konfigurálta az üzembe helyezést, megfigyelheti, hogy a kiépítési mód manuálisról automatikusra vált. A manuálisra nem módosítható. Az üzembe helyezést azonban a felhasználói felületen is kikapcsolhatja. A kiépítés a felhasználói felületen való kikapcsolása gyakorlatilag ugyanaz, mint a legördülő menü manuálisra állítása.  


## <a name="attribute-mappings"></a>Attribútumok hozzárendelése 

**A SamAccountName vagy userType attribútum nem érhető el forrás attribútumként**

A SamAccountName és a userType attribútumok alapértelmezés szerint nem érhetők el forrás attribútumként. Bővítse sémáját az attribútum hozzáadásához. Az attribútumokat a séma kibővítésével adhatja hozzá a rendelkezésre álló forrás attribútumainak listájához. További információ: [hiányzó forrás attribútum](user-provisioning-sync-attributes-for-mapping.md). 

**A séma-bővítményhez hiányzik a forrás attribútum legördülő listája**

A sémához tartozó bővítmények esetenként hiányozhatnak a felhasználói felületen a forrás attribútum legördülő menüből. Nyissa meg az attribútumok leképezésének speciális beállításait, és manuálisan adja hozzá az attribútumokat. További információ: [attribútumok leképezésének testreszabása](customize-application-attributes.md).

**NULL attribútum nem építhető ki**

Az Azure AD jelenleg nem tud null attribútumokat kiépíteni. Ha egy attribútum null értékű a felhasználói objektumon, a rendszer kihagyja a következőt:. 

**Attribútum-hozzárendelési kifejezések maximális száma**

Az attribútum-hozzárendelési kifejezések legfeljebb 10 000 karakterből állhatnak. 


## <a name="service-issues"></a>Szolgáltatásproblémák 

**Nem támogatott forgatókönyvek**

- A kiépítési jelszavak nem támogatottak. 
- A beágyazott csoportok kiépítés nem támogatott. 
- A B2C-bérlők kiépítés a bérlők mérete miatt nem támogatott. 

**A cél alkalmazásból az Azure AD-be való áttérés nem változik**

Az alkalmazás-kiépítési szolgáltatás nem ismeri a külső alkalmazásokban végrehajtott módosításokat. Így nem kerül sor a visszaállításra. Az alkalmazás-kiépítési szolgáltatás az Azure AD-ben végrehajtott módosításokra támaszkodik.  

**A kiépítési ciklus a befejezésig folytatódik**

A kiépítés `enabled = off` vagy a Leállítás megszakadása esetén az aktuális kiépítési ciklus a befejezésig folytatódni fog. A szolgáltatás addig nem hajtja végre a jövőbeli ciklusok végrehajtását, amíg újra be nem kapcsolja az üzembe helyezést.

**Nem kiépített csoport tagja**

Ha egy csoport hatókörben van, és egy tag kívül esik a hatókörön, a rendszer kiépíti a csoportot. A hatókörön kívüli felhasználó nem lesz kiépítve. Ha a tag visszatér a hatókörbe, a szolgáltatás nem ismeri fel azonnal a változást. A kiépítés újraindítása a probléma megoldásához vezet. Javasoljuk, hogy a szolgáltatás rendszeres újraindítása után ellenőrizze, hogy az összes felhasználó megfelelően van-e kiépítve.  


## <a name="next-steps"></a>Következő lépések
- [Az üzembe helyezés menete](how-provisioning-works.md)
