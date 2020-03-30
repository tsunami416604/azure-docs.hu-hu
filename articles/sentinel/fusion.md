---
title: Fejlett többfokozatú támadásészlelés az Azure Sentinelben
description: Az Azure Sentinel Fusion-technológiájával csökkentheti a riasztások fáradtságát, és speciális többlépcsős támadásészlelésen alapuló, végrehajtható incidenseket hozhat létre.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587923"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Fejlett többfokozatú támadásészlelés az Azure Sentinelben


> [!IMPORTANT]
> Az Azure Sentinel egyes Fusion-funkciói jelenleg nyilvános előzetes verzióban jelennek meg.
> Ezek a szolgáltatások szolgáltatásszint-szerződés nélkül vannak biztosítva, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)



A gépi tanuláson alapuló Fusion-technológia használatával az Azure Sentinel automatikusan észleli a többlépcsős támadásokat a rendellenes viselkedések és a kill-chain különböző szakaszaiban megfigyelt gyanús tevékenységek kombinálásával. Az Azure Sentinel ezután olyan incidenseket hoz létre, amelyeket egyébként nagyon nehéz lenne elkapni. Ezek az incidensek két vagy több riasztást vagy tevékenységet sújtanak. Ezek az incidensek szándékosan alacsony a térfogatúak, nagy a hűségük és súlyosak.

Az Ön környezetéhez testreszabva ez az észlelés nem csak csökkenti a hamis pozitív arányokat, hanem a korlátozott vagy hiányzó adatokkal rendelkező támadásokat is észlel.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Fejlett többfázisú támadásészlelés konfigurálása

Ez az észlelés alapértelmezés szerint engedélyezve van az Azure Sentinelben. Az állapot ellenőrzéséhez vagy letiltásához, például azért, mert alternatív megoldást használ a több riasztáson alapuló incidensek létrehozásához, kövesse az alábbi utasításokat:

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Navigálás az **Azure Sentinel** > **konfigurációs** > **elemzésre**

3. Válassza **az Aktív szabályok lehetőséget,** és keresse meg a Speciális **többszintű támadásészlelést** a **NAME** oszlopban. Ellenőrizze az **ÁLLAPOT** oszlopot, és ellenőrizze, hogy az észlelés engedélyezve van-e vagy le van tiltva.

4. Az állapot módosításához jelölje ki ezt a bejegyzést, és a **Speciális többszintű támadásészlelés** panelen válassza a **Szerkesztés**lehetőséget.

5. A **Szabálykészítő varázsló** panelen a rendszer automatikusan kiválasztja az állapotváltozást, ezért válassza a **Tovább: Véleményezés**lehetőséget, majd a **Mentés lehetőséget.** 

A szabálysablonok nem alkalmazhatók a speciális többszintű támadások észlelésére.

> [!NOTE]
> Az Azure Sentinel jelenleg 30 nap előzményadatokat használ a gépi tanulási rendszerek betanításához. Ezek az adatok mindig titkosítva vannak a Microsoft kulcsaival, ahogy áthaladnak a gépi tanulási folyamaton. A betanítási adatok azonban nem titkosítva vannak [az Ügyfél által felügyelt kulcsok (CMK)](customer-managed-keys.md) használatával, ha engedélyezte a CMK-t az Azure Sentinel-munkaterületen. A Fusion letiltásához keresse meg az **Azure Sentinel** \> **Configuration** \>Analytics ** \> Aktív szabályok \> speciális többfokozatú támadásészlelését,** és az **Állapot** oszlopban válassza a **Letiltás lehetőséget.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fusion segítségével Palo Alto Networks és a Microsoft Defender ATP

