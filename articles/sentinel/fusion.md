---
title: Fejlett többlépcsős támadások észlelése az Azure Sentinelben
description: Az Azure Sentinel fúziós technológiájának használatával csökkentheti a riasztások fáradtságát, és a fejlett többlépcsős támadási észlelésen alapuló, gyakorlati incidenseket hozhat létre.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: rkarlin
ms.openlocfilehash: 5ab5d3c0fc1c37feaac2cc6b4b6837627c5a82df
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500644"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Fejlett többlépcsős támadások észlelése az Azure Sentinelben


> [!IMPORTANT]
> Az Azure Sentinel néhány fúziós funkciója jelenleg nyilvános előzetes verzióban érhető el.
> Ezeket a szolgáltatásokat szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



A gépi tanuláson alapuló fúziós technológiával az Azure Sentinel automatikusan észleli a többszörös támadásokat a rendellenes viselkedés és a gyanús tevékenységek kombinálásával, amelyek a kill-Chain különböző szakaszaiban figyelhetők meg. Az Azure Sentinel ezután olyan incidenseket hoz létre, amelyek egyébként nagyon nehézek lesznek a fogáshoz. Ezek az incidensek két vagy több riasztást vagy tevékenységet foglalnak magukban. A tervezés szerint ezek az incidensek alacsony mennyiségű, magas megbízhatóságú és nagy fontossággal rendelkeznek.

A környezethez testre szabott, ez az észlelés nem csupán csökkenti a hamis pozitív arányt, de a korlátozott vagy hiányzó adatokat tartalmazó támadásokat is képes észlelni.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfiguráció a fejlett többlépcsős támadás észleléséhez

Ez az észlelés alapértelmezés szerint engedélyezve van az Azure Sentinelben. Az állapot ellenőrzéséhez, illetve annak letiltásához, mert a több riasztáson alapuló incidensek létrehozására alternatív megoldást használ, kövesse az alábbi utasításokat:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigáljon az **Azure Sentinel** > **Configuration** > **Analytics szolgáltatáshoz**

3. Válassza az **aktív szabályok** lehetőséget, és keresse meg a **speciális többlépcsős támadás észlelését** a **Name (név** ) oszlopban. Ellenőrizze az **állapot** oszlopot, és erősítse meg, hogy az észlelés engedélyezve van vagy le van tiltva.

4. Az állapot módosításához válassza ki ezt a bejegyzést, és a **speciális többlépcsős támadás-észlelés** panelen válassza a **Szerkesztés**lehetőséget.

5. A **szabály létrehozása varázsló** panelen az állapot módosítása automatikusan ki lesz választva, ezért válassza a **Tovább: Áttekintés**, majd a **Mentés**lehetőséget. 

A szabály sablonjai nem alkalmazhatók a fejlett többlépcsős támadás észlelésére.

> [!NOTE]
> Az Azure Sentinel jelenleg 30 napos korábbi adatmennyiséget használ a gépi tanulási rendszerek betanításához. Ezeket az adategységeket a rendszer mindig a Microsoft kulcsaival titkosítja, ahogy az a Machine learning-folyamaton halad át. A betanítási adatait azonban nem titkosítja az [ügyfél által felügyelt kulcsokkal (CMK)](customer-managed-keys.md) , ha engedélyezte a CMK használatát az Azure Sentinel-munkaterületen. A fúzió letiltásához navigáljon az **Azure Sentinel** \> **Configuration** \> **Analytics \> aktív szabályok \> Advanced többlépcsős támadás észlelése** és az **állapot** oszlopban válassza a **Letiltás lehetőséget.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fúzió a Palo Alto Networks és a Microsoft Defender ATP használatával

Ezek a forgatókönyvek a biztonsági elemzők által használt alapvető naplók közül kettőt kombinálnak: a tűzfal naplóit a Palo Alto hálózatokból és a Microsoft Defender ATP-ből származó végpontok észlelési naplóiból. Az alább felsorolt forgatókönyvekben a rendszer gyanús tevékenységet észlel a végponton, amely egy külső IP-címet is magában foglal, majd ezt követi a külső IP-címről érkező rendellenes forgalom a tűzfalon. A Palo Alto-naplókban az Azure Sentinel a [veszélyforrások naplóira](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)koncentrál, és a forgalom gyanúsnak minősül, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgálatok, kémprogramok, URL-címek, vírusok, sebezhetőségek, Futótűz-vírusok, erdőtüzek).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Hálózati kérelem a TOR névtelenítésével szolgáltatáshoz, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.

