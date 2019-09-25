---
title: Fejlett többlépcsős támadások észlelése az Azure Sentinelben
description: Az Azure Sentinel fúziós technológiájának használatával csökkentheti a riasztások fáradtságát, és a fejlett többlépcsős támadási észlelésen alapuló, gyakorlati incidenseket hozhat létre.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/24/2019
ms.author: cabailey
ms.openlocfilehash: e6ddb1b01b705d2a7857682bd84e9482e064a8db
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240069"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Fejlett többlépcsős támadások észlelése az Azure Sentinelben

A gépi tanuláson alapuló fúziós technológiával az Azure Sentinel automatikusan észleli a többszörös támadásokat a rendellenes viselkedés és a gyanús tevékenységek kombinálásával, amelyek a kill-Chain különböző szakaszaiban figyelhetők meg. Az Azure Sentinel ezután olyan incidenseket hoz létre, amelyek egyébként nagyon nehézek lesznek a fogáshoz. Ezek az incidensek két vagy több riasztást vagy tevékenységet foglalnak magukban. A tervezés szerint ezek az incidensek alacsony mennyiségű, magas megbízhatóságú és nagy fontossággal rendelkeznek.

A környezethez testre szabott, ez az észlelés nem csupán csökkenti a hamis pozitív arányt, de a korlátozott vagy hiányzó adatokat tartalmazó támadásokat is képes észlelni.

