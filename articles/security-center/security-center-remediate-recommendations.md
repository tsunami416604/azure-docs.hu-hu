---
title: Javaslatok szervizelése Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum ismerteti, hogyan javíthatja a javaslatait a Azure Security Centerban, hogy segítsen az Azure-erőforrások védelmében és a biztonsági szabályzatoknak való megfelelésben.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2019
ms.author: v-mohabe
ms.openlocfilehash: a32e344ffe33f411bae85763ae3b919040c1109b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575611"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Javaslatok szervizelése Azure Security Center

A javaslatok segítséget nyújtanak az erőforrások jobb biztonságossá tételéhez.  A javaslatot a javaslatban megadott szervizelési lépések követésével hajthatja végre. 

## Szervizelési lépések<a name="remediation-steps"></a>

Az összes javaslat áttekintése után döntse el, hogy melyiket kell elsőként kijavítani. Javasoljuk, hogy a [biztonságos pontszám hatása](security-center-recommendations.md#monitor-recommendations) alapján rangsorolja, hogy mi az első.

1. A listában kattintson a javaslatra.
1. Kövesse a **szervizelési lépések** szakaszban található utasításokat. Minden javaslathoz saját utasítások vannak megadva. Az alábbi szervizelési lépések azt mutatják be, hogyan konfigurálhatja az alkalmazásokat, hogy csak a HTTPS protokollon keresztül engedélyezze a forgalmat.

    ![Javaslat részletei](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## Egy kattintással javíthatja a javítási szervizelést (előzetes verzió)<a name="one-click"></a>

Az egykattintásos javítás lehetővé teszi, hogy egy-egy kattintással több erőforrásra vonatkozó ajánlást javítsa ki. Ez a lehetőség csak bizonyos javaslatok esetében érhető el. Az egykattintásos javítás egyszerűsíti a szervizelést, és lehetővé teszi a biztonságos pontszám gyors javítását és a környezet biztonságának növelését.

Egy kattintásos szervizelés megvalósítása:

1. Az **1 kattintásos javítás** címkével rendelkező javaslatok listájáról kattintson a javaslatra.  

   ![Válasszon egy kattintásos javítást](./media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)

2. A nem kifogástalan **erőforrások** lapon válassza ki azokat az erőforrásokat, amelyekhez javaslatot kíván végrehajtani, majd kattintson a **szervizelés**elemre. 

    > [!NOTE]
    > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.

3. A megerősítés mezőben olvassa el a szervizelés részleteit és a következményeket. 

   ![Egy kattintással történő javítás](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > A következmények a javítás után megnyíló **erőforrások szervizelése** ablak szürke mezőjében jelennek meg. Kilistázzak, hogy milyen változások történnek az 1 kattintásos szervizeléssel való továbblépés során.

4. Szükség esetén szúrja be a megfelelő paramétereket, majd hagyja jóvá a szervizelést.

    > [!NOTE]
    > A szervizelés befejezése után több percet is igénybe vehet, hogy az erőforrások megjelenjenek az **egészséges erőforrások** lapon. A Szervizelési műveletek megtekintéséhez tekintse meg a [tevékenység naplóját](#activity-log).

5. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## Egy kattintással végzett szervizelési naplózás a tevékenység naplójában<a name="activity-log"></a>

A Szervizelési művelet sablon-telepítési vagy REST-javító API-hívást használ a konfiguráció alkalmazásához az erőforráson. Ezeket a műveleteket az [Azure-tevékenység naplójában](../azure-resource-manager/resource-group-audit.md)naplózza a rendszer.


## <a name="recommendations-with-one-click-remediation"></a>Javaslatok egyetlen kattintással szervizeléssel

|Ajánlás|Ennek következménye|
|---|---|
|Az SQL-kiszolgálók naplózását engedélyezni kell|Ezzel a művelettel engedélyezheti az SQL-naplózást ezeken a kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**: <ul><li>A kiválasztott SQL-kiszolgálók minden egyes régiója számára létrejön egy Storage-fiók a naplók mentéséhez, és az adott régióban lévő összes kiszolgáló megosztja őket.</li><li>A megfelelő naplózás biztosításához ne törölje vagy nevezze át az erőforráscsoportot vagy a Storage-fiókokat.</li></ul>|
|A speciális adatbiztonságot engedélyezni kell az SQL felügyelt példányain|Ezzel a művelettel engedélyezheti az SQL Advanced Security (hirdetések) szolgáltatást a kiválasztott SQL felügyelt példányokon. <br>**Megjegyzés**: <ul><li>A kiválasztott SQL felügyelt példányok minden régiója és erőforráscsoport esetében a vizsgálati eredmények mentésére szolgáló Storage-fiók lesz létrehozva és megosztva az adott régió összes példányával.</li><li> A HIRDETÉSEKET a felügyelt SQL-példányon $15-kor számoljuk el.</li></ul>|
|A sebezhetőségi felmérést engedélyezni kell az SQL felügyelt példányain.|Ez a művelet engedélyezi az SQL sebezhetőségi felmérést a kiválasztott SQL felügyelt példányokon. <br>**Megjegyzés**:<ul><li>Az SQL biztonsági rések felmérése az SQL Advanced Security (ADS) csomag részét képezi. Ha a HIRDETÉSEKET már nem engedélyezték, a felügyelt példányon automatikusan engedélyezve lesz.</li><li>A kiválasztott SQL felügyelt példányok minden régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva az adott régió összes példányával.</li><li>A HIRDETÉSEKET a $15-es számú SQL Serveren számoljuk el.</li></ul>||
|A speciális adatbiztonságot engedélyezni kell az SQL-kiszolgálókon|Ez a művelet engedélyezi a speciális adatbiztonságot (HIRDETÉSEKET) a kiválasztott kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**:<ul><li>A kiválasztott SQL-kiszolgálók minden régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva az adott régióban lévő összes kiszolgáló között. <</li><li>A HIRDETÉSEKET a $15-es számú SQL Serveren számoljuk el.</li></ul>||
|A sebezhetőségi felmérést engedélyezni kell az SQL-kiszolgálókon|Ez a művelet engedélyezi az SQL sebezhetőségi felmérést a kiválasztott kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**:<ul><li>Az SQL biztonsági rések felmérése az SQL Advanced Security (ADS) csomag részét képezi. Ha a HIRDETÉSEKET már nem engedélyezték, automatikusan engedélyezve lesz az SQL Serveren.</li><li>A kiválasztott SQL-kiszolgálók minden régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva az adott régióban lévő összes példányban.</li><li>A HIRDETÉSEKET a $15-es számú SQL Serveren számoljuk el.</li></ul>||
|Az SQL-adatbázisokon engedélyezni kell az transzparens adattitkosítást|Ez a művelet engedélyezi SQL Database transzparens adattitkosítás (TDE) használatát a kiválasztott adatbázisokon. <br>**Megjegyzés**: Alapértelmezés szerint a rendszer a szolgáltatás által felügyelt TDE kulcsokat használja. 
|A Storage-fiókoknak való biztonságos átvitelt engedélyezni kell|Ez a művelet frissíti a Storage-fiók biztonságát, hogy csak biztonságos kapcsolatok esetén engedélyezze a kérelmeket. (HTTPS). <br>**Megjegyzés**:<ul><li>A HTTP protokollt használó kérelmek el lesznek utasítva.</li><li>Ha az Azure Files szolgáltatást használja, a titkosítás nélküli csatlakozás sikertelen lesz, beleértve az SMB 2,1, az SMB 3,0 titkosítás nélkül, valamint a Linux SMB-ügyfél néhány változatát.  Részletek.</li></ul>|
|Webes alkalmazás csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Ezért az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|Alkalmazás függvény csak elérhetőnek kell lennie HTTPS-kapcsolaton keresztül|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Ezért az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|Az API-alkalmazás csak HTTPS protokollon keresztül érhető el|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Ezért az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|Távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni függvényalkalmazás|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz|Ez a művelet letiltja a más tartományokat a webalkalmazáshoz való hozzáféréshez. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe: "Engedélyezett eredetek"|
|A CORS nem teszi lehetővé a Függvényalkalmazás eléréséhez minden erőforrás|Ez a művelet blokkolja a többi tartományt a Function alkalmazás eléréséhez. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe: "Engedélyezett eredetek"|
|A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését|Ez a művelet blokkolja más tartományok hozzáférését az API-alkalmazáshoz. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe: "Engedélyezett eredetek"|
|A figyelési ügynököt engedélyezni kell a virtuális gépeken|Ez a művelet egy figyelési ügynököt telepít a kiválasztott virtuális gépekre. Válasszon ki egy munkaterületet, amelyről jelentést szeretne készíteni az ügynöknek.<ul><li>Ha a frissítési szabályzat automatikusra van beállítva, akkor az új meglévő példányokon fog települni.</li><li>Ha a frissítési szabályzat manuálisra van beállítva, és az ügynököt meglévő példányokra szeretné telepíteni, jelölje be a jelölőnégyzetet.  [További információ](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|A Key Vault lévő diagnosztikai naplókat engedélyezni kell|Ez a művelet engedélyezi a diagnosztikai naplókat a kulcstartók számára. A rendszer menti a diagnosztikai naplókat és mérőszámokat a kiválasztott munkaterületen.|
|A Service Bus szolgáltatásban engedélyezni kell a diagnosztikai naplókat.|Ez a művelet engedélyezi a diagnosztikai naplókat a Service Bus szolgáltatásban. A rendszer menti a diagnosztikai naplókat és mérőszámokat a kiválasztott munkaterületen.|

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan javíthatja az ajánlásokat a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő témaköröket:

* [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Ismerje meg, hogyan konfigurálhatja az Azure-előfizetések és-erőforráscsoportok biztonsági szabályzatait.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