Ezek a forgatókönyvek a biztonsági elemzők által használt két alapvető naplót kombinálják: a Palo Alto Networks tűzfalnaplóit és a Microsoft Defender ATP végpontészlelési naplóit. Az alább felsorolt esetek mindegyikében a rendszer egy gyanús tevékenységet észlel a végponton, amely külső IP-címet tartalmaz, majd ezt a külső IP-címről a tűzfalra visszavezető rendellenes forgalom követi. A Palo Alto naplók, Az Azure Sentinel összpontosít [fenyegetés naplók](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs), és a forgalom gyanúsnak tekinthető, ha a fenyegetések engedélyezettek (gyanús adatok, fájlok, árvizek, csomagok, vizsgál, spyware, URL-ek, vírusok, sebezhetőségek, futótűz-vírusok, futótűz).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Hálózati kérelem a TOR anonimizálási szolgáltatásnak, amelyet a Palo Alto Networks tűzfala által megjelölt rendellenes forgalom követ.

Ebben a forgatókönyvben az Azure Sentinel először észleli a riasztást, hogy a Microsoft Defender Komplex veszélyforrások elleni védelem észlelt egy hálózati kérelmet a TOR anonimizálási szolgáltatás, amely rendellenes tevékenységhez vezet. Ezt a(z) {fióknév} fiók ban kezdeményezték{ sid} SID azonosítóval {time}. A kapcsolat kimenő IP-címe {IndividualIp}.
Ezt követően a Palo Alto Networks tűzfal a(z) {TimeGenerated} helyen szokatlan tevékenységet észlelt. Ez azt jelzi, hogy rosszindulatú forgalom lépett be a hálózatba A hálózati forgalom cél IP-címe: {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>A PowerShell gyanús hálózati kapcsolatot létesített, amelyet a Palo Alto Networks tűzfala által megjelölt rendellenes forgalom követett.

Ebben a forgatókönyvben az Azure Sentinel először észleli a riasztást, hogy a Microsoft Defender komplex veszélyforrások elleni védelem észlelte, hogy a PowerShell egy gyanús hálózati kapcsolat, amely a Palo Alto hálózati tűzfal által észlelt rendellenes tevékenységet eredményez. Ezt a(z) {fióknév} fiók kezdeményezte{ sid} SID azonosítóval a következő helyen: {time}. A kapcsolat kimenő IP-címe {IndividualIp}. Ezt követően a Palo Alto Networks tűzfal a(z) {TimeGenerated} helyen szokatlan tevékenységet észlelt. Ez azt jelzi, hogy rosszindulatú forgalom lépett be a hálózatba. A hálózati forgalom cél IP-címe: {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Kimenő kapcsolat az IP-hez, amelynek előzményei a jogosulatlan hozzáférési kísérletek, majd a Palo Alto Networks tűzfal anomáliaforgalommal

Ebben a forgatókönyvben az Azure Sentinel észlelegy riasztást, amely szerint a Microsoft Defender Komplex veszélyforrások elleni védelem kimenő kapcsolatot észlelt egy OLYAN IP-címhez, amelynek előzményei a nem engedélyezett hozzáférési kísérletek, amelyek a Palo Alto által észlelt rendellenes tevékenységészleléséhez vezetnek Hálózatok tűzfala. Ezt a(z) {fióknév} fiók kezdeményezte{ sid} SID azonosítóval a következő helyen: {time}. A kapcsolat kimenő IP-címe {IndividualIp}. Ezt követően a Palo Alto Networks tűzfal a(z) {TimeGenerated} helyen szokatlan tevékenységet észlelt. Ez azt jelzi, hogy rosszindulatú forgalom lépett be a hálózatba. A hálózati forgalom cél IP-címe: {DestinationIP}.

Ez a forgatókönyv jelenleg nyilvános előzetes verzióban érhető el.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fusion az Identity Protection és a Microsoft Cloud App Security használatával

A fejlett többfokozatú támadásészlelés használatával az Azure Sentinel a következő forgatókönyveket támogatja, amelyek az Azure Active Directory identity Protection és a Microsoft Cloud App Security anomáliaeseményeit kombinálják:

- [Lehetetlen utazás atipikus helyre, majd rendellenes Office 365-tevékenység](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Ismeretlen helyre jelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenységről](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Bejelentkezési tevékenység a felhasználótól kiszivárgott hitelesítő adatokkal, majd rendellenes Office 365-tevékenységgel](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Rendelkeznie kell az [Azure AD Identity Protection adatösszekötővel](connect-azure-ad-identity-protection.md) és a [Cloud App Security](connect-cloud-app-security.md) összekötők konfigurálva.

A következő leírásokban az Azure Sentinel az ezen az oldalon változóként zárójelben megjelenő adatok tényleges értékét jeleníti meg. Például a \< *fiók név* helyett a fiók tényleges megjelenítendő neve \<>, és a tényleges szám a *szám* helyett>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Lehetetlen utazás atipikus helyre, majd rendellenes Office 365-tevékenység

Hét lehetséges Azure Sentinel-incidens létezik, amelyek az Azure AD Identity Protection és a Microsoft Cloud App Security által létrehozott rendellenes Office 365-riasztások nem kívánt helyriasztásaihoz való lehetetlen utazást kombinálják:

- **Lehetetlen utazás az Office 365 postafiókjának kiszivárgásához vezető atipikus helyekre**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez a fiók neve \< *alapján,*> egy lehetetlen utazásról a *>* helyre, egy atipikus helyre, majd egy gyanús beérkező továbbítási szabály van beállítva a felhasználó beérkezett üzenetek mappájában.
    
    Ez azt jelezheti, hogy a fiók biztonsága sérül, és hogy a postaládát arra használják, hogy kiszivárogjanak a szervezetből származó információkból. A \<felhasználói *fiók neve*> létrehozott vagy frissített egy beérkező levéltovábbítási \<szabályt, amely az összes bejövő e-mailt továbbítja a külső *e-mail címre*>.

- **Lehetetlen utazás atipikus helyekre, amelyek gyanús felhőalkalmazás-felügyeleti tevékenységhez vezetnek**
    
    Ez a riasztás egy \< *bejelentkezési* esemény jelkép alapján> \<egy lehetetlen utazás *a helyre,*>, egy atipikus helyen.
    
    Ezután a \< *fiókfiók* nevét \<> egyetlen munkamenetben, *>* felügyeleti tevékenységek száma felett végrehajtani.

- **Lehetetlen utazás atipikus helyekre, ami tömeges fájltörléshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez a \<fiók *neve* alapján,> a *helyhez*>, egy atipikus helyre. 
    
    Ezután a \< *fiókfiók neve*> törölt \< *számú*> egyedi fájlokat egy munkamenetben.

- **Lehetetlen utazás atipikus helyeken vezető tömeges fájl letöltés**
    
    Ez a riasztás egy \< *bejelentkezési* esemény jelkép alapján> \<egy lehetetlen utazás *a helyre,*>, egy atipikus helyen. 
    
    Ezután a \< *fiókfiók nevét*> letölteni> egyedi fájlok \< *száma* egy munkamenetben.

- **Lehetetlen utazás az Office 365 megszemélyesítéséhez vezető atipikus helyekre**
    
    Ez a riasztás egy \< *bejelentkezési* esemény jelkép alapján> \<egy lehetetlen utazás *a helyre,*>, egy atipikus helyen. 
    
    Ezután a \< *fiókfiók neve*>\<szokatlan mennyiségű (tevékenységek*száma*>) végzett megszemélyesítési tevékenységek egyetlen munkamenetben.

- **Lehetetlen utazás atipikus helyekre, ami tömeges fájlmegosztáshoz vezet**
    
    Ez a riasztás egy \< *bejelentkezési* esemény jelkép alapján> \<egy lehetetlen utazás *a helyre,*>, egy atipikus helyen. 
    
    Ezután a \< *fiókfiók nevét*> megosztva \<> egyedi fájlok *egy* munkamenetben.

- **Lehetetlen utazás atipikus helyekre, amelyek zsarolóprogramokhoz vezetnek a felhőalkalmazásban**
    
    Ez a riasztás egy \< *bejelentkezési* esemény jelkép alapján> \<egy lehetetlen utazás *a helyre,*>, egy atipikus helyen. 
    
    Ezután a \< *fiókfiók* neve \<> feltöltött> fájlok *számát,* és összesen \< *több*> fájlt törölt. 
    
    Ez a tevékenységminta egy lehetséges zsarolóvírus-támadásra utal.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Ismeretlen helyre jelentkezési tevékenység, amelyet rendellenes Office 365-tevékenység követ

Hét lehetséges Azure Sentinel-incidens létezik, amelyek egyesítik a bejelentkezési tevékenységet az Azure AD Identity Protection ismeretlen helyriasztásai és a Microsoft Cloud App Security által létrehozott rendellenes Office 365-riasztások esetében.

- **Bejelentkezési esemény ismeretlen helyről, amely az Exchange Online postaláda kiszivárgásához vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> \< *egy* ismeretlen helyről,>, majd egy gyanús beérkező továbbítási szabály van beállítva a felhasználó beérkezett üzenetek mappájában.
    
    Ez azt jelezheti, hogy a fiók biztonsága sérül, és hogy a postaládát arra használják, hogy kiszivárogjanak a szervezetből származó információkból. A \<felhasználói *fiók neve*> létrehozott vagy frissített egy beérkező levéltovábbítási \<szabályt, amely az összes bejövő e-mailt továbbítja a külső *e-mail címre*>. 

- **Bejelentkezési esemény ismeretlen helyről, amely gyanús felhőalkalmazás-felügyeleti tevékenységhez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről. 
    
    Ezután a \< *fiókfiók nevét*> egyetlen munkamenetben> felügyeleti tevékenységek \< *száma* alapján hajtják végre.

- **Bejelentkezési esemény ismeretlen helyről, amely tömeges fájltörléshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről. 
    
    Ezután a \< *fiókfiók neve*> törölt \< *számú*> egyedi fájlokat egy munkamenetben.

- **Bejelentkezési esemény ismeretlen helyről, amely tömeges fájlletöltéshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről. 
    
    Ezután a \< *fiókfiók nevét*> letölteni> egyedi fájlok \< *száma* egy munkamenetben.

- **Bejelentkezési esemény ismeretlen helyről, amely az Office 365 megszemélyesítéséhez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről.
    
    Ezután a \< *fiókfiók nevét* \<egy munkamenetben> különböző fiókok *száma* miatt> megszemélyesíteni.

- **Bejelentkezési esemény ismeretlen helyről, amely tömeges fájlmegosztáshoz vezet**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről. 
    
    Ezután a \< *fiókfiók nevét*> megosztva \<> egyedi fájlok *egy* munkamenetben.

- **Bejelentkezési esemény ismeretlen helyről, amely zsarolóprogramokhoz vezet a felhőalkalmazásban**
    
    Ez a riasztás egy bejelentkezési eseményt jelez \< *a fiók neve* alapján,> a> \< *helyről,* egy ismeretlen helyről. 
    
    Ezután a \< *fiókfiók* neve \<> feltöltött> fájlok *számát,* és összesen \< *több*> fájlt törölt. 
    
    Ez a tevékenységminta egy lehetséges zsarolóvírus-támadásra utal.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység fertőzött eszközről, majd rendellenes Office 365-tevékenység

Hét lehetséges Azure Sentinel-incidens kombinálható az Azure AD Identity Protection fertőzött eszközriasztásaiból származó bejelentkezési tevékenységgel és a Microsoft Cloud App Security által létrehozott rendellenes Office 365-ös riasztásokkal:

- **Bejelentkezési esemény az Office 365 postafiókjának kiszivárgásához vezető fertőzött eszközről**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *fióknév alapján,*> egy potenciálisan rosszindulatú programokkal fertőzött eszközről, majd egy gyanús beérkező továbbítási szabályt állított be a felhasználó beérkezett üzenetek mappájában.
    
    Ez azt jelezheti, hogy a fiók biztonsága sérül, és hogy a postaládát arra használják, hogy kiszivárogjanak a szervezetből származó információkból. A \<felhasználói *fiók neve*> létrehozott vagy frissített egy beérkező levéltovábbítási \<szabályt, amely az összes bejövő e-mailt továbbítja a külső *e-mail címre*>. 

- **Bejelentkezési esemény egy fertőzött eszközről, amely gyanús felhőalkalmazás-felügyeleti tevékenységhez vezet**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware.
    
    Ezután a \< *fiókfiók nevét*> egyetlen munkamenetben> felügyeleti tevékenységek \< *száma* alapján hajtják végre.

- **Bejelentkezési esemény egy fertőzött eszközről, amely tömeges fájltörléshez vezet**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware. 
    
    Ezután a \< *fiókfiók neve*> törölt \< *számú*> egyedi fájlokat egy munkamenetben.

- **Bejelentkezési esemény egy fertőzött eszközről, amely tömeges fájlletöltéshez vezet**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware. 
    
    Ezután a \< *fiókfiók nevét*> letölteni> egyedi fájlok \< *száma* egy munkamenetben.

- **Bejelentkezési esemény az Office 365 megszemélyesítéséhez vezető fertőzött eszközről**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware. 
    
    Ezután a \< *fiókfiók nevét* \<egy munkamenetben> különböző fiókok *száma* miatt> megszemélyesíteni.

- **Bejelentkezési esemény tömeges fájlmegosztáshoz vezető fertőzött eszközről**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware. 
    
    Ezután a \< *fiókfiók nevét*> megosztva \<> egyedi fájlok *egy* munkamenetben.

- **Bejelentkezési esemény egy fertőzött eszközről, amely zsarolóprogramokhoz vezet a felhőalkalmazásban**
    
    Ez a riasztás egy bejelentkezési esemény \<jele *a fiók neve*> egy eszköz potenciálisan fertőzött malware. 
    
    Ezután a \< *fiókfiók* neve \<> feltöltött> fájlok *számát,* és összesen \< *több*> fájlt törölt. 
    
    Ez a tevékenységminta egy lehetséges zsarolóvírus-támadásra utal.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység névtelen IP-címről, majd rendellenes Office 365-tevékenységről

Hét lehetséges Azure Sentinel-incidens kombinálható az Azure AD Identity Protection névtelen IP-címriasztásaiból származó bejelentkezési tevékenységgel és a Microsoft Cloud App Security által létrehozott rendellenes Office 365-ös riasztásokkal:

- **Bejelentkezési esemény az Office 365 postafiókjának kiszivárgásához vezető névtelen IP-címről**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *a fiók név alapján,*> egy névtelen proxy \< *IP-cím>,* majd egy gyanús beérkező továbbítási szabály van beállítva a felhasználó beérkezett üzenetek mappájában.
    
    Ez azt jelezheti, hogy a fiók biztonsága sérül, és hogy a postaládát arra használják, hogy kiszivárogjanak a szervezetből származó információkból. A \<felhasználói *fiók neve*> létrehozott vagy frissített egy beérkező levéltovábbítási \<szabályt, amely az összes bejövő e-mailt továbbítja a külső *e-mail címre*>. 

- **Bejelentkezési esemény egy névtelen IP-címről, amely gyanús felhőalkalmazás-felügyeleti tevékenységhez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók nevét*> egyetlen munkamenetben> felügyeleti tevékenységek \< *száma* alapján hajtják végre.

- **Bejelentkezési esemény egy névtelen IP-címről, amely tömeges fájltörléshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók neve*> törölt \< *számú*> egyedi fájlokat egy munkamenetben.

- **Bejelentkezési esemény egy névtelen IP-címről, amely tömeges fájlletöltéshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók nevét*> letölteni> egyedi fájlok \< *száma* egy munkamenetben.

- **Bejelentkezési esemény egy névtelen IP-címről, amely office 365-i megszemélyesítéshez vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók nevét* \<egy munkamenetben> különböző fiókok *száma* miatt> megszemélyesíteni.

- **Bejelentkezési esemény egy névtelen IP-címről, amely tömeges fájlmegosztáshoz vezet**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók nevét*> megosztva \<> egyedi fájlok *egy* munkamenetben.

- **Bejelentkezési esemény névtelen IP-címről zsarolóprogramokba a felhőalapú alkalmazásban**
    
    Ez a riasztás egy bejelentkezési eseményt \<jelez *egy névtelen* proxy \< *IP-címéről*>>. 
    
    Ezután a \< *fiókfiók* neve \<> feltöltött> fájlok *számát,* és összesen \< *több*> fájlt törölt. 
    
    Ez a tevékenységminta egy lehetséges zsarolóvírus-támadásra utal.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Bejelentkezési tevékenység a felhasználótól kiszivárgott hitelesítő adatokkal, majd rendellenes Office 365-tevékenységgel

Hét lehetséges Azure Sentinel-incidens létezik, amelyek egyesítik a felhasználó bejelentkezési tevékenységét az Azure AD Identity Protection kiszivárgott hitelesítő adataival és a Microsoft Cloud App Security által létrehozott rendellenes Office 365-ös riasztásoksal:

- **Bejelentkezési esemény az Office 365 postaláda kiszivárgásához vezető, kiszivárgott hitelesítő adatokkal rendelkező felhasználótól**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> kiszivárgott hitelesítő adatokat használt, majd egy gyanús beérkezett üzenetek továbbítási szabály a felhasználó beérkezett üzenetek mappájában. 
    
    Ez azt jelezheti, hogy a fiók biztonsága sérül, és hogy a postaládát arra használják, hogy kiszivárogjanak a szervezetből származó információkból. A \<felhasználói *fiók neve*> létrehozott vagy frissített egy beérkező levéltovábbítási \<szabályt, amely az összes bejövő e-mailt továbbítja a külső *e-mail címre*>. 

- **Bejelentkezési esemény a kiszivárgott hitelesítő adatokkal rendelkező felhasználótól, amely gyanús felhőalkalmazás-felügyeleti tevékenységhez vezet**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat.
    
    Ezután a \< *fiókfiók nevét*> egyetlen munkamenetben> felügyeleti tevékenységek \< *száma* alapján hajtják végre.

- **Bejelentkezési esemény a felhasználótól kiszivárgott hitelesítő adatokkal, amelyek tömeges fájltörléshez vezetnek**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat.
    
    Ezután a \< *fiókfiók neve*> törölt \< *számú*> egyedi fájlokat egy munkamenetben.

- **Bejelentkezési esemény a felhasználótól a kiszivárgott hitelesítő adatokkal, ami tömeges fájlletöltéshez vezet**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat.
    
    Ezután a \< *fiókfiók nevét*> letölteni> egyedi fájlok \< *száma* egy munkamenetben.

- **Bejelentkezési esemény az Office 365 megszemélyesítéséhez vezető, kiszivárgott hitelesítő adatokkal rendelkező felhasználótól**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat. 
    
    Ezután a \< *fiókfiók nevét* \<egy munkamenetben> különböző fiókok *száma* miatt> megszemélyesíteni.

- **Bejelentkezési esemény a felhasználótól kiszivárgott hitelesítő adatokkal, amelyek tömeges fájlmegosztáshoz vezetnek**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat.
    
    Ezután a \< *fiókfiók nevét*> megosztva \<> egyedi fájlok *egy* munkamenetben.

- **Bejelentkezési esemény a kiszivárgott hitelesítő adatokkal rendelkező felhasználótól a zsarolóprogramokhoz a felhőalkalmazásban**
    
    Ez a riasztás azt jelzi, hogy \<a bejelentkezési esemény *fióknév szerint*> használt kiszivárgott hitelesítő adatokat. 
    
    Ezután a \< *fiókfiók* neve \<> feltöltött> fájlok *számát,* és összesen \< *több*> fájlt törölt. 
    
    Ez a tevékenységminta egy lehetséges zsarolóvírus-támadásra utal.

## <a name="next-steps"></a>További lépések

Most, hogy többet megtudhat a fejlett többfokozatú támadások észleléséről, az alábbi rövid útmutatóból megtudhatja, hogyan kaphat betekintést az adatokba és a potenciális fenyegetésekbe: [Ismerkedjen meg az Azure Sentinel használatával.](quickstart-get-visibility.md)

Ha készen áll az Ön számára létrehozott incidensek kivizsgálására, tekintse meg a következő oktatóanyagot: [Az Azure Sentinel incidensei vizsgálata.](tutorial-investigate-cases.md)