Ebben az esetben az Azure Sentinel először riasztást észlel arról, hogy a Microsoft Defender komplex veszélyforrások elleni védelem hálózati kérelmet észlelt egy olyan TOR névtelenítésével-szolgáltatáshoz, amely rendellenes tevékenységeket eredményezett. Ezt a (z) {Time} SID-AZONOSÍTÓJÚ {SID} nevű fiókban kezdeményezték. A kapcsolódás kimenő IP-címe: {IndividualIp}.
Ezt követően a Palo Alto Networks-tűzfal szokatlan tevékenységet észlelt a következő helyen: {TimeGenerated}. Ez azt jelzi, hogy a hálózati forgalom célként megadott IP-címe {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell gyanús hálózati kapcsolatokat követett, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.

Ebben az esetben az Azure Sentinel először riasztást észlel, miszerint a Microsoft Defender komplex veszélyforrások elleni védelem észlelte, hogy a PowerShell gyanús hálózati kapcsolatot hozott létre, amely a Palo Alto hálózati tűzfal által észlelt rendellenes tevékenységekhez vezet. Ezt a (z) {Time} SID-AZONOSÍTÓJÚ {SID} fiókkal kezdeményezte. A kapcsolódás kimenő IP-címe: {IndividualIp}. Ezt követően a Palo Alto Networks-tűzfal szokatlan tevékenységet észlelt a következő helyen: {TimeGenerated}. Ez azt jelzi, hogy a hálózatban a rosszindulatú forgalom lett megadva. A hálózati forgalom cél IP-címe: {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Kimenő kapcsolat az IP-címmel a jogosulatlan hozzáférési kísérletek előzményeivel, amelyet a Palo Alto Networks tűzfal által megjelölt rendellenes forgalom követ.

Ebben az esetben az Azure Sentinel olyan riasztást észlel, amely szerint a Microsoft Defender komplex veszélyforrások elleni védelem kimenő kapcsolatot észlelt egy IP-címmel, és a nem engedélyezett hozzáférési kísérletek előzményeit a Palo Alto által észlelt rendellenes tevékenységekhez vezet. Hálózati tűzfal. Ezt a (z) {Time} SID-AZONOSÍTÓJÚ {SID} fiókkal kezdeményezte. A kapcsolódás kimenő IP-címe: {IndividualIp}. Ezt követően a Palo Alto Networks tűzfal szokatlan tevékenységet észlelt a következő helyen: {TimeGenerated}. Ez azt jelzi, hogy a hálózatban a rosszindulatú forgalom lett megadva. A hálózati forgalom cél IP-címe: {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Az Identity Protectiont és a Microsoft Cloud App Securityt használó fúzió

Az Azure Sentinel fejlett többlépcsős támadásokkal támogatja az alábbi forgatókönyveket, amelyekkel összekapcsolhatja a Azure Active Directory Identity Protection és Microsoft Cloud App Security anomália eseményeit:

- [Nem lehet utazni atipikus helyre, amelyet rendellenes Office 365-tevékenység követ](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Ismeretlen helyre vonatkozó bejelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ.](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenység](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység a felhasználótól a kiszivárgott hitelesítő adatokkal, majd a rendellenes Office 365-tevékenységgel](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Rendelkeznie kell a [Azure ad Identity Protection adatösszekötővel](connect-azure-ad-identity-protection.md) és a konfigurált [Cloud app Security](connect-cloud-app-security.md) -összekötővel.

Az alábbi leírásokban az Azure Sentinel megjeleníti az ezen az oldalon megjelenített adatok tényleges értékét zárójelek között. Például a fiók tényleges megjelenítendő neve nem \<*fiók neve*>, és a tényleges szám nem \<*szám*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Nem lehet utazni atipikus helyre, amelyet rendellenes Office 365-tevékenység követ

Hét lehetséges Azure Sentinel-incidens áll rendelkezésre, amelyek összekapcsolják a lehetetlen utazást szokatlan helyre Azure AD Identity Protection és a Microsoft Cloud App Security által generált rendellenes Office 365-riasztások esetén:

- **Az Office 365 postaláda-kiszűrése vezető atipikus helyekre nem lehet utazni**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre, majd egy gyanús beérkezett fájlok továbbítására vonatkozó szabály, amelyet egy felhasználó Beérkezett üzenetek mappájában beállítottak.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználó \<*fióknevet*> létrehozott vagy frissített egy beérkezett fájlok továbbítására vonatkozó szabályt, amely az összes bejövő e-mailt továbbítja a külső címre \<*e-mail-címre*>.

- **Nem lehet utazni a gyanús Cloud app felügyeleti tevékenységekhez vezető atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre.
    
    Ezt követően a fiók \<*fiók neve*> \<*számú*> felügyeleti tevékenységen keresztül hajtott végre egyetlen munkamenetben.

- **Nem lehet utazni a tömeges fájl törlését eredményező atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> \<*hely*>, egy szokatlan hely. 
    
    Ezután a fiók \<*fiók neve*> törölve \<egyetlen munkamenetben > egyedi fájlok *száma*.

- **Nem lehet utazni a tömeges fájl letöltéséhez vezető atipikus helyekre**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre. 
    
    Ezután a fiók \<a *fiók neve*> a \<*számú*> egyedi fájl egyetlen munkamenetben való letöltését.

- **Az Office 365 megszemélyesítéshez vezető atipikus helyekre nem lehet utazni**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre. 
    
    Ezután a fiók \<a *fiók neve*> a megszemélyesítési tevékenységek egy adott munkamenetben való szokatlan mennyiségét *(\<>* ) hajtotta végre.

- **Nem lehet utazni a tömeges fájlmegosztást eredményező atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre. 
    
    Ezután a fiók \<a *fiók nevét*, > megosztva \<*számú*> egyedi fájl egyetlen munkamenetben.

- **Nem lehet utazni a Cloud app ransomware vezető atipikus helyszínekre**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyre*>, egy szokatlan helyre. 
    
    Ezután a fiók \<a *fiók neve*> feltöltve \<*számú*> fájlt, és törölte a \<fájlok teljes *számát*. 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Ismeretlen helyre vonatkozó bejelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ.

Hét lehetséges Azure Sentinel-incidens van, amelyek a bejelentkezési tevékenységet a Microsoft Cloud App Security által generált Azure AD Identity Protection és rendellenes Office 365 riasztásokkal ötvözik.

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Exchange Online postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fióknév*> \<*helyről*>, ismeretlen hely, majd egy gyanús bejövő továbbítási szabály, amelyet a felhasználó Beérkezett üzenetek mappájában beállítottak.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználó \<*fióknevet*> létrehozott vagy frissített egy beérkezett fájlok továbbítására vonatkozó szabályt, amely az összes bejövő e-mailt továbbítja a külső címre \<*e-mail-címre*>. 

- **Bejelentkezési esemény olyan ismeretlen helyről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről. 
    
    Ezután a fiók \<*fiók nevét*> hajtja végre \<*számú*> felügyeleti tevékenység egyetlen munkamenetben.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlok törlését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről. 
    
    Ezután a fiók \<*fiók neve*> törölve \<egyetlen munkamenetben > egyedi fájlok *száma*.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a Mass file letöltéséhez vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről. 
    
    Ezután a fiók \<a *fiók neve*> a \<*számú*> egyedi fájl egyetlen munkamenetben való letöltését.

- **Bejelentkezési esemény egy ismeretlen helyről, amely az Office 365 megszemélyesítéshez vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről.
    
    Ezután a fiók \<a *fiók neve*> megszemélyesített \<*számú*> különböző fiók egyetlen munkamenetben.

- **Bejelentkezési esemény egy ismeretlen helyről, amely a tömeges fájlmegosztást eredményezi**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről. 
    
    Ezután a fiók \<a *fiók nevét*, > megosztva \<*számú*> egyedi fájl egyetlen munkamenetben.

- **Bejelentkezési esemény ismeretlen helyről, amely a Cloud app ransomware vezető**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a \<*helyről*>, ismeretlen helyről. 
    
    Ezután a fiók \<a *fiók neve*> feltöltve \<*számú*> fájlt, és törölte a \<fájlok teljes *számát*. 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység

A rendszer hét lehetséges Azure Sentinel-eseményt tartalmaz, amelyek a Azure AD Identity Protection és a rendellenes Office 365 által generált riasztásokból származó bejelentkezési tevékenységeket egyesítenek Microsoft Cloud App Security:

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről, amelyet egy gyanús beérkezett fájlok továbbítására vonatkozó szabály állított be a felhasználó postaládájába.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználó \<*fióknevet*> létrehozott vagy frissített egy beérkezett fájlok továbbítására vonatkozó szabályt, amely az összes bejövő e-mailt továbbítja a külső címre \<*e-mail-címre*>. 

- **Bejelentkezési esemény olyan fertőzött eszközről, amely gyanús Cloud app rendszergazdai tevékenységet vezet**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről.
    
    Ezután a fiók \<*fiók nevét*> hajtja végre \<*számú*> felügyeleti tevékenység egyetlen munkamenetben.

- **Bejelentkezési esemény egy fertőzött eszközről, amely a tömeges fájl törlését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről. 
    
    Ezután a fiók \<*fiók neve*> törölve \<egyetlen munkamenetben > egyedi fájlok *száma*.

- **Bejelentkezési esemény egy fertőzött eszközről, amely a Mass file letöltését eredményezi**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről. 
    
    Ezután a fiók \<a *fiók neve*> a \<*számú*> egyedi fájl egyetlen munkamenetben való letöltését.

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely az Office 365 megszemélyesítést vezeti**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről. 
    
    Ezután a fiók \<a *fiók neve*> megszemélyesített \<*számú*> különböző fiók egyetlen munkamenetben.

- **Bejelentkezési esemény a tömeges fájlmegosztást eredményező fertőzött eszközről**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről. 
    
    Ezután a fiók \<a *fiók nevét*, > megosztva \<*számú*> egyedi fájl egyetlen munkamenetben.

- **Bejelentkezési esemény egy olyan fertőzött eszközről, amely ransomware vezet a Cloud app szolgáltatásban**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> egy kártevő szoftverrel fertőzött eszközről. 
    
    Ezután a fiók \<a *fiók neve*> feltöltve \<*számú*> fájlt, és törölte a \<fájlok teljes *számát*. 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenység

Hét lehetséges Azure Sentinel-incidens áll rendelkezésre, amelyek a bejelentkezési tevékenységet kombinálják a névtelen IP-címek értesítéseitől Azure AD Identity Protection és a rendellenes Office 365-riasztásokból, amelyeket a Microsoft Cloud App Security generált:

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 postaláda-kiszűrése vezet**
    
    Ez a riasztás azt jelzi, hogy \<*fióknév*> a névtelen proxy IP-címe \<*ip-cím*>, majd egy gyanús beérkezett fájlok továbbítására vonatkozó szabály, amelyet egy felhasználó Beérkezett üzenetek mappájában beállítottak.
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználó \<*fióknevet*> létrehozott vagy frissített egy beérkezett fájlok továbbítására vonatkozó szabályt, amely az összes bejövő e-mailt továbbítja a külső címre \<*e-mail-címre*>. 

- **Bejelentkezési esemény olyan névtelen IP-címről, amely gyanús Cloud app felügyeleti tevékenységhez vezet**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<*fiók nevét*> hajtja végre \<*számú*> felügyeleti tevékenység egyetlen munkamenetben.

- **Bejelentkezési esemény a tömeges fájlok törlését eredményező névtelen IP-címről**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<*fiók neve*> törölve \<egyetlen munkamenetben > egyedi fájlok *száma*.

- **Bejelentkezési esemény a tömeges fájl letöltését eredményező névtelen IP-címről**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<a *fiók neve*> a \<*számú*> egyedi fájl egyetlen munkamenetben való letöltését.

- **Bejelentkezési esemény egy névtelen IP-címről, amely az Office 365 megszemélyesítést vezeti**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<a *fiók neve*> megszemélyesített \<*számú*> különböző fiók egyetlen munkamenetben.

- **Bejelentkezési esemény egy tömeges fájlmegosztást eredményező névtelen IP-címről**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<a *fiók nevét*, > megosztva \<*számú*> egyedi fájl egyetlen munkamenetben.

- **Bejelentkezési esemény névtelen IP-címről a Cloud app ransomware**
    
    Ez a riasztás egy bejelentkezési esemény megjelölése \<*fióknév*> egy névtelen proxy IP-címe \<*IP-cím*>. 
    
    Ezután a fiók \<a *fiók neve*> feltöltve \<*számú*> fájlt, és törölte a \<fájlok teljes *számát*. 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység a felhasználótól a kiszivárgott hitelesítő adatokkal, majd a rendellenes Office 365-tevékenységgel

Hét lehetséges Azure Sentinel-incidens van, amelyek a bejelentkezési tevékenységet összekapcsolják a felhasználótól a kiszivárgott hitelesítő adatokkal Azure AD Identity Protection és az Microsoft Cloud App Security által generált rendellenes Office 365 riasztásokkal:

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 postaláda kiszűrése**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta, amelyet egy gyanús beérkezett fájlok továbbítására vonatkozó szabály követ egy felhasználó beérkezett üzeneteiben. 
    
    Ez arra utalhat, hogy a fiók biztonságban van, és a postaláda használatával exfiltrate az adatokat a szervezettől. A felhasználó \<*fióknevet*> létrehozott vagy frissített egy beérkezett fájlok továbbítására vonatkozó szabályt, amely az összes bejövő e-mailt továbbítja a külső címre \<*e-mail-címre*>. 

- **Bejelentkezési esemény a felhasználótól olyan kiszivárgott hitelesítő adatokkal, amelyek gyanús Cloud app felügyeleti tevékenységet vezetnek**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta.
    
    Ezután a fiók \<*fiók nevét*> hajtja végre \<*számú*> felügyeleti tevékenység egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, amelyek a tömeges fájlok törlését eredményezik**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta.
    
    Ezután a fiók \<*fiók neve*> törölve \<egyetlen munkamenetben > egyedi fájlok *száma*.

- **Bejelentkezési esemény a felhasználótól a fájlok letöltéséhez szükséges kiszivárgott hitelesítő adatokkal**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta.
    
    Ezután a fiók \<a *fiók neve*> a \<*számú*> egyedi fájl egyetlen munkamenetben való letöltését.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal az Office 365 megszemélyesítéshez vezető módon**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta. 
    
    Ezután a fiók \<a *fiók neve*> megszemélyesített \<*számú*> különböző fiók egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a tömeges fájlmegosztást eredményező kiszivárgott hitelesítő adatokkal**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta.
    
    Ezután a fiók \<a *fiók nevét*, > megosztva \<*számú*> egyedi fájl egyetlen munkamenetben.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal a ransomware a Cloud app szolgáltatásban**
    
    Ez a riasztás azt jelzi, hogy a bejelentkezési esemény \<*fiók neve*> a kiszivárgott hitelesítő adatokat használta. 
    
    Ezután a fiók \<a *fiók neve*> feltöltve \<*számú*> fájlt, és törölte a \<fájlok teljes *számát*. 
    
    Ez a tevékenységi minta egy lehetséges ransomware támadásra utal.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a fejlett többlépcsős támadások észlelését, az alábbi rövid útmutatóból megtudhatja, hogyan tekintheti meg az adatait és a potenciális fenyegetéseket: Ismerkedés [Az Azure Sentinel szolgáltatással](quickstart-get-visibility.md).

Ha készen áll az Ön számára létrehozott incidensek vizsgálatára, tekintse meg a következő oktatóanyagot: [incidensek vizsgálata az Azure Sentinel](tutorial-investigate-cases.md)használatával.

