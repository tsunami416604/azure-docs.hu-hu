---
title: Javaslatok szervizelése Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan javíthatja a javaslatokat a Azure Security Centerban az erőforrások védelme és a biztonsági szabályzatok betartása érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9beb617ed8626b1fda1c9db98d626ca70ee01755
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86042917"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Helyreállítási javaslatok az Azure Security Centerben

A javaslatok segítséget nyújtanak az erőforrások jobb biztonságossá tételéhez. A javaslatot a javaslatban megadott szervizelési lépések követésével hajthatja végre.

## <a name="remediation-steps"></a>Szervizelési lépések<a name="remediation-steps"></a>

Az összes javaslat áttekintése után döntse el, hogy melyiket kell elsőként kijavítani. Javasoljuk, hogy a [biztonságos pontszám hatása](security-center-recommendations.md#monitor-recommendations) alapján rangsorolja, hogy mi az első.

1. A listában kattintson a javaslatra.

1. Kövesse a **szervizelési lépések** szakaszban található utasításokat. Minden javaslathoz saját utasítások vannak megadva. Az alábbi képernyőképen az alkalmazások konfigurálásához szükséges szervizelési lépések láthatók a HTTPS protokollon keresztüli adatforgalom engedélyezéséhez.

    ![Javaslat részletei](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="quick-fix-remediation"></a>Gyors javítás szervizelése<a name="one-click"></a>

A gyors javítás lehetővé teszi, hogy gyorsan javítsa a több erőforrásra vonatkozó javaslatait. Csak konkrét javaslatok esetében érhető el. A gyors javítás leegyszerűsíti a szervizelést, és lehetővé teszi a biztonsági pontszám gyors növelését, a környezet biztonságának javítását.

A gyors javítási szervizelés megvalósítása:

1. A **gyors javítást** tartalmazó javaslatok listájáról címkére, kattintson a javaslatra.

    [![Válassza a gyors javítás lehetőséget.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. A nem kifogástalan **erőforrások** lapon válassza ki azokat az erőforrásokat, amelyekhez javaslatot kíván végrehajtani, majd kattintson a **szervizelés**elemre.

    > [!NOTE]
    > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.

1. A megerősítés mezőben olvassa el a szervizelés részleteit és a következményeket.

    ![Gyors javítás](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > A következmények a javítás után megnyíló **erőforrások szervizelése** ablak szürke mezőjében jelennek **meg.** Felsorolja, hogy milyen változások történnek a gyors javítás szervizelésének folytatásakor.

1. Szükség esetén szúrja be a megfelelő paramétereket, majd hagyja jóvá a szervizelést.

    > [!NOTE]
    > A szervizelés befejezése után több percet is igénybe vehet, hogy az erőforrások megjelenjenek az **egészséges erőforrások** lapon. A Szervizelési műveletek megtekintéséhez tekintse meg a [tevékenység naplóját](#activity-log).

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szervizelési napló gyors javítása a műveletnapló esetében<a name="activity-log"></a>

A Szervizelési művelet sablon-telepítési vagy REST-javító API-hívást használ a konfiguráció alkalmazásához az erőforráson. Ezeket a műveleteket az [Azure-tevékenység naplójában](../azure-resource-manager/management/view-activity-logs.md)naplózza a rendszer.


## <a name="recommendations-with-quick-fix-remediation"></a>Javaslatok a gyors javítási szervizeléssel

|Ajánlás|Ennek következménye|
|---|---|
|A SQL Database naplózását engedélyezni kell|Ezzel a művelettel engedélyezheti az SQL-naplózást ezeken a kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**: <ul><li>A kiválasztott SQL Database minden egyes régiója számára létrejön egy Storage-fiók a naplók mentéséhez, és az adott régióban lévő összes kiszolgáló megosztja őket.</li><li>A megfelelő naplózás biztosításához ne törölje vagy nevezze át az erőforráscsoportot vagy a Storage-fiókokat.</li></ul>|
|A speciális adatbiztonságot engedélyezni kell a felügyelt SQL-példányon|Ezzel a művelettel engedélyezheti az SQL Advanced Security (hirdetések) szolgáltatást a kiválasztott SQL felügyelt példányokon. <br>**Megjegyzés**: <ul><li>A kiválasztott SQL felügyelt példány minden egyes régiója és erőforráscsoport esetében a vizsgálati eredmények mentésére szolgáló Storage-fiók lesz létrehozva és megosztva az adott régió összes példányával.</li><li> A HIRDETÉSEKET a felügyelt SQL-példányon $15-kor számoljuk el.</li></ul>|
|A biztonsági rések felmérését engedélyezni kell a felügyelt SQL-példányon|Ez a művelet lehetővé teszi az SQL sebezhetőségi felmérést a kiválasztott SQL felügyelt példányon. <br>**Megjegyzés**:<ul><li>Az SQL biztonsági rések felmérése az SQL Advanced Security (ADS) csomag részét képezi. Ha a HIRDETÉSEKET már nem engedélyezték, a felügyelt példányon automatikusan engedélyezve lesz.</li><li>A kiválasztott SQL felügyelt példány minden régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva az adott régió összes példányával.</li><li>A hirdetések díja SQL Database $15.</li></ul>||
|A speciális adatbiztonságot engedélyezni kell a SQL Database|Ez a művelet engedélyezi a speciális adatbiztonságot (HIRDETÉSEKET) a kiválasztott kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**:<ul><li>A kiválasztott SQL Database minden egyes régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva az adott régióban lévő összes kiszolgáló között. <</li><li>A hirdetések díja SQL Database $15.</li></ul>||
|A sebezhetőségi felmérést engedélyezni kell a SQL Database|Ez a művelet engedélyezi az SQL sebezhetőségi felmérést a kiválasztott kiszolgálókon és azok adatbázisain. <br>**Megjegyzés**:<ul><li>Az SQL biztonsági rések felmérése az SQL Advanced Security (ADS) csomag részét képezi. Ha a hirdetések már nincsenek engedélyezve, automatikusan engedélyezve lesz a SQL Database.</li><li>A kiválasztott SQL Database minden egyes régiója és erőforráscsoport esetében a vizsgálati eredmények tárolására szolgáló Storage-fiók lesz létrehozva és megosztva a régió összes példányával.</li><li>A hirdetések díja SQL Database $15.</li></ul>||
|A SQL Database transzparens adattitkosításának engedélyezve kell lennie|Ez a művelet engedélyezi SQL Database transzparens adattitkosítás (TDE) használatát a kiválasztott adatbázisokon. <br>**Megjegyzés**: alapértelmezés szerint a rendszer a szolgáltatás által felügyelt TDE kulcsokat használja.
|Engedélyezni kell a tárfiókokba történő biztonságos átvitelt|Ez a művelet frissíti a Storage-fiók biztonságát, hogy csak biztonságos kapcsolatok esetén engedélyezze a kérelmeket. (HTTPS). <br>**Megjegyzés**:<ul><li>A HTTP protokollt használó kérelmek el lesznek utasítva.</li><li>Az Azure Files szolgáltatás használatakor a titkosítás nélküli csatlakozás meghiúsul, beleértve az SMB 2,1, az SMB 3,0 és a titkosítás nélkül, valamint a Linux SMB-ügyfél néhány változatát is. További információk.</li></ul>|
|A webalkalmazás csak HTTPS protokollon keresztül érhető el|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy olyan HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy olyan HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|Az API-alkalmazás csak HTTPS protokollon keresztül érhető el|Ez a művelet átirányítja a HTTP-ről a HTTPS-re irányuló összes forgalmat a kiválasztott erőforrásokra. <br>**Megjegyzés**:<ul><li>Egy olyan HTTPS-végpont, amely nem rendelkezik SSL-tanúsítvánnyal, "adatvédelmi hiba" üzenet jelenik meg a böngészőben. Az egyéni tartománnyal rendelkező felhasználóknak ellenőriznie kell, hogy beállították-e az SSL-tanúsítványt.</li><li>Győződjön meg arról, hogy a csomag-és webalkalmazási tűzfal védi az App Service-t, engedélyezze a HTTPS-munkamenetek továbbítását.</li></ul>|
|A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni függvényalkalmazás|Ez a művelet letiltja a távoli hibakeresést.|
|A távoli hibakeresést ki kell kapcsolni az API-alkalmazáshoz|Ez a művelet letiltja a távoli hibakeresést.|
|A CORS nem engedheti meg, hogy minden erőforrás hozzáférjen a webalkalmazáshoz|Ez a művelet letiltja a más tartományokat a webalkalmazáshoz való hozzáféréshez. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe:" Allowed Origins "|
|A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését|Ez a művelet blokkolja a többi tartományt a Function alkalmazás eléréséhez. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe:" Allowed Origins "|
|A CORS nem teszi lehetővé minden erőforrás számára az API-alkalmazás elérését|Ez a művelet blokkolja más tartományok hozzáférését az API-alkalmazáshoz. Adott tartományok engedélyezéséhez adja meg azokat az engedélyezett Origins mezőben (vesszővel elválasztva). <br>**Megjegyzés**: Ha a mezőt üresen hagyja, a rendszer letiltja az összes kereszthivatkozási hívást. "params mező címe:" Allowed Origins "|
|A figyelési ügynököt engedélyezni kell a virtuális gépeken|Ez a művelet egy figyelési ügynököt telepít a kiválasztott virtuális gépekre. Válasszon ki egy munkaterületet, amelyről jelentést szeretne készíteni az ügynöknek.<ul><li>Ha a frissítési szabályzat automatikusra van beállítva, akkor az új meglévő példányokon fog települni.</li><li>Ha a frissítési szabályzat manuálisra van beállítva, és az ügynököt meglévő példányokra szeretné telepíteni, jelölje be a jelölőnégyzetet. [További információ](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|A Key Vault lévő diagnosztikai naplókat engedélyezni kell|Ez a művelet engedélyezi a diagnosztikai naplókat a kulcstartók számára. A rendszer menti a diagnosztikai naplókat és mérőszámokat a kiválasztott munkaterületen.|
|A Service Bus szolgáltatásban engedélyezni kell a diagnosztikai naplókat.|Ez a művelet engedélyezi a diagnosztikai naplókat a Service Bus szolgáltatásban. A rendszer menti a diagnosztikai naplókat és mérőszámokat a kiválasztott munkaterületen.|

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan javíthatja az ajánlásokat a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő témaköröket:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
* A [Azure Security Center biztonsági állapotának monitorozása](security-center-monitoring.md) – megismerheti az Azure-erőforrások állapotának figyelését.