A támogatott forgatókönyvekre vonatkozó riasztásokkal kapcsolatos részletekért tekintse meg a [többfokozatú támadások észlelése](#scenarios-supported-for-advanced-multistage-attack-detection) című szakaszt ezen a lapon.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguráció a fejlett többlépcsős támadás észleléséhez

Ez az észlelés alapértelmezés szerint engedélyezve van az Azure Sentinelben. Az állapot ellenőrzéséhez, illetve annak letiltásához, mert a több riasztáson alapuló incidensek létrehozására alternatív megoldást használ, kövesse az alábbi utasításokat:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon az **Azure Sentinel** > **Configuration** > **Analytics szolgáltatáshoz**

3. Válassza az **aktív szabályok** lehetőséget, és keresse meg a **speciális többlépcsős támadás észlelését** a **Name (név** ) oszlopban. Ellenőrizze az **állapot** oszlopot, és erősítse meg, hogy az észlelés engedélyezve van vagy le van tiltva.

4. Az állapot módosításához válassza ki ezt a bejegyzést, és a **speciális többlépcsős támadás-észlelés** panelen válassza a **Szerkesztés**lehetőséget.

5. A **szabály létrehozása varázsló** panelen az állapot módosítása automatikusan ki lesz választva, ezért válassza a tovább lehetőséget **: Tekintse át**, majd **mentse**. 

A szabály sablonjai nem alkalmazhatók a fejlett többlépcsős támadás észlelésére.

## <a name="scenarios-supported-for-advanced-multistage-attack-detection"></a>Fejlett többfokozatú támadások észleléséhez támogatott forgatókönyvek

Az Azure Sentinel fejlett többlépcsős támadásokkal támogatja az alábbi forgatókönyveket, amelyekkel összekapcsolhatja a Azure Active Directory Identity Protection és Microsoft Cloud App Security anomália eseményeit:

- [Nem lehet utazni atipikus helyre, amelyet rendellenes Office 365-tevékenység követ](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Ismeretlen helyre vonatkozó bejelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ.](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenység](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység a felhasználótól a kiszivárgott hitelesítő adatokkal, majd a rendellenes Office 365-tevékenységgel](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Rendelkeznie kell a [Azure ad Identity Protection adatösszekötővel](connect-azure-ad-identity-protection.md) és a konfigurált [Cloud app Security](connect-cloud-app-security.md) -összekötővel.

Az alábbi leírásokban az Azure Sentinel megjeleníti az ezen az oldalon megjelenített adatok tényleges értékét zárójelek között. Például egy fiók \<tényleges megjelenítendő neve, nem pedig a *fiók neve*>, és a \<tényleges szám nem a *szám*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Nem lehet utazni atipikus helyre, amelyet rendellenes Office 365-tevékenység követ

Hét lehetséges Azure Sentinel-incidens áll rendelkezésre, amelyek összekapcsolják a lehetetlen utazást szokatlan helyre Azure AD Identity Protection és a Microsoft Cloud App Security által generált rendellenes Office 365-riasztások esetén:

- **Az Office 365 postaláda-kiszűrése vezető atipikus helyekre nem lehet utazni**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<bejelentkezési eseményekre, ha \<a *hely*>, egy szokatlan hely, és egy gyanús beérkezett fájlok továbbítására vonatkozó szabály is be van állítva egy felhasználó postaládájába.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználói \< *fiók neve*> létrehozott vagy frissített egy beérkezett fájlok továbbítására szolgáló szabályt, amely az összes beérkező \<e-mailt továbbítja a külső cím *e-mail-címére*>.

- **Nem lehet utazni a gyanús Cloud app felügyeleti tevékenységekhez vezető atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy \< \<a *fiók neve*> a bejelentkezési eseményekre, amelyeknek a *helye*nem lehet egy szokatlan hely >.
    
    Ezt követően a fiók \< *fiókjának neve*> több \< *mint >* felügyeleti tevékenységet hajtott végre egyetlen munkamenetben.

- **Nem lehet utazni a tömeges fájl törlését eredményező atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, \<hogy a *fiók neve*> a \< *hely*>, egy szokatlan hely. 
    
    Ezután \<a fiók *fiókneve*> törölte \<a > egyedi fájlok *számát*egyetlen munkamenetben.

- **Nem lehet utazni a tömeges fájl letöltéséhez vezető atipikus helyekre**
    
    Ez a riasztás azt jelzi, hogy \< \<a *fiók neve*> a bejelentkezési eseményekre, amelyeknek a *helye*nem lehet egy szokatlan hely >. 
    
    Ezt követően a fiók \< *fiókjának neve*> letöltött \<egyedi fájlok *száma*> egyetlen munkamenetben.

- **Az Office 365 megszemélyesítéshez vezető atipikus helyekre nem lehet utazni**
    
    Ez a riasztás azt jelzi, hogy \< \<a *fiók neve*> a bejelentkezési eseményekre, amelyeknek a *helye*nem lehet egy szokatlan hely >. 
    
    Ezután \<a fiók *fiókneve*> szokatlan mennyiségű (\<*tevékenységek száma*>) végezte el a megszemélyesítési tevékenységeket egyetlen munkamenetben.

- **Nem lehet utazni a tömeges fájlmegosztást eredményező atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy \< \<a *fiók neve*> a bejelentkezési eseményekre, amelyeknek a *helye*nem lehet egy szokatlan hely >. 
    
    Ezután \<a fiók *fiókjának neve*> megosztva \<> egyedi fájlok *száma*egyetlen munkamenetben.

- **Nem lehet utazni a Cloud app ransomware vezető atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy \< \<a *fiók neve*> a bejelentkezési eseményekre, amelyeknek a *helye*nem lehet egy szokatlan hely >. 
    
    Ezután \<a fiók *fiókjának neve*> > \<fájlok feltöltött *számát*, és törölte \< *a > fájlok teljes számát.* 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Ismeretlen helyre vonatkozó bejelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ.

Hét lehetséges Azure Sentinel-incidens van, amelyek a bejelentkezési tevékenységet a Microsoft Cloud App Security által generált Azure AD Identity Protection és rendellenes Office 365 riasztásokkal ötvözik.

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Exchange Online postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, \<hogy a *fiók* \<neve > a *hely*>, ismeretlen hely, majd egy gyanús beérkezett fájlok továbbítására vonatkozó szabály, amelyet egy felhasználó postaládájába állítottak be.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználói \< *fiók neve*> létrehozott vagy frissített egy beérkezett fájlok továbbítására szolgáló szabályt, amely az összes beérkező \<e-mailt továbbítja a külső cím *e-mail-címére*>. 

- **Bejelentkezési esemény olyan ismeretlen helyről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt. 
    
    Ezt követően a fiók \< *fiókjának neve*> egy \<munkamenetben > felügyeleti tevékenységek *száma*felett.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlok törlését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókneve*> törölte \<a > egyedi fájlok *számát*egyetlen munkamenetben.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a Mass file letöltéséhez vezet**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt. 
    
    Ezt követően a fiók \< *fiókjának neve*> letöltött \<egyedi fájlok *száma*> egyetlen munkamenetben.

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Office 365 megszemélyesítéshez vezet**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt.
    
    Ezután \<a fiók *fiókjának neve* \<> megszemélyesített > különböző fiókok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlmegosztást eredményezi**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> megosztva \<> egyedi fájlok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény ismeretlen helyről, amely a Cloud app ransomware vezető**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \< *hely*>, ismeretlen \<helyről származó bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> > \<fájlok feltöltött *számát*, és törölte \< *a > fájlok teljes számát.* 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység

A rendszer hét lehetséges Azure Sentinel-eseményt tartalmaz, amelyek a Azure AD Identity Protection és a rendellenes Office 365 által generált riasztásokból származó bejelentkezési tevékenységeket egyesítenek Microsoft Cloud App Security:

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, hogy a \< *fiók neve*> egy, a kártevő szoftverrel fertőzött eszközről, majd egy gyanús beérkezett fájlok továbbítására vonatkozó szabály, amelyet a felhasználó Beérkezett üzenetek mappájában adott meg.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználói \< *fiók neve*> létrehozott vagy frissített egy beérkezett fájlok továbbítására szolgáló szabályt, amely az összes beérkező \<e-mailt továbbítja a külső cím *e-mail-címére*>. 

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús Cloud app rendszergazdai tevékenységet vezet**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt.
    
    Ezt követően a fiók \< *fiókjának neve*> egy \<munkamenetben > felügyeleti tevékenységek *száma*felett.

- **Bejelentkezési esemény egy fertőzött eszközről, amely a tömeges fájl törlését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókneve*> törölte \<a > egyedi fájlok *számát*egyetlen munkamenetben.

- **Bejelentkezési esemény egy fertőzött eszközről, amely a Mass file letöltését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt. 
    
    Ezt követően a fiók \< *fiókjának neve*> letöltött \<egyedi fájlok *száma*> egyetlen munkamenetben.

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 megszemélyesítést vezeti**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve* \<> megszemélyesített > különböző fiókok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény a tömeges fájlmegosztást eredményező fertőzött eszközről**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> megosztva \<> egyedi fájlok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely ransomware vezet a Cloud app szolgáltatásban**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> egy \<, a kártevő szoftverrel fertőzött eszközről érkező bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> > \<fájlok feltöltött *számát*, és törölte \< *a > fájlok teljes számát.* 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenység

Hét lehetséges Azure Sentinel-incidens áll rendelkezésre, amelyek a bejelentkezési tevékenységet kombinálják a névtelen IP-címek értesítéseitől Azure AD Identity Protection és a rendellenes Office 365-riasztásokból, amelyeket a Microsoft Cloud App Security generált:

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, \<hogy a *fiók neve*> egy névtelen proxy IP-címének \< *IP-* címe >, amelyet egy gyanús beérkezett fájlok továbbítására vonatkozó szabály követ egy felhasználó Beérkezett üzenetek mappájába.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználói \< *fiók neve*> létrehozott vagy frissített egy beérkezett fájlok továbbítására szolgáló szabályt, amely az összes beérkező \<e-mailt továbbítja a külső cím *e-mail-címére*>. 

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezt követően a fiók \< *fiókjának neve*> egy \<munkamenetben > felügyeleti tevékenységek *száma*felett.

- **Bejelentkezési esemény a tömeges fájlok törlését eredményező névtelen IP-címről**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókneve*> törölte \<a > egyedi fájlok *számát*egyetlen munkamenetben.

- **Bejelentkezési esemény a tömeges fájl letöltését eredményező névtelen IP-címről**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezt követően a fiók \< *fiókjának neve*> letöltött \<egyedi fájlok *száma*> egyetlen munkamenetben.

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 megszemélyesítést vezeti**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve* \<> megszemélyesített > különböző fiókok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény egy tömeges fájlmegosztást eredményező névtelen IP-címről**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> megosztva \<> egyedi fájlok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény névtelen IP-címről a Cloud app ransomware**
    
    Ez a riasztás azt jelzi, hogy a *fiók neve*> a \<névtelen proxy IP-címének \< *IP-* címe > az adott bejelentkezési eseményt. 
    
    Ezután \<a fiók *fiókjának neve*> > \<fájlok feltöltött *számát*, és törölte \< *a > fájlok teljes számát.* 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység a felhasználótól a kiszivárgott hitelesítő adatokkal, majd a rendellenes Office 365-tevékenységgel

Hét lehetséges Azure Sentinel-incidens van, amelyek a bejelentkezési tevékenységet összekapcsolják a felhasználótól a kiszivárgott hitelesítő adatokkal Azure AD Identity Protection és az Microsoft Cloud App Security által generált rendellenes Office 365 riasztásokkal:

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 postaláda kiszűrése**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokat, majd egy gyanús beérkezett fájlok továbbítására vonatkozó szabályt adott meg a felhasználó beérkezett üzeneteiben. 
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználói \< *fiók neve*> létrehozott vagy frissített egy beérkezett fájlok továbbítására szolgáló szabályt, amely az összes beérkező \<e-mailt továbbítja a külső cím *e-mail-címére*>. 

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús Cloud app felügyeleti tevékenységet vezetnek**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal.
    
    Ezt követően a fiók \< *fiókjának neve*> egy \<munkamenetben > felügyeleti tevékenységek *száma*felett.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, amelyek a tömeges fájlok törlését eredményezik**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal.
    
    Ezután \<a fiók *fiókneve*> törölte \<a > egyedi fájlok *számát*egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a fájlok letöltéséhez szükséges kiszivárgott hitelesítő adatokkal**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal.
    
    Ezt követően a fiók \< *fiókjának neve*> letöltött \<egyedi fájlok *száma*> egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 megszemélyesítéshez vezető módon**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal. 
    
    Ezután \<a fiók *fiókjának neve* \<> megszemélyesített > különböző fiókok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a tömeges fájlmegosztást eredményező kiszivárgott hitelesítő adatokkal**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal.
    
    Ezután \<a fiók *fiókjának neve*> megosztva \<> egyedi fájlok *száma*egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal a ransomware a Cloud app szolgáltatásban**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \< *fiók neve*> használt kiszivárgott hitelesítő adatokkal. 
    
    Ezután \<a fiók *fiókjának neve*> > \<fájlok feltöltött *számát*, és törölte \< *a > fájlok teljes számát.* 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a fejlett többlépcsős támadások észlelését, az alábbi rövid útmutatóból megtudhatja, hogyan érheti el az adatait és a potenciális fenyegetéseket: Ismerkedjen meg [Az Azure Sentinel használatába](quickstart-get-visibility.md).

Ha készen áll az Ön számára létrehozott incidensek vizsgálatára, tekintse meg a következő oktatóanyagot: [Az incidensek vizsgálata az Azure sentinelrel](tutorial-investigate-cases.md).

